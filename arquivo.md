# ansible/predeploy_per_machine.yml
#
# Tarefas executadas para CADA máquina no pré-deploy.
#
# Variáveis esperadas (vêm do include em predeploy_from_execution.yml):
#   - machine_name   (item do loop)
#   - repo_root
#   - status_dir
#   - deployment_ref
#   - nexus_base_url

- name: "Definir caminhos locais para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
    remote_base_dir: "/opt/SoftwareExpress/sitef"
    remote_package_dir: "/opt/SoftwareExpress/sitef/package/linux"
    remote_scripts_dir: "/opt/SoftwareExpress/sitef/scripts"

# -------------------------------------------------------------------
# 1) Carrega configs da máquina e do package
# -------------------------------------------------------------------
- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Definir arquivo de package para {{ machine_name }}"
  ansible.builtin.set_fact:
    package_file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"

- name: "Carregar config do package {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ package_file }}"
    name: package_cfg

# -------------------------------------------------------------------
# 2) Cria JSON inicial (queued) na pasta de status
# -------------------------------------------------------------------
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

# -------------------------------------------------------------------
# 3) Garante diretórios na MÁQUINA alvo
# -------------------------------------------------------------------
- name: "Garantir diretórios base no host {{ machine_cfg.host }}"
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ remote_base_dir }}"
    - "{{ remote_package_dir }}"
    - "{{ remote_scripts_dir }}"
  delegate_to: "{{ machine_cfg.host }}"
  become: true

# -------------------------------------------------------------------
# 4) Baixa componentes do package no host alvo (Nexus → /opt/SoftwareExpress/sitef/package/linux)
# -------------------------------------------------------------------
- name: "Baixar componentes do package {{ machine_cfg.package }} para {{ machine_name }}"
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ remote_package_dir }}/{{ item | basename }}"
    mode: "0644"
    force: true
  loop: "{{ package_cfg.components }}"
  delegate_to: "{{ machine_cfg.host }}"
  become: true

# -------------------------------------------------------------------
# 5) Copia diretório de scripts do repositório para o host alvo
#    Ex.: scripts/deploy-sitef-0.0.2/* → /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.2/
# -------------------------------------------------------------------
- name: "Copiar scripts {{ package_cfg.script }} para {{ machine_name }}"
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ package_cfg.script }}/"
    dest: "{{ remote_scripts_dir }}/{{ package_cfg.script }}/"
    mode: "0755"
  delegate_to: "{{ machine_cfg.host }}"
  become: true

# -------------------------------------------------------------------
# 6) Executa o init_parallel.sh na máquina, com env_vars do machine
#    + variável PACKAGE_DIR apontando para a pasta dos RPMs
# -------------------------------------------------------------------
- name: "Executar script de pré-deploy (init_parallel.sh) em {{ machine_name }}"
  ansible.builtin.shell: |
    cd "{{ remote_scripts_dir }}/{{ package_cfg.script }}"
    ./init_parallel.sh
  args:
    executable: /bin/bash
  delegate_to: "{{ machine_cfg.host }}"
  become: true
  environment: "{{ (machine_cfg.env_vars | default({})) | combine({'PACKAGE_DIR': remote_package_dir}) }}"
  register: predeploy_result

# -------------------------------------------------------------------
# 7) Atualiza JSON de status (success / failed) com stdout / stderr
# -------------------------------------------------------------------
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
  when: predeploy_result.rc == 0

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
  when: predeploy_result.rc != 0
  failed_when: predeploy_result.rc != 0