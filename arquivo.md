---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina) - COM ESTRUTURA:
#   dev/$GIT_TAG/$MACHINE-(PRE-DEPLOY|DEPLOY|ROLLBACK)
#
# Requer:
#   current_machine, deployment_ref, filestore_env, machine_status_file, log_content, status_tag_value
# Opcional:
#   stage_name (predeploy|deploy|rollback)  (default: predeploy)
#   extra_tags []
# Token:
#   HARNESS_X_API_KEY (env) ou harness_x_api_key var
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

- name: "Harness | Definir defaults + normalizações (parte 1)"
  ansible.builtin.set_fact:
    harness_account_id_resolved: >-
      {{
        (harness_account_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_ACCOUNT_ID') | default('', true) | string | trim), true)
          | default('fgDto6qoTT6ctfZS9eWbEw', true)
      }}
    harness_org_id_resolved: >-
      {{
        (harness_org_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_ORG_ID') | default('', true) | string | trim), true)
          | default('Fiserv', true)
      }}
    harness_project_id_resolved: >-
      {{
        (harness_project_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_PROJECT_ID') | default('', true) | string | trim), true)
          | default('sitef', true)
      }}
    harness_filestore_root_identifier_resolved: >-
      {{
        (harness_filestore_root_identifier_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_FILESTORE_ROOT_IDENTIFIER') | default('', true) | string | trim), true)
          | default('Root', true)
      }}

    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    env_lower: "{{ (filestore_env | string | trim | lower) }}"
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"
    stage_raw: "{{ (stage_name | default('predeploy')) | string | trim | lower }}"

- name: "Harness | Definir stage_label (parte 2)"
  ansible.builtin.set_fact:
    stage_label: "{{ {'predeploy':'PRE-DEPLOY','deploy':'DEPLOY','rollback':'ROLLBACK'}.get(stage_raw, stage_raw | upper) }}"

- name: "Harness | Identifiers SAFE (HARNESS RULE: [A-Za-z][A-Za-z0-9_]{0,127})"
  ansible.builtin.set_fact:
    env_identifier: "{{ ('d_' ~ env_lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    deployment_ref_identifier: "{{ ('d_' ~ (deployment_ref_folder | lower)) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    machine_identifier: "{{ ('m_' ~ (current_machine | lower)) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    stage_identifier: "{{ ('s_' ~ (stage_label | lower)) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    machine_stage_name: "{{ (current_machine | trim) ~ '-' ~ stage_label }}"
    machine_stage_identifier_raw: "{{ ('p_' ~ (current_machine | lower) ~ '_' ~ (stage_label | lower)) | regex_replace('[^A-Za-z0-9_]', '_') }}"

- name: "Harness | Limitar machine_stage_identifier a 128 (se precisar)"
  ansible.builtin.set_fact:
    machine_stage_identifier: >-
      {{
        (machine_stage_identifier_raw[0:110] ~ '_' ~ (machine_stage_identifier_raw | hash('sha1'))[0:16])
        if (machine_stage_identifier_raw | length) > 128 else machine_stage_identifier_raw
      }}

- name: "Harness | Resolver token (vars > env)"
  no_log: true
  ansible.builtin.set_fact:
    harness_api_key_resolved: >-
      {{
        (harness_api_key_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_API_KEY') | default('', true) | string | trim), true)
          | default((lookup('ansible.builtin.env','HARNESS_PAT')     | default('', true) | string | trim), true)
          | default((lookup('ansible.builtin.env','HARNESS_TOKEN')   | default('', true) | string | trim), true)
      }}

- name: "Harness | Debug seguro do token (não mostra valor)"
  ansible.builtin.debug:
    msg: >-
      Harness token presente? {{
        (harness_api_key_resolved | default('') | length) > 10
      }} (len={{ harness_api_key_resolved | default('') | length }})

- name: "Harness | Falhar se token vazio"
  ansible.builtin.assert:
    that:
      - (harness_api_key_resolved | default('') | length) > 10
    fail_msg: >-
      Harness token não encontrado. Defina harness_api_key_override OU exporte
      HARNESS_API_KEY (ou HARNESS_PAT / HARNESS_TOKEN) no step ANTES do ansible.

- name: "Harness | Map token para header x-api-key (sem recursão)"
  no_log: true
  ansible.builtin.set_fact:
    harness_x_api_key_resolved: "{{ harness_api_key_resolved }}"

- name: "Harness | Alias para o header usado nos curls"
  no_log: true
  ansible.builtin.set_fact:
    harness_x_api_key: "{{ harness_x_api_key_resolved }}"

- name: "Harness | Falhar se token vazio (upload)"
  ansible.builtin.assert:
    that:
      - (harness_x_api_key_resolved | default('') | length) > 10
    fail_msg: "Token do Harness vazio para upload."

- name: "Harness | Montar tags base"
  ansible.builtin.set_fact:
    harness_tags:
      - { key: "env",        value: "{{ env_lower }}" }
      - { key: "deployment", value: "{{ deployment_ref_lower }}" }
      - { key: "machine",    value: "{{ current_machine | lower }}" }
      - { key: "stage",      value: "{{ stage_label }}" }
      - { key: "tag",        value: "{{ status_tag_value }}" }

- name: "Harness | Adicionar extra_tags como tag=..."
  ansible.builtin.set_fact:
    harness_tags: "{{ harness_tags + [ {'key':'tag','value': (item | string | trim)} ] }}"
  loop: "{{ extra_tags | default([]) }}"
  when: (item | string | trim | length) > 0

- name: "Harness | Definir http_codes aceitos"
  ansible.builtin.set_fact:
    hfs_ok_create_codes: ["200","201","202","409"]
    hfs_ok_update_codes: ["200","201","202"]

- name: "Harness | Render tags JSON"
  ansible.builtin.set_fact:
    harness_tags_json: "{{ harness_tags | to_json }}"

# -----------------------------------------------------------------------------
# Helper: função “mental” — resolve folder existente via POST /file-store/folder
# endpoint documentado no Harness API. :contentReference[oaicite:1]{index=1}
# -----------------------------------------------------------------------------

# -----------------------------------------------------------------------------
# 1) Garantir folder ENV (dev) embaixo da raiz
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (POST)"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl --http1.1 -sS -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  register: hfs_env_create_http
  changed_when: hfs_env_create_http.stdout in ['200','201']
  failed_when: false

- name: "Harness | Ler body ENV (sempre)"
  ansible.builtin.shell: "cat /tmp/hfs_env_create.out 2>/dev/null || true"
  register: hfs_env_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Detectar DUPLICATE_FIELD ENV"
  ansible.builtin.set_fact:
    hfs_env_is_duplicate: >-
      {{
        (hfs_env_create_http.stdout | default('')) == '400'
        and ('\"code\":\"DUPLICATE_FIELD\"' in (hfs_env_create_body.stdout | default('')))
      }}

- name: "Harness | Resolver identifier real do ENV (quando já existe por name)"
  ansible.builtin.shell: |
    set -euo pipefail
    curl --http1.1 -sS \
      --request POST \
      "{{ harness_api_base }}/folder?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -H "Content-Type: application/json" \
      -H "Expect:" \
      -d '{
        "identifier": "{{ harness_filestore_root_identifier_resolved }}",
        "parentIdentifier": "",
        "name": "{{ harness_filestore_root_identifier_resolved }}",
        "type": "FOLDER"
      }'
  args:
    executable: /bin/bash
  register: hfs_root_folder_dump
  changed_when: false
  failed_when: false
  when: hfs_env_is_duplicate | bool

- name: "Harness | Parse children do Root"
  ansible.builtin.set_fact:
    hfs_root_obj: "{{ (hfs_root_folder_dump.stdout | default('{}')) | from_json }}"
    hfs_root_children: >-
      {{
        (
          (hfs_root_obj.data.children if (hfs_root_obj.data is defined) else hfs_root_obj.children)
          | default([])
        )
      }}
  when: hfs_env_is_duplicate | bool

- name: "Harness | Extrair identifier existente do ENV"
  ansible.builtin.set_fact:
    env_identifier_resolved: >-
      {{
        (
          hfs_root_children
          | selectattr('type','equalto','FOLDER')
          | selectattr('name','equalto', env_lower)
          | map(attribute='identifier')
          | list
          | first
        ) | default('')
      }}
  when: hfs_env_is_duplicate | bool

- name: "Harness | Definir ENV identifier final (criado ou existente)"
  ansible.builtin.set_fact:
    env_identifier_final: >-
      {{
        (env_identifier_resolved | string | trim)
        if (hfs_env_is_duplicate | bool)
        else env_identifier
      }}

- name: "Harness | Falhar ENV se não for OK e não for DUPLICATE_FIELD"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ hfs_env_http_code | default('') }}
      body={{ hfs_env_create_body.stdout | default('') }}
  when:
    - (hfs_env_http_code | default('') | trim) not in hfs_ok_create_codes
    - not (hfs_env_is_duplicate | bool)

- name: "Harness | Falhar se ENV já existe mas não consegui resolver identifier"
  ansible.builtin.fail:
    msg: |
      Folder ENV "{{ env_lower }}" já existe, mas não consegui resolver o identifier pelo endpoint /folder.
      root_children_count={{ (hfs_root_children | default([])) | length }}
  when:
    - hfs_env_is_duplicate | bool
    - (env_identifier_final | default('') | length) == 0

# -----------------------------------------------------------------------------
# 2) Garantir folder GIT_TAG (deployment_ref) embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder GIT_TAG (POST)"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl --http1.1 -sS -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
      -F "name={{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_identifier_final }}" \
      -F "identifier={{ deployment_ref_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  register: hfs_ref_create_http
  changed_when: hfs_ref_create_http.stdout in ['200','201']
  failed_when: false

- name: "Harness | Ler body REF (sempre)"
  ansible.builtin.shell: "cat /tmp/hfs_ref_create.out 2>/dev/null || true"
  register: hfs_ref_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Detectar DUPLICATE_FIELD ENV (robusto)"
  ansible.builtin.set_fact:
    hfs_env_http_code: "{{ (hfs_env_create_http.stdout | default('') | string | trim) }}"
    hfs_env_is_duplicate: >-
      {{
        (hfs_env_http_code == '400')
        and (
          (
            (hfs_env_create_body.stdout | default(''))
            | regex_search('\"code\"\\s*:\\s*\"DUPLICATE_FIELD\"')
            | default('')
          ) | length > 0
        )
      }}

- name: "Harness | Dump do ENV via /folder (para achar identifier do GIT_TAG existente)"
  ansible.builtin.shell: |
    set -euo pipefail
    curl --http1.1 -sS \
      --request POST \
      "{{ harness_api_base }}/folder?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -H "Content-Type: application/json" \
      -H "Expect:" \
      -d '{
        "identifier": "{{ env_identifier_final }}",
        "parentIdentifier": "{{ harness_filestore_root_identifier_resolved }}",
        "name": "{{ env_lower }}",
        "type": "FOLDER"
      }'
  args:
    executable: /bin/bash
  register: hfs_env_folder_dump
  changed_when: false
  failed_when: false
  when: hfs_ref_is_duplicate | bool

- name: "Harness | Parse children do ENV"
  ansible.builtin.set_fact:
    hfs_env_obj: "{{ (hfs_env_folder_dump.stdout | default('{}')) | from_json }}"
    hfs_env_children: >-
      {{
        (
          (hfs_env_obj.data.children if (hfs_env_obj.data is defined) else hfs_env_obj.children)
          | default([])
        )
      }}
  when: hfs_ref_is_duplicate | bool

- name: "Harness | Extrair identifier existente do GIT_TAG"
  ansible.builtin.set_fact:
    deployment_ref_identifier_resolved: >-
      {{
        (
          hfs_env_children
          | selectattr('type','equalto','FOLDER')
          | selectattr('name','equalto', deployment_ref_folder)
          | map(attribute='identifier')
          | list
          | first
        ) | default('')
      }}
  when: hfs_ref_is_duplicate | bool

- name: "Harness | Definir GIT_TAG identifier final (criado ou existente)"
  ansible.builtin.set_fact:
    deployment_ref_identifier_final: >-
      {{
        (deployment_ref_identifier_resolved | string | trim)
        if (hfs_ref_is_duplicate | bool)
        else deployment_ref_identifier
      }}

- name: "Harness | Falhar REF se não for OK e não for DUPLICATE_FIELD"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder GIT_TAG no Harness.
      http_code={{ hfs_ref_create_http.stdout | default('') }}
      body={{ hfs_ref_create_body.stdout | default('') }}
  when:
    - (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes
    - not (hfs_ref_is_duplicate | bool)

- name: "Harness | Falhar se GIT_TAG já existe mas não consegui resolver identifier"
  ansible.builtin.fail:
    msg: |
      Folder GIT_TAG "{{ deployment_ref_folder }}" já existe, mas não consegui resolver o identifier pelo endpoint /folder.
      env_children_count={{ (hfs_env_children | default([])) | length }}
  when:
    - hfs_ref_is_duplicate | bool
    - (deployment_ref_identifier_final | default('') | length) == 0

# -----------------------------------------------------------------------------
# 3) Garantir folder LEAF (MACHINE-STAGE) embaixo do GIT_TAG
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder LEAF (MACHINE-STAGE) (POST)"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl --http1.1 -sS -o /tmp/hfs_leaf_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
      -F "name={{ machine_stage_name }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ deployment_ref_identifier_final }}" \
      -F "identifier={{ machine_stage_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  register: hfs_leaf_create_http
  changed_when: hfs_leaf_create_http.stdout in ['200','201']
  failed_when: false

- name: "Harness | Ler body LEAF (sempre)"
  ansible.builtin.shell: "cat /tmp/hfs_leaf_create.out 2>/dev/null || true"
  register: hfs_leaf_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Detectar DUPLICATE_FIELD LEAF"
  ansible.builtin.set_fact:
    hfs_leaf_is_duplicate: >-
      {{
        (hfs_leaf_create_http.stdout | default('')) == '400'
        and ('\"code\":\"DUPLICATE_FIELD\"' in (hfs_leaf_create_body.stdout | default('')))
      }}

- name: "Harness | Dump do GIT_TAG via /folder (para achar identifier do LEAF existente)"
  ansible.builtin.shell: |
    set -euo pipefail
    curl --http1.1 -sS \
      --request POST \
      "{{ harness_api_base }}/folder?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -H "Content-Type: application/json" \
      -H "Expect:" \
      -d '{
        "identifier": "{{ deployment_ref_identifier_final }}",
        "parentIdentifier": "{{ env_identifier_final }}",
        "name": "{{ deployment_ref_folder }}",
        "type": "FOLDER"
      }'
  args:
    executable: /bin/bash
  register: hfs_ref_folder_dump
  changed_when: false
  failed_when: false
  when: hfs_leaf_is_duplicate | bool

- name: "Harness | Parse children do GIT_TAG"
  ansible.builtin.set_fact:
    hfs_ref_obj: "{{ (hfs_ref_folder_dump.stdout | default('{}')) | from_json }}"
    hfs_ref_children: >-
      {{
        (
          (hfs_ref_obj.data.children if (hfs_ref_obj.data is defined) else hfs_ref_obj.children)
          | default([])
        )
      }}
  when: hfs_leaf_is_duplicate | bool

- name: "Harness | Extrair identifier existente do LEAF"
  ansible.builtin.set_fact:
    leaf_identifier_resolved: >-
      {{
        (
          hfs_ref_children
          | selectattr('type','equalto','FOLDER')
          | selectattr('name','equalto', machine_stage_name)
          | map(attribute='identifier')
          | list
          | first
        ) | default('')
      }}
  when: hfs_leaf_is_duplicate | bool

- name: "Harness | Definir LEAF identifier final (criado ou existente)"
  ansible.builtin.set_fact:
    leaf_identifier_final: >-
      {{
        (leaf_identifier_resolved | string | trim)
        if (hfs_leaf_is_duplicate | bool)
        else machine_stage_identifier
      }}

- name: "Harness | Falhar LEAF se não for OK e não for DUPLICATE_FIELD"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder LEAF (MACHINE-STAGE) no Harness.
      http_code={{ hfs_leaf_create_http.stdout | default('') }}
      body={{ hfs_leaf_create_body.stdout | default('') }}
  when:
    - (hfs_leaf_create_http.stdout | default('')) not in hfs_ok_create_codes
    - not (hfs_leaf_is_duplicate | bool)

- name: "Harness | Falhar se LEAF já existe mas não consegui resolver identifier"
  ansible.builtin.fail:
    msg: |
      Folder LEAF "{{ machine_stage_name }}" já existe, mas não consegui resolver o identifier pelo endpoint /folder.
      ref_children_count={{ (hfs_ref_children | default([])) | length }}
  when:
    - hfs_leaf_is_duplicate | bool
    - (leaf_identifier_final | default('') | length) == 0

# -----------------------------------------------------------------------------
# 4) Upload/Upsert STATUS JSON (POST -> se 409 faz PUT)
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do STATUS JSON (dentro do LEAF)"
  ansible.builtin.set_fact:
    hfs_status_name: "status.json"
    hfs_status_identifier_raw: "{{ ('f_status_' ~ deployment_ref_identifier ~ '_' ~ machine_stage_identifier) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    hfs_status_parent_identifier: "{{ leaf_identifier_final }}"

- name: "Harness | Limitar identifier STATUS a 128 (se precisar)"
  ansible.builtin.set_fact:
    hfs_status_identifier: >-
      {{
        (hfs_status_identifier_raw[0:110] ~ '_' ~ (hfs_status_identifier_raw | hash('sha1'))[0:16])
        if (hfs_status_identifier_raw | length) > 128 else hfs_status_identifier_raw
      }}

- name: "Harness | CREATE status"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl --http1.1 -sS -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  register: hfs_status_create_http
  changed_when: (hfs_status_create_http.stdout | default('')) is match('^2..$')
  failed_when: false

- name: "Harness | UPDATE status (se create retornou 409)"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl --http1.1 -sS -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  register: hfs_status_update_http
  when: (hfs_status_create_http.stdout | default('')) == '409'
  changed_when: (hfs_status_update_http.stdout | default('')) is match('^2..$')
  failed_when: false

# -----------------------------------------------------------------------------
# 5) Upload/Upsert LOG (POST -> se 409 faz PUT)
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do LOG (dentro do LEAF)"
  ansible.builtin.set_fact:
    hfs_log_name: "pipeline.log"
    hfs_log_identifier_raw: "{{ ('f_log_' ~ deployment_ref_identifier ~ '_' ~ machine_stage_identifier) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    hfs_log_parent_identifier: "{{ leaf_identifier_final }}"

- name: "Harness | Limitar identifier LOG a 128 (se precisar)"
  ansible.builtin.set_fact:
    hfs_log_identifier: >-
      {{
        (hfs_log_identifier_raw[0:110] ~ '_' ~ (hfs_log_identifier_raw | hash('sha1'))[0:16])
        if (hfs_log_identifier_raw | length) > 128 else hfs_log_identifier_raw
      }}

- name: "Harness | Upload do LOG (create/update) com cleanup garantido"
  block:
    - name: "Harness | Criar arquivo temporário do log no controller"
      ansible.builtin.copy:
        dest: "/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log"
        mode: "0600"
        content: "{{ log_content }}"

    - name: "Harness | CREATE log"
      ansible.builtin.shell: |
        set -euo pipefail
        http="$(curl --http1.1 -sS -o /tmp/hfs_log_create.out -w "%{http_code}" \
          --request POST \
          "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log" \
        )"
        echo "${http}"
      args:
        executable: /bin/bash
      register: hfs_log_create_http
      changed_when: (hfs_log_create_http.stdout | default('')) is match('^2..$')
      failed_when: false
      no_log: true

    - name: "Harness | UPDATE log (se create retornou 409)"
      ansible.builtin.shell: |
        set -euo pipefail
        http="$(curl --http1.1 -sS -o /tmp/hfs_log_update.out -w "%{http_code}" \
          --request PUT \
          "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log" \
        )"
        echo "${http}"
      args:
        executable: /bin/bash
      register: hfs_log_update_http
      when: (hfs_log_create_http.stdout | default('')) == '409'
      changed_when: (hfs_log_update_http.stdout | default('')) is match('^2..$')
      failed_when: false
      no_log: true

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log"
        state: absent
      changed_when: false
      failed_when: false
