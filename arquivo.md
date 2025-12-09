#ansible/ansible.cfg
[defaults]
host_key_checking = False
stdout_callback = default
timeout = 30
forks = 10
retry_files_enabled = False
inventory = localhost,

[ssh_connection]
pipelining = True
ssh_args = -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -o ControlMaster=auto -o ControlPersist=60s

#ansible/predeploy_from_execution.yml
# PIPELINE 1 - PREDEPLOY
# - Lê execution/<arquivo>.yml
# - Para cada máquina:
#   * carrega machine + package
#   * exporta env_vars (merge)
#   * prepara área /opt/SoftwareExpress/sitef-pipeline/deploy
#   * limpa scripts
#   * copia package.yml
#   * baixa componentes do Nexus
#   * copia scripts do repo
#   * executa init_parallel.sh
#   * gera status JSON
#   * envia JSON + LOG para Harness File Store

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

    # Nexus (ideal: vir do Harness Secrets)
    nexus_base_url: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user: "{{ nexus_user | default(omit) }}"
    nexus_password: "{{ nexus_password | default(omit) }}"

    # File Store base
    filestore_env: "{{ filestore_env | default('dev') }}"  # dev|cat|prd
    filestore_base_dir: "{{ filestore_base_dir | default(filestore_env ~ '/' ~ deployment_ref) }}"

  tasks:
    - name: "Mostrar variáveis de entrada"
      ansible.builtin.debug:
        msg:
          - "execution_file_name = {{ execution_file_name }}"
          - "deployment_ref      = {{ deployment_ref }}"
          - "repo_root           = {{ repo_root }}"
          - "status_dir          = {{ status_dir }}"
          - "nexus_base_url      = {{ nexus_base_url }}"
          - "filestore_env       = {{ filestore_env }}"
          - "filestore_base_dir  = {{ filestore_base_dir }}"

    - name: "Criar diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir }}"
        state: directory
        mode: "0755"

    - name: "Carregar arquivo de execução"
      ansible.builtin.include_vars:
        file: "{{ repo_root }}/{{ execution_file_name }}"
        name: execution_cfg

    - name: "Falhar se não tiver máquinas no arquivo de execução"
      ansible.builtin.fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: execution_cfg.machines is not defined or execution_cfg.machines | length == 0

    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name
      vars:
        deployment_ref: "{{ deployment_ref }}"
        repo_root: "{{ repo_root }}"
        status_dir: "{{ status_dir }}"
        nexus_base_url: "{{ nexus_base_url }}"
        nexus_user: "{{ nexus_user | default('') }}"
        nexus_password: "{{ nexus_password | default('') }}"
        filestore_env: "{{ filestore_env }}"
        filestore_base_dir: "{{ filestore_base_dir }}"

# ansible/predeploy_per_machine.yml
# Tarefas por máquina - PREDEPLOY

# -------------------------------
# Paths e arquivos
# -------------------------------
- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"

    # Área padrão do pipeline no host
    host_pipeline_base: "/opt/SoftwareExpress/sitef-pipeline"
    host_deploy_base: "/opt/SoftwareExpress/sitef-pipeline/deploy"
    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    host_deploy_scripts_pkg: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"
    host_deploy_pkg_root: "/opt/SoftwareExpress/sitef-pipeline/deploy"

# -------------------------------
# Validar existência de machine
# -------------------------------
- name: "Validar arquivo da máquina"
  ansible.builtin.stat:
    path: "{{ machine_file }}"
  register: machine_file_stat

- name: "Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: "Arquivo da máquina não encontrado: {{ machine_file }}"
  when: not machine_file_stat.stat.exists

# -------------------------------
# Load vars
# -------------------------------
- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg.package is defined
      - machine_cfg.package | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -------------------------------
# Validações fortes (evita item=None)
# -------------------------------
- name: "Validar script do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg.script is defined
      - package_cfg.script | length > 0
    fail_msg: "package_cfg.script não definido no pacote {{ machine_cfg.package }}"

