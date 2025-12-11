# ansible/rollback_from_status.yml

# ansible/rollback_from_status.yml
# PIPELINE 3 - ROLLBACK

- name: "Rollback por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    machine_name: "{{ machine_name | default('') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

    nexus_base_url: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user: "{{ nexus_user | default(omit) }}"
    nexus_password: "{{ nexus_password | default(omit) }}"

    # evita loop recursivo: pegamos do ambiente e caímos pra "dev"
    filestore_env: "{{ lookup('ansible.builtin.env', 'FILESTORE_ENV') | default('dev', true) }}"
    filestore_base_dir: "{{ filestore_base_dir | default(filestore_env ~ '/' ~ deployment_ref) }}"

  tasks:
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - machine_name | length > 0
        fail_msg: "machine_name não informado"

    - name: "Garantir diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir }}"
        state: directory
        mode: "0755"

    - name: "Mostrar variáveis de entrada e resolvidas (rollback)"
      ansible.builtin.debug:
        msg:
          - "machine_name                = {{ machine_name }}"
          - "deployment_ref             = {{ deployment_ref }}"
          - "repo_root                  = {{ repo_root }}"
          - "status_dir                 = {{ status_dir }}"
          - "nexus_base_url             = {{ nexus_base_url }}"
          - "filestore_env              = {{ filestore_env }}"
          - "filestore_base_dir         = {{ filestore_base_dir }}"

    - name: "Incluir rollback por máquina"
      ansible.builtin.include_tasks: rollback_per_machine.yml
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name }}"
        repo_root: "{{ repo_root }}"
        status_dir: "{{ status_dir }}"
        nexus_base_url: "{{ nexus_base_url }}"
        nexus_user: "{{ nexus_user | default('') }}"
        nexus_password: "{{ nexus_password | default('') }}"
        filestore_env: "{{ filestore_env }}"
        filestore_base_dir: "{{ filestore_base_dir }}"

---

# ansible/rollback_per_machine.yml
# Tarefas por máquina - ROLLBACK

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/rollback-{{ machine_name }}.json"

    host_rb_base: "/opt/SoftwareExpress/sitef-pipeline/rollback"
    host_rb_scripts: "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts"
    host_rb_scripts_pkg: "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package"
    host_rb_components_root: "/opt/SoftwareExpress/sitef-pipeline/rollback/components"

- name: "Carregar config da máquina"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Validar rollback package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg.rollback is defined
      - machine_cfg.rollback | length > 0
    fail_msg: "machine_cfg.rollback não definido em {{ machine_file }}"

- name: "Carregar config do pacote de rollback {{ machine_cfg.rollback }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.rollback }}.yml"
    name: rollback_pkg_cfg

- name: "Validar components de rollback"
  ansible.builtin.assert:
    that:
      - rollback_pkg_cfg.components is defined
      - rollback_pkg_cfg.components | length > 0
    fail_msg: "rollback_pkg_cfg.components vazio/ausente no pacote {{ machine_cfg.rollback }}"

- name: "Definir usuário alvo"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default('ec2-user') }}"

- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{
        machine_cfg.ssh_common_args
          | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null')
      }}

- name: "Registrar host dinâmico"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"

- name: "Montar env final"
  ansible.builtin.set_fact:
    effective_env: "{{ (rollback_pkg_cfg.env_vars | default({})) | combine(machine_cfg.env_vars | default({}), recursive=True) }}"

- name: "Criar status inicial (queued)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback }}",
        "status": "queued",
        "stage": "rollback",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }

- name: "Garantir diretórios base de rollback no host"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ host_rb_base }}"
    - "{{ host_rb_scripts }}"
    - "{{ host_rb_scripts_pkg }}"
    - "{{ host_rb_components_root }}"
  delegate_to: "{{ machine_name }}"

- name: "Limpar pasta de scripts do rollback"
  become: true
  ansible.builtin.shell: |
    rm -rf "{{ host_rb_scripts }}"/*
  args:
    warn: false
  delegate_to: "{{ machine_name }}"

- name: "Copiar package rollback yml para scripts/package/"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/package/{{ machine_cfg.rollback }}.yml"
    dest: "{{ host_rb_scripts_pkg }}/{{ machine_cfg.rollback }}.yml"
    mode: "0644"
  delegate_to: "{{ machine_name }}"

# aqui garantimos diretórios intermediários para os components,
# ex: components/package/linux/
- name: "Garantir diretórios dos components no host"
  become: true
  ansible.builtin.file:
    path: "{{ host_rb_components_root }}/{{ item | dirname }}"
    state: directory
    mode: "0755"
  loop: "{{ rollback_pkg_cfg.components | map('dirname') | list | unique }}"
  delegate_to: "{{ machine_name }}"

- name: "Baixar componentes do Nexus para rollback"
  become: true
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ host_rb_components_root }}/{{ item }}"
    mode: "0644"
    force: true
    url_username: "{{ nexus_user if (nexus_user | length) > 0 else omit }}"
    url_password: "{{ nexus_password if (nexus_password | length) > 0 else omit }}"
  loop: "{{ rollback_pkg_cfg.components | list }}"
  delegate_to: "{{ machine_name }}"
  no_log: true

- name: "Copiar scripts do rollback"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ rollback_pkg_cfg.script }}/"
    dest: "{{ host_rb_scripts }}/"
    mode: "0755"
  delegate_to: "{{ machine_name }}"

- name: "Verificar init_rollback.sh"
  become: true
  ansible.builtin.stat:
    path: "{{ host_rb_scripts }}/init_rollback.sh"
  delegate_to: "{{ machine_name }}"
  register: rb_stat

- name: "Falhar se init_rollback.sh não existir"
  ansible.builtin.fail:
    msg: "init_rollback.sh não encontrado em {{ host_rb_scripts }}"
  when: not rb_stat.stat.exists

- name: "Executar init_rollback.sh"
  become: true
  ansible.builtin.shell: |
    set -e
    cd "{{ host_rb_scripts }}"
    /bin/bash -x ./init_rollback.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ effective_env }}"
  register: rb_result
  ignore_errors: true

- name: "Ler rollback.txt do host"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_rb_scripts }}/rollback.txt" 2>/dev/null || echo "rollback.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: rb_log
  changed_when: false

- name: "Atualizar status final - success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback }}",
        "status": "success",
        "stage": "rollback",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ rb_result.rc | default(0) }},
        "stdout": {{ rb_result.stdout | default('') | to_json }},
        "stderr": {{ rb_result.stderr | default('') | to_json }},
        "script_log": {{ rb_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: rb_result.rc == 0

- name: "Atualizar status final - failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback }}",
        "status": "failed",
        "stage": "rollback",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ rb_result.rc | default(1) }},
        "stdout": {{ rb_result.stdout | default('') | to_json }},
        "stderr": {{ rb_result.stderr | default('') | to_json }},
        "script_log": {{ rb_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: rb_result.rc != 0

- name: "Upload status JSON para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "{{ status_file }}" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.json"
  changed_when: false
  failed_when: false

- name: "Upload log do rollback para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "-" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log" \
      <<'EOF'
    ---- pipeline rollback ----
    {{ rb_result.stdout | default('') }}
    {{ rb_result.stderr | default('') }}
    ---- rollback.txt ----
    {{ rb_log.stdout | default('') }}
    ---- pipeline rollback ----
    EOF
  args:
    executable: /bin/bash
  changed_when: false
  failed_when: false

- name: "Falhar pipeline se rollback falhar"
  ansible.builtin.fail:
    msg: "ROLLBACK falhou em {{ machine_name }} (host {{ machine_cfg.host }})"
  when: rb_result.rc != 0
