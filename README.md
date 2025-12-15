---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina)
# - Cria (se não existir) pasta raiz -> env -> deployment_ref
# - Faz UPSERT do status.json e do log da máquina
# - Aplica tags (status_tag_value + extra_tags + tags fixas)
#
# ENTRADAS (vars esperadas):
#   current_machine: "sitef-01"
#   deployment_ref: "DEV000000007"
#   filestore_env: "dev"
#   filestore_base_dir: "dev/DEV000000007"   (apenas lógico; aqui usamos a hierarquia env/deployment_ref)
#   machine_status_file: "/path/status.json" (arquivo local no controller)
#   log_content: "...."                      (string)
#   status_tag_value: "dev000000007:deploy:ok"
#   extra_tags: ["dev000000007:rollback:pending"]  (opcional)
#
# VARS OPCIONAIS (recomendado):
#   harness_account_id
#   harness_org_id
#   harness_project_id
#   harness_filestore_root_identifier (default: "Root" ou um folder seu)
#
# SEGREDO (NÃO NO REPO):
#   HARNESS_X_API_KEY (env var)
# =====================================================================================

- name: "Harness | Validar vars mínimas"
  ansible.builtin.assert:
    that:
      - current_machine is defined
      - deployment_ref is defined
      - filestore_env is defined
      - machine_status_file is defined
      - log_content is defined
      - status_tag_value is defined
    fail_msg: "Faltam vars obrigatórias para harness_filestore_upload.yml"

- name: "Harness | Definir defaults"
  ansible.builtin.set_fact:
    harness_account_id_resolved: "{{ harness_account_id | default('fgDto6qoTT6ctfZS9eWbEw') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('Fiserv') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('sitef') }}"
    harness_filestore_root_identifier_resolved: "{{ harness_filestore_root_identifier | default('Root') }}"

    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"
    deployment_ref_lower: "{{ deployment_ref | lower }}"
    env_lower: "{{ filestore_env | lower }}"

- name: "Harness | Ler token do ambiente (HARNESS_X_API_KEY)"
  ansible.builtin.set_fact:
    harness_x_api_key: "{{ lookup('ansible.builtin.env', 'HARNESS_X_API_KEY') | default('', true) }}"
  no_log: true

- name: "Harness | Falhar se token não está no ambiente"
  ansible.builtin.fail:
    msg: "HARNESS_X_API_KEY não definido no ambiente. Exporte no step do Harness antes de rodar o Ansible."
  when: (harness_x_api_key | length) == 0

- name: "Harness | Montar tags (JSON)"
  ansible.builtin.set_fact:
    harness_tags: >-
      {{
        (
          [
            {"key":"env","value": env_lower},
            {"key":"deployment","value": deployment_ref_lower},
            {"key":"machine","value": (current_machine | lower)},
            {"key":"tag","value": status_tag_value}
          ]
          + (
              (extra_tags | default([]))
              | map('regex_replace', '^(.*)$', '{"key":"tag","value":"\\1"}')
              | map('from_json')
              | list
            )
        )
      }}

# -----------------------------------------------------------------------------
# 1) Garantir folder ENV (filestore_env) embaixo da raiz
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (create se não existir)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_lower }}" \
      -F "tags={{ harness_tags | to_json }}"
  args:
    executable: /bin/bash
  register: hfs_env_create_rc
  changed_when: false
  failed_when: false
  no_log: true

# -----------------------------------------------------------------------------
# 2) Garantir folder DEPLOYMENT_REF embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder DEPLOYMENT_REF (create se não existir)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ deployment_ref_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_lower }}" \
      -F "identifier={{ deployment_ref_lower }}" \
      -F "tags={{ harness_tags | to_json }}"
  args:
    executable: /bin/bash
  register: hfs_ref_create_rc
  changed_when: false
  failed_when: false
  no_log: true

# -----------------------------------------------------------------------------
# 3) Upload/Upsert STATUS JSON
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do status JSON"
  ansible.builtin.set_fact:
    hfs_status_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.json"
    hfs_status_identifier: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-json"

- name: "Harness | Tentar CREATE status JSON"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ deployment_ref_lower }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@{{ machine_status_file }}"
  args:
    executable: /bin/bash
  register: hfs_status_create_http
  changed_when: false
  failed_when: false
  no_log: true

# Se já existe, fazemos UPDATE (o endpoint pode variar por versão; aqui uso PUT no /file-store/{identifier})
- name: "Harness | UPDATE status JSON (se create não retornou 2xx)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ deployment_ref_lower }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@{{ machine_status_file }}"
  args:
    executable: /bin/bash
  when: (hfs_status_create_http.stdout | default('')) is not match('^2..$')
  changed_when: false
  failed_when: false
  no_log: true

# -----------------------------------------------------------------------------
# 4) Upload/Upsert LOG
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do LOG"
  ansible.builtin.set_fact:
    hfs_log_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.log"
    hfs_log_identifier: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-log"