- name: "Validar components do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg.components is defined
      - package_cfg.components | length > 0
    fail_msg: "package_cfg.components vazio/ausente no pacote {{ machine_cfg.package }}"

# -------------------------------
# SSH defaults
# -------------------------------
- name: "Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default('ec2-user') }}"

- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{
        machine_cfg.ssh_common_args
          | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null')
      }}

- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    target_ssh_common_args: "-o ProxyJump={{ bastion_user | default('ec2-user') }}@{{ bastion_host }} -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null"
  when:
    - (machine_cfg.ssh_common_args | default('') | length) == 0
    - bastion_host is defined

# -------------------------------
# Registrar host dinâmico
# -------------------------------
- name: "Registrar host dinâmico para {{ machine_name }}"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"

# -------------------------------
# Merge de env_vars (package + machine)
# machine sobrescreve package se houver conflito
# -------------------------------
- name: "Montar env final"
  ansible.builtin.set_fact:
    effective_env: "{{ (package_cfg.env_vars | default({})) | combine(machine_cfg.env_vars | default({}), recursive=True) }}"

# -------------------------------
# Status inicial
# -------------------------------
- name: "Criar status inicial (queued)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "stage": "pre-deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }

# -------------------------------
# Garantir diretórios no host
# -------------------------------
- name: "Garantir base do pipeline no host"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ host_deploy_base }}"
    - "{{ host_deploy_scripts }}"
    - "{{ host_deploy_scripts_pkg }}"
  delegate_to: "{{ machine_name }}"

