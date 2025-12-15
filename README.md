---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina)
# - Cria (se não existir) pasta raiz -> env -> deployment_ref_folder
# - Faz UPSERT do status.json e do log da máquina
# - Aplica tags (status_tag_value + extra_tags + tags fixas)
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

- name: "Harness | Definir defaults + normalizações"
  ansible.builtin.set_fact:
    harness_account_id_resolved: "{{ harness_account_id | default('fgDto6qoTT6ctfZS9eWbEw') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('Fiserv') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('sitef') }}"
    harness_filestore_root_identifier_resolved: "{{ harness_filestore_root_identifier | default('Root') }}"

    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    # pasta ENV sempre em lower
    env_lower: "{{ (filestore_env | string | trim | lower) }}"

    # folder visual do deployment (ex: DEV000001) - nome pode ter uppercase
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"

    # lower para nomes/identifiers/tags
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"

- name: "Harness | Normalizar identifiers (safe)"
  ansible.builtin.set_fact:
    # regra "segura" pra identifier: lower + troca qualquer coisa fora [a-z0-9_-] por "-"
    env_identifier: "{{ env_lower | regex_replace('[^a-z0-9_-]+', '-') }}"
    deployment_ref_identifier: "{{ (deployment_ref_folder | lower) | regex_replace('[^a-z0-9_-]+', '-') }}"

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