- name: "Harness | Criar arquivo temporário do log no controller"
  ansible.builtin.copy:
    dest: "/tmp/{{ hfs_log_name }}"
    mode: "0600"
    content: "{{ log_content }}"

- name: "Harness | Tentar CREATE log"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_log_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_log_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ deployment_ref_lower }}" \
      -F "identifier={{ hfs_log_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@/tmp/{{ hfs_log_name }}"
  args:
    executable: /bin/bash
  register: hfs_log_create_http
  changed_when: false
  failed_when: false
  no_log: true

- name: "Harness | UPDATE log (se create não retornou 2xx)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_log_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_log_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ deployment_ref_lower }}" \
      -F "identifier={{ hfs_log_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@/tmp/{{ hfs_log_name }}"
  args:
    executable: /bin/bash
  when: (hfs_log_create_http.stdout | default('')) is not match('^2..$')
  changed_when: false
  failed_when: false
  no_log: true

- name: "Harness | Limpar arquivo temporário do log"
  ansible.builtin.file:
    path: "/tmp/{{ hfs_log_name }}"
    state: absent
  changed_when: false
  failed_when: false
****



---------------

---
# =====================================================================================
# DEPLOY POR MÁQUINA (com log + File Store)
# =====================================================================================

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machines/{{ machine_name }}.yml"

    # status por máquina (igual seu padrão de status_dir/<machine>/status.json)
    machine_status_dir: "{{ status_dir }}/{{ machine_name }}"
    machine_status_file: "{{ status_dir }}/{{ machine_name }}/status.json"

    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    host_deploy_logs_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/logs"

- name: "Carregar config da máquina"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/packages/{{ machine_cfg.package }}.yml"
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

- name: "Montar env final (package + machine + extras)"
  ansible.builtin.set_fact:
    effective_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': machine_name,
            'SITEF_HOST': machine_cfg.host | default(machine_cfg.ip | default('')),
            'DEPLOYMENT_REF': deployment_ref | default(''),
            'PACKAGE_NAME': machine_cfg.package | default(''),
            'ROLLBACK_PACKAGE': machine_cfg.rollback | default('')
          }, recursive=True)
      }}

- name: "Criar diretório local de status"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Calcular caminhos lógicos do File Store (deploy)"
  ansible.builtin.set_fact:
    filestore_log_path: "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
    filestore_status_path: "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.json"

- name: "Escrever status inicial (deploy:queued)"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "deploy:queued",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

- name: "Garantir diretório de logs no host"
  ansible.builtin.file:
    path: "{{ host_deploy_logs_dir }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ machine_name }}"

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

# -----------------------------------------------------------------------------
# EXEC com log em arquivo remoto + stdout pro Ansible
# -----------------------------------------------------------------------------
- name: "Executar init_deploy.sh no host (com log + stdout)"
  ansible.builtin.shell: |
    set -o pipefail
    LOG="{{ host_deploy_logs_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}-deploy.log"
    cd "{{ host_deploy_scripts }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_deploy.sh 2>&1 | tee -a "$LOG"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  environment: "{{ effective_env }}"
  become: true
  delegate_to: "{{ machine_name }}"
  register: deploy_result
  changed_when: true
  ignore_errors: true

- name: "Tail do log remoto do deploy (sempre)"
  ansible.builtin.shell: |
    LOG="{{ host_deploy_logs_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}-deploy.log"
    echo "===== tail -n 200 $LOG ====="
    tail -n 200 "$LOG" 2>/dev/null || echo "log não encontrado"
  args:
    executable: /bin/bash
  become: true
  delegate_to: "{{ machine_name }}"
  register: deploy_tail
  changed_when: false

- name: "DEBUG deploy tail"
  ansible.builtin.debug:
    var: deploy_tail.stdout_lines

# -----------------------------------------------------------------------------
# Status final local
# -----------------------------------------------------------------------------
- name: "Atualizar status após deploy"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "{{ 'deploy:ok' if (deploy_result.rc | default(1)) == 0 else 'deploy:error' }}",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ deploy_result.rc | default(1) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# Upload File Store (STATUS + LOG) - SEMPRE
# -----------------------------------------------------------------------------
- name: "Upload File Store (deploy)"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ machine_name }}"
    machine_status_file: "{{ machine_status_file }}"
    log_content: |
      ---- pipeline deploy ----
      rc={{ deploy_result.rc | default(1) }}
      ---- stdout ----
      {{ deploy_result.stdout | default('') }}
      ---- stderr ----
      {{ deploy_result.stderr | default('') }}
      ---- tail log remoto ----
      {{ (deploy_tail.stdout | default('')) }}
      ---- pipeline deploy ----
    status_tag_value: "{{ (deployment_ref | lower) ~ ':deploy:' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}"
    extra_tags: []

# -----------------------------------------------------------------------------
# Falha a pipeline se deploy deu erro (DEPOIS do upload)
# -----------------------------------------------------------------------------
- name: "Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ machine_name }} (host {{ machine_cfg.host }}) rc={{ deploy_result.rc | default(1) }}"
  when: (deploy_result.rc | default(1)) != 0