# -------------------------------
# Limpar scripts
# -------------------------------
- name: "Limpar pasta de scripts do predeploy"
  become: true
  ansible.builtin.shell: |
    rm -rf "{{ host_deploy_scripts }}"/*
  args:
    warn: false
  delegate_to: "{{ machine_name }}"

# -------------------------------
# Copiar package YAML do repo para a área do host
# -------------------------------
- name: "Copiar package.yml para scripts/package/"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    dest: "{{ host_deploy_scripts_pkg }}/{{ machine_cfg.package }}.yml"
    mode: "0644"
  delegate_to: "{{ machine_name }}"

# -------------------------------
# Baixar componentes do Nexus
# (corrigido para nunca iterar None)
# -------------------------------
- name: "Montar lista de URLs finais sem credenciais (debug)"
  ansible.builtin.set_fact:
    components_eff: "{{ package_cfg.components | list }}"
    components_urls: "{{ package_cfg.components | map('regex_replace', '^(.*)$', nexus_base_url ~ '/\\1') | list }}"

- name: "DEBUG - componentes do pacote"
  ansible.builtin.debug:
    msg:
      - "package={{ machine_cfg.package }}"
      - "components={{ components_eff }}"
      - "base_url={{ nexus_base_url }}"

- name: "Baixar componentes do Nexus para a área do pipeline"
  become: true
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ host_deploy_pkg_root }}/{{ item }}"
    mode: "0644"
    force: true
    url_username: "{{ nexus_user if (nexus_user | length) > 0 else omit }}"
    url_password: "{{ nexus_password if (nexus_password | length) > 0 else omit }}"
  loop: "{{ components_eff }}"
  delegate_to: "{{ machine_name }}"
  register: nexus_dl
  no_log: true

# -------------------------------
# Copiar scripts do repo para área do host
# -------------------------------
- name: "Copiar scripts do package para deploy/scripts/"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ package_cfg.script }}/"
    dest: "{{ host_deploy_scripts }}/"
    mode: "0755"
  delegate_to: "{{ machine_name }}"

# -------------------------------
# Validar init_parallel.sh
# -------------------------------
- name: "Verificar init_parallel.sh"
  become: true
  ansible.builtin.stat:
    path: "{{ host_deploy_scripts }}/init_parallel.sh"
  delegate_to: "{{ machine_name }}"
  register: parallel_stat

- name: "Falhar se init_parallel.sh não existir"
  ansible.builtin.fail:
    msg: "init_parallel.sh não encontrado em {{ host_deploy_scripts }}"
  when: not parallel_stat.stat.exists

# -------------------------------
# Executar init_parallel.sh
# -------------------------------
- name: "Executar init_parallel.sh"
  become: true
  ansible.builtin.shell: |
    set -e
    cd "{{ host_deploy_scripts }}"
    /bin/bash -x ./init_parallel.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ effective_env }}"
  register: predeploy_result
  ignore_errors: true

# -------------------------------
# Coletar log do script (se existir)
# -------------------------------
- name: "Ler parallel.txt do host"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_deploy_scripts }}/parallel.txt" 2>/dev/null || echo "parallel.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: parallel_log
  changed_when: false

# -------------------------------
# Atualizar status final
# -------------------------------
- name: "Atualizar status para success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "stage": "pre-deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ predeploy_result.rc | default(0) }},
        "stdout": {{ predeploy_result.stdout | default('') | to_json }},
        "stderr": {{ predeploy_result.stderr | default('') | to_json }},
        "script_log": {{ parallel_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: predeploy_result.rc == 0

- name: "Atualizar status para failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "stage": "pre-deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ predeploy_result.rc | default(1) }},
        "stdout": {{ predeploy_result.stdout | default('') | to_json }},
        "stderr": {{ predeploy_result.stderr | default('') | to_json }},
        "script_log": {{ parallel_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: predeploy_result.rc != 0

# -------------------------------
# Upload para Harness File Store
# -------------------------------
- name: "Upload status JSON para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "{{ status_file }}" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.json"
  register: upload_status
  changed_when: false
  failed_when: false

- name: "Upload log do predeploy para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "-" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log" \
      <<'EOF'
    ---- pipeline pre deploy ----
    {{ predeploy_result.stdout | default('') }}
    {{ predeploy_result.stderr | default('') }}
    ---- parallel.txt ----
    {{ parallel_log.stdout | default('') }}
    ---- pipeline pre deploy ----
    EOF
  args:
    executable: /bin/bash
  register: upload_log
  changed_when: false
  failed_when: false

# -------------------------------
# Falhar job se predeploy falhar
# -------------------------------
- name: "Falhar pipeline se init_parallel.sh retornou erro"
  ansible.builtin.fail:
    msg: |
      PREDEPLOY falhou em {{ machine_name }} (host {{ machine_cfg.host }})
      RC={{ predeploy_result.rc }}
      Veja status: {{ status_file }}
  when: predeploy_result.rc != 0

# ansible/deploy_from_status.yml
# PIPELINE 2 - DEPLOY
# - Recebe machine_name (ou lista externa via loop no script)
# - Reusa área /deploy/scripts preparada no predeploy
# - Executa init_deploy.sh
# - Atualiza status
# - Upload JSON + LOG no File Store

- name: "Deploy por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    machine_name: "{{ machine_name | default('') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"
    filestore_env: "{{ filestore_env | default('dev') }}"
    filestore_base_dir: "{{ filestore_base_dir | default(filestore_env ~ '/' ~ deployment_ref) }}"

  tasks:
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - machine_name is defined
          - machine_name | length > 0
        fail_msg: "machine_name não informado"

    - name: "Incluir deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name }}"
        repo_root: "{{ repo_root }}"
        status_dir: "{{ status_dir }}"
        filestore_env: "{{ filestore_env }}"
        filestore_base_dir: "{{ filestore_base_dir }}"

#ansible/deploy_per_machine.yml
# Tarefas por máquina - DEPLOY

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/deploy-{{ machine_name }}.json"

    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"

- name: "Carregar config da máquina"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

- name: "Definir usuário alvo padrão"
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
    effective_env: "{{ (package_cfg.env_vars | default({})) | combine(machine_cfg.env_vars | default({}), recursive=True) }}"

- name: "Criar status inicial (queued)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }

- name: "Verificar init_deploy.sh"
  become: true
  ansible.builtin.stat:
    path: "{{ host_deploy_scripts }}/init_deploy.sh"
  delegate_to: "{{ machine_name }}"
  register: deploy_script_stat

- name: "Falhar se init_deploy.sh não existir"
  ansible.builtin.fail:
    msg: "init_deploy.sh não encontrado em {{ host_deploy_scripts }}. Rode o predeploy antes."
  when: not deploy_script_stat.stat.exists

- name: "Executar init_deploy.sh"
  become: true
  ansible.builtin.shell: |
    set -e
    cd "{{ host_deploy_scripts }}"
    /bin/bash -x ./init_deploy.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ effective_env }}"
  register: deploy_result
  ignore_errors: true

- name: "Ler deploy.txt do host"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_deploy_scripts }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: deploy_log
  changed_when: false

- name: "Atualizar status para success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ deploy_result.rc | default(0) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "script_log": {{ deploy_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: deploy_result.rc == 0

- name: "Atualizar status para failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ deploy_result.rc | default(1) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "script_log": {{ deploy_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: deploy_result.rc != 0

- name: "Upload status JSON para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "{{ status_file }}" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.json"
  changed_when: false
  failed_when: false

- name: "Upload log do deploy para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "-" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log" \
      <<'EOF'
    ---- pipeline deploy ----
    {{ deploy_result.stdout | default('') }}
    {{ deploy_result.stderr | default('') }}
    ---- deploy.txt ----
    {{ deploy_log.stdout | default('') }}
    ---- pipeline deploy ----
    EOF
  args:
    executable: /bin/bash
  changed_when: false
  failed_when: false

- name: "Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ machine_name }} (host {{ machine_cfg.host }})"
  when: deploy_result.rc != 0

#ansible/rollback_from_status.yml
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
    filestore_env: "{{ filestore_env | default('dev') }}"
    filestore_base_dir: "{{ filestore_base_dir | default(filestore_env ~ '/' ~ deployment_ref) }}"

  tasks:
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - machine_name | length > 0
        fail_msg: "machine_name não informado"

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


#ansible/rollback_per_machine.yml
# Tarefas por máquina - ROLLBACK

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/rollback-{{ machine_name }}.json"

    host_rb_base: "/opt/SoftwareExpress/sitef-pipeline/rollback"
    host_rb_scripts: "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts"
    host_rb_scripts_pkg: "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package"
    host_rb_pkg_root: "/opt/SoftwareExpress/sitef-pipeline/rollback"

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

- name: "Garantir diretórios de rollback no host"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ host_rb_base }}"
    - "{{ host_rb_scripts }}"
    - "{{ host_rb_scripts_pkg }}"
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

- name: "Baixar componentes do Nexus para rollback"
  become: true
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ host_rb_pkg_root }}/{{ item }}"
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

9) harness/pre-deploy-pipeline-script.sh
#!/bin/bash
set -euo pipefail

GIT_TOKEN="${GIT_TOKEN:-}"
GIT_TAG="${GIT_TAG:-}"
EXECUTION_FILE_NAME="${EXECUTION_FILE_NAME:-execution/machine_list_dev.yml}"
GIT_BRANCH="${GIT_BRANCH:-develop}"

GIT_USER_NAME="${GIT_USER_NAME:-harness-bot}"
GIT_USER_EMAIL="${GIT_USER_EMAIL:-harness-bot@fiserv.com}"

NEXUS_BASE_URL="${NEXUS_BASE_URL:-https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev}"
NEXUS_USER="${NEXUS_USER:-}"
NEXUS_PASSWORD="${NEXUS_PASSWORD:-}"

FILESTORE_ENV="${FILESTORE_ENV:-dev}"

if [[ -z "${GIT_TOKEN}" || -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TOKEN ou GIT_TAG não informado"
  exit 1
fi

export ANSIBLE_HOST_KEY_CHECKING=False

REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone --branch "${GIT_BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"
git fetch --tags

echo "== PIPELINE PREDEPLOY =="
echo "TAG   : ${GIT_TAG}"
echo "EXEC  : ${EXECUTION_FILE_NAME}"
echo "BRANCH: ${GIT_BRANCH}"
echo

cd ansible

ansible-playbook predeploy_from_execution.yml \
  -e "execution_file_name=${EXECUTION_FILE_NAME}" \
  -e "deployment_ref=${GIT_TAG}" \
  -e "nexus_base_url=${NEXUS_BASE_URL}" \
  -e "nexus_user=${NEXUS_USER}" \
  -e "nexus_password=${NEXUS_PASSWORD}" \
  -e "filestore_env=${FILESTORE_ENV}" \
  --forks 10

10) harness/deploy-pipeline.sh
#!/usr/bin/env bash
set -euo pipefail

BRANCH="${BRANCH:-develop}"
GIT_TAG="${GIT_TAG:-}"
MACHINES="${MACHINES:-${MACHINE:-}}"
GIT_TOKEN="${GIT_TOKEN:-}"
FILESTORE_ENV="${FILESTORE_ENV:-dev}"

if [[ -z "${GIT_TOKEN}" || -z "${GIT_TAG}" || -z "${MACHINES}" ]]; then
  echo "ERRO: GIT_TOKEN, GIT_TAG ou MACHINES não informado"
  exit 1
fi

export ANSIBLE_HOST_KEY_CHECKING=False

REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

# Normaliza lista:
MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  MACHINES_CLEAN+="${m} "
done

WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

echo "== DEPLOY PIPELINE =="
echo "TAG      : ${GIT_TAG}"
echo "BRANCH   : ${BRANCH}"
echo "MACHINES : ${MACHINES_CLEAN}"
echo

for m in ${MACHINES_CLEAN}; do
  echo "===================================================="
  echo "== DEPLOY -> ${m}"
  echo "===================================================="

  ansible-playbook \
    ansible/deploy_from_status.yml \
    -e "machine_name=${m}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "filestore_env=${FILESTORE_ENV}"
done

11) harness/rollback-pipeline.sh
#!/usr/bin/env bash
set -euo pipefail

BRANCH="${BRANCH:-develop}"
GIT_TAG="${GIT_TAG:-}"
MACHINES="${MACHINES:-${MACHINE:-}}"
GIT_TOKEN="${GIT_TOKEN:-}"
NEXUS_BASE_URL="${NEXUS_BASE_URL:-https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev}"
NEXUS_USER="${NEXUS_USER:-}"
NEXUS_PASSWORD="${NEXUS_PASSWORD:-}"
FILESTORE_ENV="${FILESTORE_ENV:-dev}"

if [[ -z "${GIT_TOKEN}" || -z "${GIT_TAG}" || -z "${MACHINES}" ]]; then
  echo "ERRO: GIT_TOKEN, GIT_TAG ou MACHINES não informado"
  exit 1
fi

export ANSIBLE_HOST_KEY_CHECKING=False

REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

# Normaliza lista:
MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  MACHINES_CLEAN+="${m} "
done

WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

echo "== ROLLBACK PIPELINE =="
echo "TAG      : ${GIT_TAG}"
echo "BRANCH   : ${BRANCH}"
echo "MACHINES : ${MACHINES_CLEAN}"
echo

for m in ${MACHINES_CLEAN}; do
  echo "===================================================="
  echo "== ROLLBACK -> ${m}"
  echo "===================================================="

  ansible-playbook \
    ansible/rollback_from_status.yml \
    -e "machine_name=${m}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "nexus_base_url=${NEXUS_BASE_URL}" \
    -e "nexus_user=${NEXUS_USER}" \
    -e "nexus_password=${NEXUS_PASSWORD}" \
    -e "filestore_env=${FILESTORE_ENV}"
done
