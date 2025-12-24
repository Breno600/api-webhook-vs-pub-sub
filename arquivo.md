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

- name: "Harness | Definir defaults + normalizações"
  ansible.builtin.set_fact:
    harness_account_id_resolved: "{{ harness_account_id | default('fgDto6qoTT6ctfZS9eWbEw') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('Fiserv') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('sitef') }}"
    harness_filestore_root_identifier_resolved: "{{ harness_filestore_root_identifier | default('Root') }}"
    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    env_lower: "{{ (filestore_env | string | trim | lower) }}"
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"
    stage_raw: "{{ (stage_name | default('predeploy')) | string | trim | lower }}"
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
  ansible.builtin.set_fact:
    harness_x_api_key: >-
      {{
        (harness_x_api_key | default('') | string | trim)
          if (harness_x_api_key is defined and (harness_x_api_key | string | trim) | length > 0)
          else (lookup('ansible.builtin.env', 'HARNESS_X_API_KEY') | default('', true))
      }}
  no_log: true

- name: "Harness | Falhar se token não está no ambiente"
  ansible.builtin.fail:
    msg: "HARNESS_X_API_KEY não definido no ambiente. Exporte no step do Harness antes de rodar o Ansible."
  when: (harness_x_api_key | length) == 0

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
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (POST) com debug"
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

- name: "Harness | DEBUG ENV (se falhar)"
  ansible.builtin.shell: "sed -n '1,220p' /tmp/hfs_env_create.out || true"
  register: hfs_env_create_body
  changed_when: false
  failed_when: false
  when: (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes

- name: "Harness | Falhar ENV (com body)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ hfs_env_create_http.stdout | default('') }}
      body={{ hfs_env_create_body.stdout | default('') }}
  when: (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes

# -----------------------------------------------------------------------------
# 2) Garantir folder GIT_TAG (deployment_ref) embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder GIT_TAG (POST) com debug"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl --http1.1 -sS -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
      -F "name={{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_identifier }}" \
      -F "identifier={{ deployment_ref_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  register: hfs_ref_create_http
  changed_when: hfs_ref_create_http.stdout in ['200','201']
  failed_when: false

- name: "Harness | DEBUG REF (se falhar)"
  ansible.builtin.shell: "sed -n '1,220p' /tmp/hfs_ref_create.out || true"
  register: hfs_ref_create_body
  changed_when: false
  failed_when: false
  when: (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes

- name: "Harness | Falhar REF (com body)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder GIT_TAG no Harness.
      http_code={{ hfs_ref_create_http.stdout | default('') }}
      body={{ hfs_ref_create_body.stdout | default('') }}
  when: (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes

# -----------------------------------------------------------------------------
# 3) Garantir folder LEAF (MACHINE-STAGE) embaixo do GIT_TAG
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder LEAF (MACHINE-STAGE) (POST) com debug"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl --http1.1 -sS -o /tmp/hfs_leaf_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" -H "Expect:" \
      -F "name={{ machine_stage_name }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ deployment_ref_identifier }}" \
      -F "identifier={{ machine_stage_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  register: hfs_leaf_create_http
  changed_when: hfs_leaf_create_http.stdout in ['200','201']
  failed_when: false

- name: "Harness | DEBUG LEAF (se falhar)"
  ansible.builtin.shell: "sed -n '1,220p' /tmp/hfs_leaf_create.out || true"
  register: hfs_leaf_create_body
  changed_when: false
  failed_when: false
  when: (hfs_leaf_create_http.stdout | default('')) not in hfs_ok_create_codes

- name: "Harness | Falhar LEAF (com body)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder LEAF (MACHINE-STAGE) no Harness.
      http_code={{ hfs_leaf_create_http.stdout | default('') }}
      body={{ hfs_leaf_create_body.stdout | default('') }}
  when: (hfs_leaf_create_http.stdout | default('')) not in hfs_ok_create_codes

# -----------------------------------------------------------------------------
# 4) Upload/Upsert STATUS JSON (POST -> se 409 faz PUT)
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do STATUS JSON (dentro do LEAF)"
  ansible.builtin.set_fact:
    hfs_status_name: "status.json"
    hfs_status_identifier_raw: "{{ ('f_status_' ~ deployment_ref_identifier ~ '_' ~ machine_stage_identifier) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    hfs_status_parent_identifier: "{{ machine_stage_identifier }}"

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
    hfs_log_parent_identifier: "{{ machine_stage_identifier }}"

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


- include_tasks: harness_filestore_upload.yml
  vars:
    stage_name: "predeploy"
    current_machine: "{{ current_machine }}"
    deployment_ref: "{{ deployment_ref }}"
    filestore_env: "{{ filestore_env }}"
    machine_status_file: "{{ machine_status_file }}"
    log_content: "{{ log_content }}"
    status_tag_value: "{{ status_tag_value }}"
