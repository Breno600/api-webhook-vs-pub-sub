---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina)
# - Cria (se não existir) pasta raiz -> env -> deployment_ref
# - Faz UPSERT do status.json e do log da máquina
# - Tags enviadas com quoting seguro (evita 500 por multipart quebrado)
#
# SEGREDO (NÃO NO REPO):
#   HARNESS_X_API_KEY (env var)
#
# CORREÇÃO APLICADA:
# - Harness retorna 400 + code=DUPLICATE_FIELD quando a pasta já existe (ao invés de 409).
# - Agora, quando http_code=400 e o body contém DUPLICATE_FIELD, normalizamos para "409"
#   (ou seja: consideramos como "já existe" e seguimos).
# - Mesma normalização aplicada para:
#   * folder ENV
#   * folder DEPLOYMENT_REF
#   * CREATE de STATUS e LOG (para cair no fluxo de UPDATE)
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

- name: "Harness | Identifiers SAFE (HARNESS RULE: [A-Za-z][A-Za-z0-9_]{0,127})"
  ansible.builtin.set_fact:
    # troca qualquer coisa fora [A-Za-z0-9_] por "_"
    env_identifier: "{{ env_lower | regex_replace('[^A-Za-z0-9_]', '_') }}"
    deployment_ref_identifier: "{{ (deployment_ref_folder | string | trim | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    machine_identifier: "{{ (current_machine | string | trim | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"

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

# helper: tags json em variável (evita quebrar quoting)
- name: "Harness | Render tags JSON"
  ansible.builtin.set_fact:
    harness_tags_json: "{{ harness_tags | to_json }}"

# -----------------------------------------------------------------------------
# 1) Garantir folder ENV (filestore_env) embaixo da raiz
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (POST) com debug"
  ansible.builtin.shell: |
    set -euo pipefail

    http="$(curl -sS -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"

    # CORREÇÃO: quando já existe, Harness pode responder 400 + DUPLICATE_FIELD
    if [ "${http}" = "400" ] && grep -q 'DUPLICATE_FIELD' /tmp/hfs_env_create.out; then
      echo "409"
    else
      echo "${http}"
    fi
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
# 2) Garantir folder DEPLOYMENT_REF embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder DEPLOYMENT_REF (POST) com debug"
  ansible.builtin.shell: |
    set -euo pipefail

    http="$(curl -sS -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_identifier }}" \
      -F "identifier={{ deployment_ref_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"

    # CORREÇÃO: quando já existe, Harness pode responder 400 + DUPLICATE_FIELD
    if [ "${http}" = "400" ] && grep -q 'DUPLICATE_FIELD' /tmp/hfs_ref_create.out; then
      echo "409"
    else
      echo "${http}"
    fi
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
      Falha ao criar/garantir folder DEPLOYMENT_REF no Harness.
      http_code={{ hfs_ref_create_http.stdout | default('') }}
      body={{ hfs_ref_create_body.stdout | default('') }}
  when: (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes

# -----------------------------------------------------------------------------
# 3) Upload/Upsert STATUS JSON (POST -> se 409 faz PUT) + retry em 500
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do STATUS JSON"
  ansible.builtin.set_fact:
    hfs_status_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.json"
    hfs_status_identifier: "{{ deployment_ref_identifier }}_{{ machine_identifier }}_{{ env_identifier }}_json"
    hfs_status_parent_identifier: "{{ deployment_ref_identifier }}"

- name: "Harness | CREATE status (retry se 500)"
  ansible.builtin.shell: |
    set -euo pipefail

    http="$(curl -sS -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"

    # CORREÇÃO: quando já existe, pode vir 400 + DUPLICATE_FIELD (normaliza para 409 para cair no UPDATE)
    if [ "${http}" = "400" ] && grep -q 'DUPLICATE_FIELD' /tmp/hfs_status_create.out; then
      echo "409"
    else
      echo "${http}"
    fi
  args:
    executable: /bin/bash
  register: hfs_status_create_http
  changed_when: (hfs_status_create_http.stdout | default('')) is match('^2..$')
  failed_when: false
  no_log: true
  retries: 4
  delay: 2
  until: (hfs_status_create_http.stdout | default('')) != '500'

- name: "Harness | UPDATE status (se create retornou 409) (retry se 500)"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl -sS -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
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
  no_log: true
  retries: 4
  delay: 2
  until: (hfs_status_update_http.stdout | default('')) != '500'

- name: "Harness | DEBUG body STATUS (quando falhar)"
  ansible.builtin.shell: |
    echo "CREATE:"; sed -n '1,260p' /tmp/hfs_status_create.out || true
    echo "UPDATE:"; sed -n '1,260p' /tmp/hfs_status_update.out || true
  register: hfs_status_body
  changed_when: false
  failed_when: false
  when:
    - (hfs_status_create_http.stdout | default('')) not in hfs_ok_create_codes
      or ((hfs_status_create_http.stdout | default('')) == '409' and (hfs_status_update_http.stdout | default('')) not in hfs_ok_update_codes)

- name: "Harness | Falhar STATUS (com correlationId/body)"
  ansible.builtin.fail:
    msg: |
      Falha no upload do STATUS no Harness File Store.
      STATUS create http_code={{ hfs_status_create_http.stdout | default('') }}
      STATUS update http_code={{ hfs_status_update_http.stdout | default('') }}
      {{ hfs_status_body.stdout | default('') }}
  when:
    - (hfs_status_create_http.stdout | default('')) not in hfs_ok_create_codes
      or ((hfs_status_create_http.stdout | default('')) == '409' and (hfs_status_update_http.stdout | default('')) not in hfs_ok_update_codes)

# -----------------------------------------------------------------------------
# 4) Upload/Upsert LOG (POST -> se 409 faz PUT) + retry em 500
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do LOG"
  ansible.builtin.set_fact:
    hfs_log_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.log"
    hfs_log_identifier: "{{ deployment_ref_identifier }}_{{ machine_identifier }}_{{ env_identifier }}_log"
    hfs_log_parent_identifier: "{{ deployment_ref_identifier }}"

- name: "Harness | Upload do LOG (create/update) com cleanup garantido"
  block:
    - name: "Harness | Criar arquivo temporário do log no controller"
      ansible.builtin.copy:
        dest: "/tmp/{{ hfs_log_name }}"
        mode: "0600"
        content: "{{ log_content }}"

    - name: "Harness | CREATE log (retry se 500)"
      ansible.builtin.shell: |
        set -euo pipefail

        http="$(curl -sS -o /tmp/hfs_log_create.out -w "%{http_code}" \
          --request POST \
          "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/{{ hfs_log_name }}" \
        )"

        # CORREÇÃO: quando já existe, pode vir 400 + DUPLICATE_FIELD (normaliza para 409 para cair no UPDATE)
        if [ "${http}" = "400" ] && grep -q 'DUPLICATE_FIELD' /tmp/hfs_log_create.out; then
          echo "409"
        else
          echo "${http}"
        fi
      args:
        executable: /bin/bash
      register: hfs_log_create_http
      changed_when: (hfs_log_create_http.stdout | default('')) is match('^2..$')
      failed_when: false
      no_log: true
      retries: 4
      delay: 2
      until: (hfs_log_create_http.stdout | default('')) != '500'

    - name: "Harness | UPDATE log (se create retornou 409) (retry se 500)"
      ansible.builtin.shell: |
        set -euo pipefail
        http="$(curl -sS -o /tmp/hfs_log_update.out -w "%{http_code}" \
          --request PUT \
          "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/{{ hfs_log_name }}" \
        )"
        echo "${http}"
      args:
        executable: /bin/bash
      register: hfs_log_update_http
      when: (hfs_log_create_http.stdout | default('')) == '409'
      changed_when: (hfs_log_update_http.stdout | default('')) is match('^2..$')
      failed_when: false
      no_log: true
      retries: 4
      delay: 2
      until: (hfs_log_update_http.stdout | default('')) != '500'

    - name: "Harness | DEBUG body LOG (quando falhar)"
      ansible.builtin.shell: |
        echo "CREATE:"; sed -n '1,260p' /tmp/hfs_log_create.out || true
        echo "UPDATE:"; sed -n '1,260p' /tmp/hfs_log_update.out || true
      register: hfs_log_body
      changed_when: false
      failed_when: false
      when:
        - (hfs_log_create_http.stdout | default('')) not in hfs_ok_create_codes
          or ((hfs_log_create_http.stdout | default('')) == '409' and (hfs_log_update_http.stdout | default('')) not in hfs_ok_update_codes)

    - name: "Harness | Falhar LOG (com correlationId/body)"
      ansible.builtin.fail:
        msg: |
          Falha no upload do LOG no Harness File Store.
          LOG create http_code={{ hfs_log_create_http.stdout | default('') }}
          LOG update http_code={{ hfs_log_update_http.stdout | default('') }}
          {{ hfs_log_body.stdout | default('') }}
      when:
        - (hfs_log_create_http.stdout | default('')) not in hfs_ok_create_codes
          or ((hfs_log_create_http.stdout | default('')) == '409' and (hfs_log_update_http.stdout | default('')) not in hfs_ok_update_codes)

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/{{ hfs_log_name }}"
        state: absent
      changed_when: false
      failed_when: false
