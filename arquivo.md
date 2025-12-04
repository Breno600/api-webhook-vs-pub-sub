# ansible/predeploy_per_machine.yml
#
# Tarefas executadas para CADA máquina no pré-deploy.
# Variáveis de contexto (vêm do loop do play principal):
#   - machine_name
#   - repo_root
#   - status_dir
#   - deployment_ref

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
    host_base_dir: "/opt/SoftwareExpress/sitef"
    host_pkg_dir: "/opt/SoftwareExpress/sitef/package/linux"
    host_scripts_dir: "/opt/SoftwareExpress/sitef/scripts"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

# ----------------------------------------------------------
# Status inicial (queued)
# ----------------------------------------------------------
- name: "Criar status inicial (queued) para {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}"
      }

# ----------------------------------------------------------
# Criar diretórios NA MÁQUINA ALVO
# ----------------------------------------------------------
- name: "Garantir diretórios base no host {{ machine_cfg.host }}"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ host_base_dir }}"
    - "{{ host_pkg_dir }}"
    - "{{ host_scripts_dir }}"
    - "{{ host_scripts_dir }}/{{ package_cfg.script }}"
  delegate_to: "{{ machine_cfg.host }}"

# ----------------------------------------------------------
# Copiar RPM(s) para a máquina alvo
# package_cfg.components:
#   - packages/linux/sitef-core-0.0.1-0.x86_64.rpm
# ----------------------------------------------------------
- name: "Baixar componentes do package {{ machine_cfg.package }} para {{ machine_name }}"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/{{ item }}"
    dest: "{{ host_pkg_dir }}/"
    mode: "0644"
  loop: "{{ package_cfg.components }}"
  delegate_to: "{{ machine_cfg.host }}"

# ----------------------------------------------------------
# Copiar scripts do package para a máquina alvo
# scripts/<script>/...
# ----------------------------------------------------------
- name: "Copiar scripts do package {{ machine_cfg.package }} para {{ machine_name }}"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ package_cfg.script }}/"
    dest: "{{ host_scripts_dir }}/{{ package_cfg.script }}/"
    mode: "0755"
  delegate_to: "{{ machine_cfg.host }}"

# ----------------------------------------------------------
# Executar o init_parallel.sh NA MÁQUINA ALVO
# Usando env_vars definidos no machine/<nome>.yml
# ----------------------------------------------------------
- name: "Executar script de pré-deploy (init_parallel.sh) em {{ machine_name }}"
  become: true
  ansible.builtin.shell: |
    cd "{{ host_scripts_dir }}/{{ package_cfg.script }}"
    ./init_parallel.sh
  delegate_to: "{{ machine_cfg.host }}"
  environment: "{{ machine_cfg.env_vars | default({}) }}"
  register: predeploy_result
  ignore_errors: true

# ----------------------------------------------------------
# Atualizar status para success ou failed
# ----------------------------------------------------------
- name: "Atualizar status para success de {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "stdout": {{ predeploy_result.stdout | to_json }},
        "stderr": {{ predeploy_result.stderr | to_json }}
      }
  when: predeploy_result is succeeded

- name: "Atualizar status para failed de {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "stdout": {{ predeploy_result.stdout | to_json }},
        "stderr": {{ predeploy_result.stderr | to_json }}
      }
  when: predeploy_result is failed