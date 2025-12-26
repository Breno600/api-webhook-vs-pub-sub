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
#   HARNESS_API_KEY / HARNESS_PAT / HARNESS_TOKEN (env) ou harness_api_key_override
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

    hfs_dup_pattern: "\"code\"\\s*:\\s*\"DUPLICATE_FIELD\""

- name: "Harness | Definir stage_label (parte 2)"
  ansible.builtin.set_fact:
    stage_label: "{{ {'predeploy':'PRE-DEPLOY','deploy':'DEPLOY','rollback':'ROLLBACK'}.get(stage_raw, stage_raw | upper) }}"

- name: "Harness | Identifiers SAFE (HARNESS RULE: [A-Za-z][A-Za-z0-9_]{0,127})"
  ansible.builtin.set_fact:
    # troca qualquer coisa fora [A-Za-z0-9_] por "_"
    env_identifier: "{{ ('d_' ~ env_lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    deployment_ref_identifier: "{{ ('d_' ~ (deployment_ref_folder | lower)) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    machine_identifier: "{{ ('m_' ~ (current_machine | lower)) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    stage_identifier: "{{ ('s_' ~ (stage_label | lower)) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    # pasta leaf: MACHINE-STAGE
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

- name: "Harness | Map token para header x-api-key"
  no_log: true
  ansible.builtin.set_fact:
    harness_x_api_key: "{{ harness_api_key_resolved }}"

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
# 1) Garantir folder ENV (dev) embaixo da raiz
#    REGRA: se já existe por nome, Harness retorna 400 + DUPLICATE_FIELD.
#    Nesse caso, só "aceita" e continua usando o identifier determinístico (env_identifier).
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (POST)"
  no_log: true
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

- name: "Harness | Normalizar http_code ENV + detectar DUPLICATE_FIELD"
  ansible.builtin.set_fact:
    hfs_env_http_code: "{{ (hfs_env_create_http.stdout | default('') | string | trim) }}"
    hfs_env_is_duplicate: >-
      {{
        ( (hfs_env_create_http.stdout | default('') | string | trim) == '400' )
        and (
          (
            (hfs_env_create_body.stdout | default('', true))
            | regex_search(hfs_dup_pattern)
            | default('', true)
          ) | length > 0
        )
      }}

- name: "Harness | ENV identifier final (determinístico)"
  ansible.builtin.set_fact:
    env_identifier_final: "{{ env_identifier }}"

- name: "Harness | Falhar ENV (se não for OK e não for DUPLICATE_FIELD)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ hfs_env_http_code }}
      body={{ hfs_env_create_body.stdout | default('') }}
  when:
    - (hfs_env_http_code | default('') | trim) not in hfs_ok_create_codes
    - not (hfs_env_is_duplicate | bool)

# -----------------------------------------------------------------------------
# 2) Garantir folder GIT_TAG (deployment_ref) embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder GIT_TAG (POST)"
  no_log: true
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

- name: "Harness | Normalizar http_code REF + detectar DUPLICATE_FIELD"
  ansible.builtin.set_fact:
    hfs_ref_http_code: "{{ (hfs_ref_create_http.stdout | default('') | string | trim) }}"
    hfs_ref_is_duplicate: >-
      {{
        ( (hfs_ref_create_http.stdout | default('') | string | trim) == '400' )
        and (
          (
            (hfs_ref_create_body.stdout | default('', true))
            | regex_search(hfs_dup_pattern)
            | default('', true)
          ) | length > 0
        )
      }}

- name: "Harness | GIT_TAG identifier final (determinístico)"
  ansible.builtin.set_fact:
    deployment_ref_identifier_final: "{{ deployment_ref_identifier }}"

- name: "Harness | Falhar REF (se não for OK e não for DUPLICATE_FIELD)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder GIT_TAG no Harness.
      http_code={{ hfs_ref_http_code }}
      body={{ hfs_ref_create_body.stdout | default('') }}
  when:
    - (hfs_ref_http_code | default('') | trim) not in hfs_ok_create_codes
    - not (hfs_ref_is_duplicate | bool)

# -----------------------------------------------------------------------------
# 3) Garantir folder LEAF (MACHINE-STAGE) embaixo do GIT_TAG
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder LEAF (MACHINE-STAGE) (POST)"
  no_log: true
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

- name: "Harness | Normalizar http_code LEAF + detectar DUPLICATE_FIELD"
  ansible.builtin.set_fact:
    hfs_leaf_http_code: "{{ (hfs_leaf_create_http.stdout | default('') | string | trim) }}"
    hfs_leaf_is_duplicate: >-
      {{
        ( (hfs_leaf_create_http.stdout | default('') | string | trim) == '400' )
        and (
          (
            (hfs_leaf_create_body.stdout | default('', true))
            | regex_search(hfs_dup_pattern)
            | default('', true)
          ) | length > 0
        )
      }}

- name: "Harness | LEAF identifier final (determinístico)"
  ansible.builtin.set_fact:
    leaf_identifier_final: "{{ machine_stage_identifier }}"

- name: "Harness | Falhar LEAF (se não for OK e não for DUPLICATE_FIELD)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder LEAF (MACHINE-STAGE) no Harness.
      http_code={{ hfs_leaf_http_code }}
      body={{ hfs_leaf_create_body.stdout | default('') }}
  when:
    - (hfs_leaf_http_code | default('') | trim) not in hfs_ok_create_codes
    - not (hfs_leaf_is_duplicate | bool)

# -----------------------------------------------------------------------------
# 4) Upload/Upsert STATUS JSON (POST -> se 409 OU DUPLICATE_FIELD faz PUT)
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
  no_log: true
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

- name: "Harness | Ler body STATUS (sempre)"
  ansible.builtin.shell: "cat /tmp/hfs_status_create.out 2>/dev/null || true"
  register: hfs_status_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Normalizar http_code STATUS + decidir se precisa UPDATE"
  ansible.builtin.set_fact:
    hfs_status_http_code: "{{ (hfs_status_create_http.stdout | default('') | string | trim) }}"
    hfs_status_is_duplicate_400: >-
      {{
        ( (hfs_status_create_http.stdout | default('') | string | trim) == '400' )
        and (
          (
            (hfs_status_create_body.stdout | default('', true))
            | regex_search(hfs_dup_pattern)
            | default('', true)
          ) | length > 0
        )
      }}
    hfs_status_needs_update: >-
      {{
        ( (hfs_status_create_http.stdout | default('') | string | trim) == '409' )
        or (
          ( (hfs_status_create_http.stdout | default('') | string | trim) == '400' )
          and (
            (
              (hfs_status_create_body.stdout | default('', true))
              | regex_search(hfs_dup_pattern)
              | default('', true)
            ) | length > 0
          )
        )
      }}

- name: "Harness | Falhar STATUS (se não for 2xx e não for caso de UPDATE)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar status.json no Harness.
      http_code={{ hfs_status_http_code }}
      body={{ hfs_status_create_body.stdout | default('') }}
  when:
    - not (hfs_status_http_code is match('^2..$'))
    - not (hfs_status_needs_update | bool)

- name: "Harness | UPDATE status (se create retornou 409 ou DUPLICATE_FIELD)"
  no_log: true
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
  when: hfs_status_needs_update | bool
  changed_when: (hfs_status_update_http.stdout | default('')) is match('^2..$')
  failed_when: false

- name: "Harness | Falhar UPDATE STATUS (se não for 2xx)"
  ansible.builtin.fail:
    msg: |
      Falha ao atualizar status.json no Harness.
      http_code={{ (hfs_status_update_http.stdout | default('') | string | trim) }}
      body={{ lookup('ansible.builtin.file', '/tmp/hfs_status_update.out', errors='ignore') | default('') }}
  when:
    - hfs_status_needs_update | bool
    - not ((hfs_status_update_http.stdout | default('') | string | trim) is match('^2..$'))

# -----------------------------------------------------------------------------
# 5) Upload/Upsert LOG (POST -> se 409 OU DUPLICATE_FIELD faz PUT) + cleanup
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
      no_log: true
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

    - name: "Harness | Ler body LOG (sempre)"
      ansible.builtin.shell: "cat /tmp/hfs_log_create.out 2>/dev/null || true"
      register: hfs_log_create_body
      changed_when: false
      failed_when: false

    - name: "Harness | Normalizar http_code LOG + decidir se precisa UPDATE"
      ansible.builtin.set_fact:
        hfs_log_http_code: "{{ (hfs_log_create_http.stdout | default('') | string | trim) }}"
        hfs_log_needs_update: >-
          {{
            ( (hfs_log_create_http.stdout | default('') | string | trim) == '409' )
            or (
              ( (hfs_log_create_http.stdout | default('') | string | trim) == '400' )
              and (
                (
                  (hfs_log_create_body.stdout | default('', true))
                  | regex_search(hfs_dup_pattern)
                  | default('', true)
                ) | length > 0
              )
            )
          }}

    - name: "Harness | Falhar LOG (se não for 2xx e não for caso de UPDATE)"
      ansible.builtin.fail:
        msg: |
          Falha ao criar pipeline.log no Harness.
          http_code={{ hfs_log_http_code }}
          body={{ hfs_log_create_body.stdout | default('') }}
      when:
        - not (hfs_log_http_code is match('^2..$'))
        - not (hfs_log_needs_update | bool)

    - name: "Harness | UPDATE log (se create retornou 409 ou DUPLICATE_FIELD)"
      no_log: true
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
      when: hfs_log_needs_update | bool
      changed_when: (hfs_log_update_http.stdout | default('')) is match('^2..$')
      failed_when: false

    - name: "Harness | Falhar UPDATE LOG (se não for 2xx)"
      ansible.builtin.fail:
        msg: |
          Falha ao atualizar pipeline.log no Harness.
          http_code={{ (hfs_log_update_http.stdout | default('') | string | trim) }}
          body={{ lookup('ansible.builtin.file', '/tmp/hfs_log_update.out', errors='ignore') | default('') }}
      when:
        - hfs_log_needs_update | bool
        - not ((hfs_log_update_http.stdout | default('') | string | trim) is match('^2..$'))

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log"
        state: absent
      changed_when: false
      failed_when: false
