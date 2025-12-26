man pq vc fica querendo usar variaveis que vc sabe que nao existe? vc tem como esta atualmente meu arquivo, mas segue ele atual

TASK [Harness | Definir hfs_tag_safe (sanitizar TAG p/ nome/identifier)] *******
ok: [localhost]
TASK [Harness | Definir nomes versionados (evitar DUPLICATE_FIELD)] ************
fatal: [localhost]: FAILED! => {"msg": "The task includes an option with an undefined variable. The error was: 'hfs_run_safe' is undefined. 'hfs_run_safe' is undefined\n\nThe error appears to be in '/tmp/tmp.A8r9QbO1fd/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml': line 289, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n\n- name: \"Harness | Definir nomes versionados (evitar DUPLICATE_FIELD)\"\n  ^ here\n"}
PLAY RECAP *********************************************************************


---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina) - SIMPLES / SEM LOOKUP
#
# Estrutura:
#   dev/$GIT_TAG/$MACHINE-(PRE-DEPLOY|DEPLOY|ROLLBACK)
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

# ✅ FIX: stage_label em uma task, machine_stage_name em outra
- name: "Harness | Definir stage_label"
  ansible.builtin.set_fact:
    stage_label: "{{ {'predeploy':'PRE-DEPLOY','deploy':'DEPLOY','rollback':'ROLLBACK'}.get(stage_raw, stage_raw | upper) }}"

- name: "Harness | Definir machine_stage_name"
  ansible.builtin.set_fact:
    machine_stage_name: "{{ (current_machine | string | trim) ~ '-' ~ stage_label }}"

# -----------------------------------------------------------------------------
# TOKEN (sem recursão)
# -----------------------------------------------------------------------------
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
# Identifiers SIMPLES: usar identifier = name (isso evita lookup e bate com o que já existe)
# Regras Harness: [A-Za-z][A-Za-z0-9_]{0,127}
# -----------------------------------------------------------------------------
# -----------------------------------------------------------------------------
# Identifiers SIMPLES: usar identifier = name (isso evita lookup e bate com o que já existe)
# Regras Harness: [A-Za-z][A-Za-z0-9_]{0,127}
# -----------------------------------------------------------------------------

- name: "Harness | Identifiers (RAW)"
  ansible.builtin.set_fact:
    env_identifier_final: "{{ env_lower }}"

    deployment_ref_identifier_raw: "{{ deployment_ref_folder | regex_replace('[^A-Za-z0-9_]', '_') }}"

    leaf_identifier_raw: "{{ (machine_stage_name | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"

- name: "Harness | Identifiers (FINAL a partir do RAW)"
  ansible.builtin.set_fact:
    deployment_ref_identifier_final: >-
      {{
        deployment_ref_identifier_raw
        if (deployment_ref_identifier_raw | regex_search('^[A-Za-z]'))
        else ('d_' ~ deployment_ref_identifier_raw)
      }}

    leaf_identifier_raw2: >-
      {{
        leaf_identifier_raw
        if (leaf_identifier_raw | regex_search('^[A-Za-z]'))
        else ('p_' ~ leaf_identifier_raw)
      }}

- name: "Harness | Limitar leaf_identifier a 128 (se precisar)"
  ansible.builtin.set_fact:
    leaf_identifier_final: >-
      {{
        (leaf_identifier_raw2[0:110] ~ '_' ~ (leaf_identifier_raw2 | hash('sha1'))[0:16])
        if (leaf_identifier_raw2 | length) > 128 else leaf_identifier_raw2
      }}


- name: "Harness | Limitar leaf_identifier a 128 (se precisar)"
  ansible.builtin.set_fact:
    leaf_identifier_final: >-
      {{
        (leaf_identifier_raw2[0:110] ~ '_' ~ (leaf_identifier_raw2 | hash('sha1'))[0:16])
        if (leaf_identifier_raw2 | length) > 128 else leaf_identifier_raw2
      }}

# -----------------------------------------------------------------------------
# 1) Garantir folder ENV (dev) embaixo do Root
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ env_lower }}" \
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
  changed_when: hfs_env_create_http.stdout | trim in ['200','201']

