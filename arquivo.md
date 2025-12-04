# ansible/predeploy_per_machine.yml
# ...
# Variáveis de contexto:
# - machine_name
# - repo_root
# - status_dir
# - deployment_ref

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
    base_dir: "/opt/SoftwareExpress/sitef"
    packages_dir: "/opt/SoftwareExpress/sitef/package/linux"
    scripts_dir: "/opt/SoftwareExpress/sitef/scripts/{{ package_cfg.script }}"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

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

# ========== NOVO: diretórios e download do RPM ==========

- name: "Garantir diretórios base no host {{ machine_cfg.host }}"
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ base_dir }}"
    - "{{ base_dir }}/package"
    - "{{ packages_dir }}"
    - "{{ base_dir }}/scripts"
    - "{{ scripts_dir }}"
  delegate_to: "{{ machine_cfg.host }}"

- name: "Copiar scripts do package para o host {{ machine_name }}"
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ package_cfg.script }}/"
    dest: "{{ scripts_dir }}/"
    mode: "0755"
  delegate_to: "{{ machine_cfg.host }}"

- name: "Baixar componentes do package {{ machine_cfg.package }} para {{ machine_name }}"
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ packages_dir }}/{{ item | basename }}"
    mode: "0644"
    force: yes
    validate_certs: false
  loop: "{{ package_cfg.components }}"
  delegate_to: "{{ machine_cfg.host }}"

# ========== EXECUÇÃO DO init_parallel.sh ==========

- name: "Executar script de pré-deploy (init_parallel.sh) em {{ machine_name }}"
  ansible.builtin.shell: |
    set -e
    cd "{{ scripts_dir }}"

    # Exporta env_vars configuradas na máquina
    {% for item in (machine_cfg.env_vars | default({}) | dict2items) %}
    export {{ item.key }}="{{ item.value }}"
    {% endfor %}

    ./init_parallel.sh
  args:
    executable: /bin/bash
  register: predeploy_result
  delegate_to: "{{ machine_cfg.host }}"

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