# -----------------------------------------------------------------------------
# Helpers: valida http_code + debug body quando falhar
# -----------------------------------------------------------------------------
- name: "Harness | Definir http_codes aceitos"
  ansible.builtin.set_fact:
    hfs_ok_create_codes: ["200","201","202","409"]
    hfs_ok_update_codes: ["200","201","202"]

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
      -F "identifier={{ env_identifier }}" \
      -F "tags={{ harness_tags | to_json }}"
  args:
    executable: /bin/bash
  register: hfs_env_create_http
  changed_when: (hfs_env_create_http.stdout | default('')) is match('^2..$')
  failed_when: (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes
  no_log: true

- name: "Harness | DEBUG body ENV (somente se falhar)"
  ansible.builtin.shell: "sed -n '1,200p' /tmp/hfs_env_create.out || true"
  register: hfs_env_create_body
  changed_when: false
  failed_when: false
  when: (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes

- name: "Harness | DEBUG http_code/body ENV"
  ansible.builtin.debug:
    msg:
      - "ENV create http_code={{ hfs_env_create_http.stdout | default('') }}"
      - "{{ hfs_env_create_body.stdout | default('') }}"
  when: (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes


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
      -F "parentIdentifier={{ env_identifier }}" \
      -F "identifier={{ deployment_ref_identifier }}" \
      -F "tags={{ harness_tags | to_json }}"
  args:
    executable: /bin/bash
  register: hfs_ref_create_http
  changed_when: (hfs_ref_create_http.stdout | default('')) is match('^2..$')
  failed_when: (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes
  no_log: true

- name: "Harness | DEBUG body REF (somente se falhar)"
  ansible.builtin.shell: "sed -n '1,200p' /tmp/hfs_ref_create.out || true"
  register: hfs_ref_create_body
  changed_when: false
  failed_when: false
  when: (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes

- name: "Harness | DEBUG http_code/body REF"
  ansible.builtin.debug:
    msg:
      - "REF create http_code={{ hfs_ref_create_http.stdout | default('') }}"
      - "{{ hfs_ref_create_body.stdout | default('') }}"
  when: (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes


# -----------------------------------------------------------------------------
# 3) Upload/Upsert STATUS JSON
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do status JSON"
  ansible.builtin.set_fact:
    hfs_status_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.json"
    hfs_status_identifier: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-json"
    hfs_status_parent_identifier: "{{ deployment_ref_identifier }}"

- name: "Harness | Tentar CREATE status JSON"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@{{ machine_status_file }}"
  args:
    executable: /bin/bash
  register: hfs_status_create_http
  changed_when: (hfs_status_create_http.stdout | default('')) is match('^2..$')
  failed_when: (hfs_status_create_http.stdout | default('')) not in hfs_ok_create_codes
  no_log: true

- name: "Harness | UPDATE status JSON (se create retornou 409)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@{{ machine_status_file }}"
  args:
    executable: /bin/bash
  register: hfs_status_update_http
  when: (hfs_status_create_http.stdout | default('')) == '409'
  changed_when: (hfs_status_update_http.stdout | default('')) is match('^2..$')
  failed_when: (hfs_status_update_http.stdout | default('')) not in hfs_ok_update_codes
  no_log: true

- name: "Harness | DEBUG body STATUS (somente se falhar)"
  ansible.builtin.shell: |
    echo "CREATE:"; sed -n '1,200p' /tmp/hfs_status_create.out || true
    echo "UPDATE:"; sed -n '1,200p' /tmp/hfs_status_update.out || true
  register: hfs_status_body
  changed_when: false
  failed_when: false
  when:
    - (hfs_status_create_http.stdout | default('')) not in hfs_ok_create_codes
      or ((hfs_status_create_http.stdout | default('')) == '409' and (hfs_status_update_http.stdout | default('')) not in hfs_ok_update_codes)

- name: "Harness | DEBUG http_code/body STATUS"
  ansible.builtin.debug:
    msg:
      - "STATUS create http_code={{ hfs_status_create_http.stdout | default('') }}"
      - "STATUS update http_code={{ hfs_status_update_http.stdout | default('') }}"
      - "{{ hfs_status_body.stdout | default('') }}"
  when: hfs_status_body is defined


# -----------------------------------------------------------------------------
# 4) Upload/Upsert LOG
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do LOG"
  ansible.builtin.set_fact:
    hfs_log_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.log"
    hfs_log_identifier: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-log"
    hfs_log_parent_identifier: "{{ deployment_ref_identifier }}"

- name: "Harness | Upload do LOG (create/update) com cleanup garantido"
  block:
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
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F "tags={{ harness_tags | to_json }}" \
          -F "content=@/tmp/{{ hfs_log_name }}"
      args:
        executable: /bin/bash
      register: hfs_log_create_http
      changed_when: (hfs_log_create_http.stdout | default('')) is match('^2..$')
      failed_when: (hfs_log_create_http.stdout | default('')) not in hfs_ok_create_codes
      no_log: true

    - name: "Harness | UPDATE log (se create retornou 409)"
      ansible.builtin.shell: |
        set -e
        curl -sS -o /tmp/hfs_log_update.out -w "%{http_code}" \
          --request PUT \
          "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F "tags={{ harness_tags | to_json }}" \
          -F "content=@/tmp/{{ hfs_log_name }}"
      args:
        executable: /bin/bash
      register: hfs_log_update_http
      when: (hfs_log_create_http.stdout | default('')) == '409'
      changed_when: (hfs_log_update_http.stdout | default('')) is match('^2..$')
      failed_when: (hfs_log_update_http.stdout | default('')) not in hfs_ok_update_codes
      no_log: true

    - name: "Harness | DEBUG body LOG (somente se falhar)"
      ansible.builtin.shell: |
        echo "CREATE:"; sed -n '1,200p' /tmp/hfs_log_create.out || true
        echo "UPDATE:"; sed -n '1,200p' /tmp/hfs_log_update.out || true
      register: hfs_log_body
      changed_when: false
      failed_when: false
      when:
        - (hfs_log_create_http.stdout | default('')) not in hfs_ok_create_codes
          or ((hfs_log_create_http.stdout | default('')) == '409' and (hfs_log_update_http.stdout | default('')) not in hfs_ok_update_codes)

    - name: "Harness | DEBUG http_code/body LOG"
      ansible.builtin.debug:
        msg:
          - "LOG create http_code={{ hfs_log_create_http.stdout | default('') }}"
          - "LOG update http_code={{ hfs_log_update_http.stdout | default('') }}"
          - "{{ hfs_log_body.stdout | default('') }}"
      when: hfs_log_body is defined

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/{{ hfs_log_name }}"
        state: absent
      changed_when: false
      failed_when: false