- name: "Harness | Ler body ENV"
  ansible.builtin.shell: "cat /tmp/hfs_env_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_env_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Marcar ENV duplicate?"
  ansible.builtin.set_fact:
    hfs_env_http_code: "{{ hfs_env_create_http.stdout | default('') | string | trim }}"
    hfs_env_is_duplicate: >-
      {{
        (hfs_env_create_http.stdout | default('') | string | trim) == '400'
        and ((hfs_env_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
      }}

- name: "Harness | Falhar ENV se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ hfs_env_http_code }}
      body={{ hfs_env_create_body.stdout | default('') }}
  when:
    - (hfs_env_http_code not in hfs_ok_folder_codes)
    - not (hfs_env_is_duplicate | bool)

# -----------------------------------------------------------------------------
# 2) Garantir folder GIT_TAG embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder GIT_TAG (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ deployment_ref_folder }}" \
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
  changed_when: hfs_ref_create_http.stdout | trim in ['200','201']

- name: "Harness | Ler body GIT_TAG"
  ansible.builtin.shell: "cat /tmp/hfs_ref_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_ref_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Marcar GIT_TAG duplicate?"
  ansible.builtin.set_fact:
    hfs_ref_http_code: "{{ hfs_ref_create_http.stdout | default('') | string | trim }}"
    hfs_ref_is_duplicate: >-
      {{
        (hfs_ref_create_http.stdout | default('') | string | trim) == '400'
        and ((hfs_ref_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
      }}

- name: "Harness | Falhar GIT_TAG se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder GIT_TAG no Harness.
      http_code={{ hfs_ref_http_code }}
      body={{ hfs_ref_create_body.stdout | default('') }}
  when:
    - (hfs_ref_http_code not in hfs_ok_folder_codes)
    - not (hfs_ref_is_duplicate | bool)

- name: "Harness | Definir hfs_tag_safe (sanitizar TAG p/ nome/identifier)"
  ansible.builtin.set_fact:
    hfs_tag_safe: >-
      {{
        (deployment_ref_resolved | default(deployment_ref | default('')) | string | trim)
        | regex_replace('[^A-Za-z0-9_]', '_')
      }}
    hfs_run_id_safe: >-
      {{
        (hfs_run_id_resolved | default(hfs_run_id | default(run_id | default(ansible_date_time.epoch))) | string | trim)
        | regex_replace('[^A-Za-z0-9_]', '_')
      }}


- name: "Harness | Definir nomes versionados (evitar DUPLICATE_FIELD)"
  ansible.builtin.set_fact:
    hfs_tag_safe: >-
      {{
        (status_tag_value | default(deployment_ref_resolved) | default('no_tag') | string)
        | regex_replace('[^A-Za-z0-9_-]', '_')
      }}
    hfs_run_safe: >-
      {{
        (run_id | default(ansible_date_time.epoch) | string)
        | regex_replace('[^A-Za-z0-9_-]', '_')
      }}

    hfs_status_filename: "status_{{ hfs_tag_safe }}_{{ hfs_run_safe }}.json"
    hfs_log_filename:    "pipeline_{{ hfs_tag_safe }}_{{ hfs_run_safe }}.log"

# -----------------------------------------------------------------------------
# 3) Garantir folder LEAF (MACHINE-STAGE) embaixo do GIT_TAG
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder LEAF (POST)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_leaf_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
      -F "name={{ machine_stage_name }}" \
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
  changed_when: hfs_leaf_create_http.stdout | trim in ['200','201']

- name: "Harness | Ler body LEAF"
  ansible.builtin.shell: "cat /tmp/hfs_leaf_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_leaf_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Marcar LEAF duplicate?"
  ansible.builtin.set_fact:
    hfs_leaf_http_code: "{{ hfs_leaf_create_http.stdout | default('') | string | trim }}"
    hfs_leaf_is_duplicate: >-
      {{
        (hfs_leaf_create_http.stdout | default('') | string | trim) == '400'
        and ((hfs_leaf_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
      }}

- name: "Harness | Falhar LEAF se não OK"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder LEAF no Harness.
      http_code={{ hfs_leaf_http_code }}
      body={{ hfs_leaf_create_body.stdout | default('') }}
  when:
    - (hfs_leaf_http_code not in hfs_ok_folder_codes)
    - not (hfs_leaf_is_duplicate | bool)

# -----------------------------------------------------------------------------
# 4) Upload/Upsert STATUS JSON (POST -> se 409 ou DUPLICATE faz PUT)
# -----------------------------------------------------------------------------
- name: "Harness | Definir identifiers do STATUS"
  ansible.builtin.set_fact:
    hfs_status_name: "{{ hfs_status_filename }}"
    hfs_status_parent_identifier: "{{ leaf_identifier_final }}"
    hfs_status_identifier_raw: "{{ ('status_' ~ deployment_ref_identifier_final ~ '_' ~ leaf_identifier_final) | regex_replace('[^A-Za-z0-9_]', '_') }}"

- name: "Harness | Limitar identifier STATUS a 128"
  ansible.builtin.set_fact:
    hfs_status_identifier: >-
      {{
        (hfs_status_identifier_raw[0:110] ~ '_' ~ (hfs_status_identifier_raw | hash('sha1'))[0:16])
        if (hfs_status_identifier_raw | length) > 128 else hfs_status_identifier_raw
      }}


- name: "Harness | CREATE status.json"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
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
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_status_create_http
  failed_when: false
  changed_when: (hfs_status_create_http.stdout | trim) is match('^2..$')

- name: "Harness | Ler body status CREATE"
  ansible.builtin.shell: "cat /tmp/hfs_status_create.out 2>/dev/null || true"
  args:
    executable: /bin/bash
  register: hfs_status_create_body
  changed_when: false
  failed_when: false

- name: "Harness | UPDATE status.json (se já existe)"
  ansible.builtin.shell: |
    set -o pipefail
    http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
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
  environment:
    HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
  register: hfs_status_update_http
  when: >
    (hfs_status_create_http.stdout | default('') | trim) in ['409','400']
    or ((hfs_status_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
  failed_when: false
  changed_when: (hfs_status_update_http.stdout | default('') | trim) is match('^2..$')

- name: "Harness | Falhar status se create e update não deram certo"
  ansible.builtin.fail:
    msg: |
      Falha ao enviar status.json
      create_http={{ hfs_status_create_http.stdout | default('') | trim }}
      create_body={{ hfs_status_create_body.stdout | default('') }}
      update_http={{ hfs_status_update_http.stdout | default('') | trim }}
  when: >
    not ((hfs_status_create_http.stdout | default('') | trim) is match('^2..$'))
    and not ((hfs_status_update_http.stdout | default('') | trim) is match('^2..$'))

# -----------------------------------------------------------------------------
# 5) Upload/Upsert LOG (POST -> se 409 ou DUPLICATE faz PUT)
# -----------------------------------------------------------------------------
- name: "Harness | Definir identifiers do LOG"
  ansible.builtin.set_fact:
    hfs_log_name: "{{ hfs_log_filename }}"
    hfs_log_parent_identifier: "{{ leaf_identifier_final }}"
    hfs_log_identifier_raw: "{{ ('log_' ~ deployment_ref_identifier_final ~ '_' ~ leaf_identifier_final) | regex_replace('[^A-Za-z0-9_]', '_') }}"

- name: "Harness | Limitar identifier LOG a 128"
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

    - name: "Harness | CREATE pipeline.log"
      ansible.builtin.shell: |
        set -o pipefail
        http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_log_create.out -w "%{http_code}" \
          --request POST \
          "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
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
      environment:
        HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
      register: hfs_log_create_http
      failed_when: false
      changed_when: (hfs_log_create_http.stdout | default('') | trim) is match('^2..$')

    - name: "Harness | Ler body log CREATE"
      ansible.builtin.shell: "cat /tmp/hfs_log_create.out 2>/dev/null || true"
      args:
        executable: /bin/bash
      register: hfs_log_create_body
      changed_when: false
      failed_when: false

    - name: "Harness | UPDATE pipeline.log (se já existe)"
      ansible.builtin.shell: |
        set -o pipefail
        http="$(curl --http1.1 -sS {{ hfs_retry_opts }} -o /tmp/hfs_log_update.out -w "%{http_code}" \
          --request PUT \
          "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: ${HARNESS_X_API_KEY}" -H "Expect:" \
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
      environment:
        HARNESS_X_API_KEY: "{{ harness_api_key_resolved }}"
      register: hfs_log_update_http
      when: >
        (hfs_log_create_http.stdout | default('') | trim) in ['409','400']
        or ((hfs_log_create_body.stdout | default('') | string) is regex('.*DUPLICATE_FIELD.*'))
      failed_when: false
      changed_when: (hfs_log_update_http.stdout | default('') | trim) is match('^2..$')

    - name: "Harness | Falhar LOG se create e update não deram certo"
      ansible.builtin.fail:
        msg: |
          Falha ao enviar pipeline.log
          create_http={{ hfs_log_create_http.stdout | default('') | trim }}
          create_body={{ hfs_log_create_body.stdout | default('') }}
          update_http={{ hfs_log_update_http.stdout | default('') | trim }}
      when: >
        not ((hfs_log_create_http.stdout | default('') | trim) is match('^2..$'))
        and not ((hfs_log_update_http.stdout | default('') | trim) is match('^2..$'))

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_raw }}.log"
        state: absent
      changed_when: false
      failed_when: false
