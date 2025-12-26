---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina) - SIMPLES / SEM LOOKUP
#
# Estrutura (por identifier, que é o que normalmente aparece no UI):
#   dev/DEV000002/sitef_02_pre_deploy/
#     status_<tag>_<run>.json
#     pipeline_<tag>_<run>.log
#
# Requer:
#   current_machine, deployment_ref, filestore_env, machine_status_file, log_content, status_tag_value
# Opcional:
#   stage_name (predeploy|deploy|rollback)  (default: predeploy)
#   extra_tags []
# Token:
#   harness_api_key_override (var) OU env: HARNESS_API_KEY / HARNESS_PAT / HARNESS_TOKEN
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

    # run_id sempre existe
    hfs_run_id_resolved: "{{ (run_id | default(ansible_date_time.epoch)) | string | trim }}"

- name: "Harness | Definir stage_label"
  ansible.builtin.set_fact:
    stage_label: "{{ {'predeploy':'PRE-DEPLOY','deploy':'DEPLOY','rollback':'ROLLBACK'}.get(stage_raw, stage_raw | upper) }}"

# Para o IDENTIFIER do leaf (sem hífen, e é isso que o UI costuma mostrar)
- name: "Harness | Definir leaf_identifier_final (folder da máquina/stage)"
  ansible.builtin.set_fact:
    leaf_identifier_raw: "{{ ((current_machine | string | trim) ~ '_' ~ stage_label | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    leaf_identifier_final: >-
      {{
        leaf_identifier_raw
        if (leaf_identifier_raw | regex_search('^[A-Za-z]'))
        else ('p_' ~ leaf_identifier_raw)
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

- name: "Harness | Falhar se token vazio"
  ansible.builtin.assert:
    that:
      - (harness_api_key_resolved | default('') | length) > 10
    fail_msg: >-
      Harness token não encontrado. Defina harness_api_key_override OU exporte
      HARNESS_API_KEY (ou HARNESS_PAT / HARNESS_TOKEN) no step ANTES do ansible.

- name: "Harness | Definir http_codes aceitos"
  ansible.builtin.set_fact:
    hfs_ok_folder_codes: ["200","201","202","409"]
    hfs_ok_file_codes:   ["200","201","202"]
    hfs_retry_opts: "--connect-timeout 10 --max-time 60 --retry 2 --retry-delay 1 --retry-connrefused"

# -----------------------------------------------------------------------------
# Tags
# -----------------------------------------------------------------------------
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

- name: "Harness | Render tags JSON"
  ansible.builtin.set_fact:
    harness_tags_json: "{{ harness_tags | to_json }}"

# -----------------------------------------------------------------------------
# Identifiers ENV + DEPLOYMENT
# -----------------------------------------------------------------------------
- name: "Harness | Identifiers ENV + DEPLOYMENT"
  ansible.builtin.set_fact:
    env_identifier_final: "{{ env_lower }}"
    deployment_ref_identifier_raw: "{{ deployment_ref_folder | regex_replace('[^A-Za-z0-9_]', '_') }}"
    deployment_ref_identifier_final: >-
      {{
        deployment_ref_identifier_raw
        if (deployment_ref_identifier_raw | regex_search('^[A-Za-z]'))
        else ('d_' ~ deployment_ref_identifier_raw)
      }}

# -----------------------------------------------------------------------------
# Filenames versionados (evitar DUPLICATE_FIELD por name)
# -----------------------------------------------------------------------------
- name: "Harness | Definir nomes versionados (tag + run_id)"
  ansible.builtin.set_fact:
    hfs_tag_safe: "{{ (status_tag_value | default('no_tag') | string | trim) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    hfs_run_safe: "{{ (hfs_run_id_resolved | string | trim) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    hfs_status_filename: "status_{{ hfs_tag_safe }}_{{ hfs_run_safe }}.json"
    hfs_log_filename:    "pipeline_{{ hfs_tag_safe }}_{{ hfs_run_safe }}.log"
    # identifiers também versionados (pra nunca “sumir” por overwrite)
    hfs_status_identifier: "{{ ('status_' ~ hfs_tag_safe ~ '_' ~ hfs_run_safe) }}"
    hfs_log_identifier:    "{{ ('log_' ~ hfs_tag_safe ~ '_' ~ hfs_run_safe) }}"

# -----------------------------------------------------------------------------
# 1) Garantir folder ENV embaixo do Root
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ env_identifier_final }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_identifier_final }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_env_create_http
  failed_when: false
  changed_when: (hfs_env_create_http.stdout | trim) in ['200','201']

- name: "Harness | Ler body ENV"
  ansible.builtin.shell: "cat /tmp/hfs_env_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_env_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Falhar ENV se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ hfs_env_create_http.stdout | default('') | trim }}
      body={{ hfs_env_create_body.stdout | default('') }}
  when: (hfs_env_create_http.stdout | default('') | trim) not in hfs_ok_folder_codes

# -----------------------------------------------------------------------------
# 2) Garantir folder DEPLOYMENT (GIT_TAG) embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder DEPLOYMENT (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ deployment_ref_identifier_final }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_identifier_final }}" \
      -F "identifier={{ deployment_ref_identifier_final }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_ref_create_http
  failed_when: false
  changed_when: (hfs_ref_create_http.stdout | trim) in ['200','201']

- name: "Harness | Ler body DEPLOYMENT"
  ansible.builtin.shell: "cat /tmp/hfs_ref_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_ref_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Falhar DEPLOYMENT se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder DEPLOYMENT no Harness.
      http_code={{ hfs_ref_create_http.stdout | default('') | trim }}
      body={{ hfs_ref_create_body.stdout | default('') }}
  when: (hfs_ref_create_http.stdout | default('') | trim) not in hfs_ok_folder_codes

# -----------------------------------------------------------------------------
# 3) Garantir folder LEAF (machine_stage) embaixo do DEPLOYMENT
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder LEAF (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_leaf_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ leaf_identifier_final }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ deployment_ref_identifier_final }}" \
      -F "identifier={{ leaf_identifier_final }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_leaf_create_http
  failed_when: false
  changed_when: (hfs_leaf_create_http.stdout | trim) in ['200','201']

- name: "Harness | Ler body LEAF"
  ansible.builtin.shell: "cat /tmp/hfs_leaf_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_leaf_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Falhar LEAF se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder LEAF no Harness.
      http_code={{ hfs_leaf_create_http.stdout | default('') | trim }}
      body={{ hfs_leaf_create_body.stdout | default('') }}
  when: (hfs_leaf_create_http.stdout | default('') | trim) not in hfs_ok_folder_codes

# -----------------------------------------------------------------------------
# 4) Upload STATUS (POST e se já existir, PUT)
# -----------------------------------------------------------------------------
- name: "Harness | CREATE STATUS"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ hfs_status_filename }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ leaf_identifier_final }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_status_create_http
  failed_when: false
  changed_when: (hfs_status_create_http.stdout | default('') | trim) is match('^2..$')

