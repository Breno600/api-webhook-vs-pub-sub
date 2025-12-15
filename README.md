harness_filestore_upload.yml

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
    deployment_ref_lower: "{{ deployment_ref | string | trim }}"
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
      -F "name={{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_lower }}" \
      -F "identifier={{ deployment_ref_folder }}" \
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
      -F "parentIdentifier={{ deployment_ref_folder }}" \
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
      -F "parentIdentifier={{ deployment_ref_folder }}" \
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
      -F "parentIdentifier={{ deployment_ref_folder }}" \
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
      -F "parentIdentifier={{ deployment_ref_folder }}" \
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