- name: "Harness | UPDATE STATUS (se já existe)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ hfs_status_filename }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ leaf_identifier_final }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_status_update_http
  when: (hfs_status_create_http.stdout | default('') | trim) in ['409','400']
  failed_when: false
  changed_when: (hfs_status_update_http.stdout | default('') | trim) is match('^2..$')

- name: "Harness | Falhar STATUS se create e update não deram certo"
  ansible.builtin.fail:
    msg: |
      Falha ao enviar STATUS
      create_http={{ hfs_status_create_http.stdout | default('') | trim }}
      update_http={{ hfs_status_update_http.stdout | default('') | trim }}
  when: >
    not ((hfs_status_create_http.stdout | default('') | trim) is match('^2..$'))
    and not ((hfs_status_update_http.stdout | default('') | trim) is match('^2..$'))

# -----------------------------------------------------------------------------
# 5) Upload LOG (POST e se já existir, PUT) + cleanup
# -----------------------------------------------------------------------------
- name: "Harness | Upload do LOG (create/update) com cleanup garantido"
  block:
    - name: "Harness | Criar arquivo temporário do log no controller"
      ansible.builtin.copy:
        dest: "/tmp/hfs_{{ leaf_identifier_final }}_{{ hfs_run_safe }}.log"
        mode: "0600"
        content: "{{ log_content }}"

    - name: "Harness | CREATE LOG"
      ansible.builtin.shell: |
        set -o pipefail
        http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_log_create.out -w "%{http_code}" \
          --request POST \
          "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
          -F "name={{ hfs_log_filename }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ leaf_identifier_final }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/hfs_{{ leaf_identifier_final }}_{{ hfs_run_safe }}.log" \
        )"
        echo "${http}"
      args:
        executable: /bin/bash
      environment:
        HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
      register: hfs_log_create_http
      failed_when: false
      changed_when: (hfs_log_create_http.stdout | default('') | trim) is match('^2..$')

    - name: "Harness | UPDATE LOG (se já existe)"
      ansible.builtin.shell: |
        set -o pipefail
        http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_log_update.out -w "%{http_code}" \
          --request PUT \
          "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
          -F "name={{ hfs_log_filename }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ leaf_identifier_final }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/hfs_{{ leaf_identifier_final }}_{{ hfs_run_safe }}.log" \
        )"
        echo "${http}"
      args:
        executable: /bin/bash
      environment:
        HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
      register: hfs_log_update_http
      when: (hfs_log_create_http.stdout | default('') | trim) in ['409','400']
      failed_when: false
      changed_when: (hfs_log_update_http.stdout | default('') | trim) is match('^2..$')

    - name: "Harness | Falhar LOG se create e update não deram certo"
      ansible.builtin.fail:
        msg: |
          Falha ao enviar LOG
          create_http={{ hfs_log_create_http.stdout | default('') | trim }}
          update_http={{ hfs_log_update_http.stdout | default('') | trim }}
      when: >
        not ((hfs_log_create_http.stdout | default('') | trim) is match('^2..$'))
        and not ((hfs_log_update_http.stdout | default('') | trim) is match('^2..$'))

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/hfs_{{ leaf_identifier_final }}_{{ hfs_run_safe }}.log"
        state: absent
      changed_when: false
      failed_when: false

# -----------------------------------------------------------------------------
# Debug útil (mostra exatamente o que você deve enxergar no UI)
# -----------------------------------------------------------------------------
- name: "Harness | DEBUG path final esperado no UI"
  ansible.builtin.debug:
    msg:
      - "PATH (identifier): {{ env_identifier_final }}/{{ deployment_ref_identifier_final }}/{{ leaf_identifier_final }}"
      - "STATUS FILE: name={{ hfs_status_filename }}  identifier={{ hfs_status_identifier }}"
      - "LOG    FILE: name={{ hfs_log_filename }}     identifier={{ hfs_log_identifier }}"
