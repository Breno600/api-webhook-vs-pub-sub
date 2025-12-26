---
# ansible/harness_filestore_upload.yml
#
# Task-file (para include_tasks). Não é playbook.
#
# REQUER:
#   - harness_base_url                 (ou env HARNESS_BASE_URL)
#   - harness_account_identifier       (ou env HARNESS_ACCOUNT_IDENTIFIER)
#   - harness_org_identifier           (ou env HARNESS_ORG_IDENTIFIER)
#   - harness_project_identifier       (ou env HARNESS_PROJECT_IDENTIFIER)
#   - harness_x_api_key                (ou env HARNESS_X_API_KEY)
#   - filestore_env_resolved           (ex: dev)
#   - deployment_ref_resolved          (ex: DEV00001)
#   - status_dir_resolved              (dir local onde existem status.json e pipeline.log)
#
# OPCIONAL:
#   - hfs_stage_label                  (default: pre_deploy)
#   - stage_label                      (fallback)
#   - machine_name_resolved / machine_name / MACHINE (fallback: inventory_hostname)
#   - hfs_run_id / run_id / RUN_ID     (fallback: epoch)
#   - hfs_extra_folder_suffix          (se quiser sufixar a pasta por algo extra)

# -----------------------------------------------------------------------------
- name: "Harness | Resolver variáveis base (sem recursão e sem vazar token)"
  ansible.builtin.set_fact:
    hfs_base_url_resolved: >-
      {{
        (
          harness_base_url
          | default(lookup('env','HARNESS_BASE_URL') | default(''))
          | trim
        ) | regex_replace('/+$','')
      }}
    hfs_account_identifier_resolved: >-
      {{
        harness_account_identifier
        | default(lookup('env','HARNESS_ACCOUNT_IDENTIFIER') | default(''))
        | trim
      }}
    hfs_org_identifier_resolved: >-
      {{
        harness_org_identifier
        | default(lookup('env','HARNESS_ORG_IDENTIFIER') | default(''))
        | trim
      }}
    hfs_project_identifier_resolved: >-
      {{
        harness_project_identifier
        | default(lookup('env','HARNESS_PROJECT_IDENTIFIER') | default(''))
        | trim
      }}
    hfs_x_api_key_resolved: >-
      {{
        harness_x_api_key
        | default(lookup('env','HARNESS_X_API_KEY') | default(''))
        | trim
      }}

- name: "Harness | Validar vars mínimas"
  ansible.builtin.assert:
    that:
      - hfs_base_url_resolved | length > 0
      - hfs_account_identifier_resolved | length > 0
      - hfs_org_identifier_resolved | length > 0
      - hfs_project_identifier_resolved | length > 0
      - hfs_x_api_key_resolved | length > 0
      - (filestore_env_resolved | default('') | trim) | length > 0
      - (deployment_ref_resolved | default('') | trim) | length > 0
      - (status_dir_resolved | default('') | trim) | length > 0
    fail_msg: >-
      Variáveis obrigatórias faltando para Harness File Store.
      Confira: harness_* + filestore_env_resolved + deployment_ref_resolved + status_dir_resolved.

# -----------------------------------------------------------------------------
- name: "Harness | Defaults/normalizações (stage, máquina, run_id)"
  ansible.builtin.set_fact:
    hfs_stage_label_resolved: >-
      {{
        (
          hfs_stage_label
          | default(stage_label | default(lookup('env','STAGE') | default('pre_deploy')))
          | trim
        )
      }}
    hfs_machine_name_raw: >-
      {{
        machine_name_resolved
        | default(machine_name | default(lookup('env','MACHINE') | default(inventory_hostname)))
        | trim
      }}
    hfs_run_id_resolved: >-
      {{
        (
          hfs_run_id
          | default(run_id | default(lookup('env','RUN_ID') | default(ansible_date_time.epoch)))
          | string
          | trim
        )
      }}

- name: "Harness | Identifiers SAFE (simples e compatível)"
  ansible.builtin.set_fact:
    hfs_env_name: "{{ filestore_env_resolved | trim }}"
    hfs_tag_name: "{{ deployment_ref_resolved | trim }}"
    hfs_machine_id: >-
      {{
        (hfs_machine_name_raw | lower)
        | regex_replace('[^a-z0-9_\\-]','_')
        | regex_replace('-','_')
      }}
    hfs_stage_id: >-
      {{
        (hfs_stage_label_resolved | lower)
        | regex_replace('[^a-z0-9_\\-]','_')
        | regex_replace('-','_')
      }}

- name: "Harness | Montar nomes/ids de pastas (Root/env/TAG/machine_stage)"
  ansible.builtin.set_fact:
    hfs_root_identifier: "Root"
    hfs_env_folder_name: "{{ hfs_env_name }}"
    hfs_env_folder_identifier: >-
      {{
        (hfs_env_name | regex_replace('[^A-Za-z0-9_]','_'))
      }}
    hfs_tag_folder_name: "{{ hfs_tag_name }}"
    hfs_tag_folder_identifier: >-
      {{
        (hfs_tag_name | regex_replace('[^A-Za-z0-9_]','_'))
      }}
    hfs_machine_stage_folder_name: >-
      {{
        (hfs_machine_id ~ '_' ~ hfs_stage_id ~ (('_' ~ hfs_extra_folder_suffix) if (hfs_extra_folder_suffix is defined and (hfs_extra_folder_suffix|trim|length>0)) else ''))
      }}
    hfs_machine_stage_folder_identifier: >-
      {{
        (
          (hfs_machine_id ~ '_' ~ hfs_stage_id ~ (('_' ~ hfs_extra_folder_suffix) if (hfs_extra_folder_suffix is defined and (hfs_extra_folder_suffix|trim|length>0)) else ''))
          | regex_replace('[^A-Za-z0-9_]','_')
        )
      }}

# -----------------------------------------------------------------------------
- name: "Harness | Paths locais dos arquivos"
  ansible.builtin.set_fact:
    hfs_status_json_local_path: "{{ status_dir_resolved | trim }}/status.json"
    hfs_pipeline_log_local_path: "{{ status_dir_resolved | trim }}/pipeline.log"

- name: "Harness | Validar existência dos arquivos locais"
  ansible.builtin.stat:
    path: "{{ item }}"
  register: hfs_local_files_stat
  loop:
    - "{{ hfs_status_json_local_path }}"
    - "{{ hfs_pipeline_log_local_path }}"

- name: "Harness | Falhar se algum arquivo local não existir"
  ansible.builtin.fail:
    msg: "Arquivo local não encontrado: {{ item.stat.path }}"
  when: not item.stat.exists
  loop: "{{ hfs_local_files_stat.results }}"

# -----------------------------------------------------------------------------
- name: "Harness | Montar URL base do File Store"
  ansible.builtin.set_fact:
    hfs_file_store_url: >-
      {{
        hfs_base_url_resolved
        ~ '/ng/api/file-store'
        ~ '?accountIdentifier=' ~ hfs_account_identifier_resolved
        ~ '&orgIdentifier=' ~ hfs_org_identifier_resolved
        ~ '&projectIdentifier=' ~ hfs_project_identifier_resolved
      }}

- name: "Harness | Debug (sem token)"
  ansible.builtin.debug:
    msg:
      - "hfs_file_store_url = {{ hfs_file_store_url }}"
      - "Folders: Root/{{ hfs_env_folder_name }}/{{ hfs_tag_folder_name }}/{{ hfs_machine_stage_folder_name }}"
      - "Locais: {{ hfs_status_json_local_path }}, {{ hfs_pipeline_log_local_path }}"
      - "run_id={{ hfs_run_id_resolved }} stage={{ hfs_stage_label_resolved }} machine={{ hfs_machine_name_raw }}"

# -----------------------------------------------------------------------------
# Criação de pastas: aceita DUPLICATE_FIELD como OK
- name: "Harness | Garantir folders (Root -> ENV -> TAG -> MACHINE_STAGE)"
  ansible.builtin.shell: |
    set -euo pipefail
    url="{{ hfs_file_store_url }}"
    parent="{{ item.parent }}"
    name="{{ item.name }}"
    identifier="{{ item.identifier }}"
    tmp="$(mktemp)"

    http="$(curl -sS -o "$tmp" -w "%{http_code}" -X POST "$url" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" \
      -F "type=FOLDER" \
      -F "parentIdentifier=$parent" \
      -F "name=$name" \
      -F "identifier=$identifier")"

    cat "$tmp"
    echo
    echo "__HTTP_CODE__=$http"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ hfs_x_api_key_resolved }}"
  register: hfs_folder_create
  changed_when: false
  failed_when: false
  loop:
    - { parent: "{{ hfs_root_identifier }}",              name: "{{ hfs_env_folder_name }}",           identifier: "{{ hfs_env_folder_identifier }}" }
    - { parent: "{{ hfs_env_folder_identifier }}",       name: "{{ hfs_tag_folder_name }}",           identifier: "{{ hfs_tag_folder_identifier }}" }
    - { parent: "{{ hfs_tag_folder_identifier }}",       name: "{{ hfs_machine_stage_folder_name }}", identifier: "{{ hfs_machine_stage_folder_identifier }}" }

- name: "Harness | Validar resultado da criação de folders"
  ansible.builtin.assert:
    that:
      - http_code in ok_codes or is_duplicate
    fail_msg: |-
      Falha ao criar/garantir folder no Harness File Store.
      folder_name={{ item.item.name }}
      parentIdentifier={{ item.item.parent }}
      http_code={{ http_code }}
      body={{ body }}
  vars:
    ok_codes: ["200","201","202","204"]
    http_code: "{{ (item.stdout | regex_search('__HTTP_CODE__=([0-9]{3})','\\1')) | default('') }}"
    body: "{{ (item.stdout | regex_replace('(?s)\\n__HTTP_CODE__=.*$','') | trim) }}"
    is_duplicate: "{{ (http_code == '400') and ((body | regex_search('\"code\"\\s*:\\s*\"DUPLICATE_FIELD\"')) is not none) }}"
  loop: "{{ hfs_folder_create.results }}"

# -----------------------------------------------------------------------------
# Upload de arquivos com nome ÚNICO para nunca bater DUPLICATE_FIELD
- name: "Harness | Montar nomes remotos únicos (concat TAG + run_id)"
  ansible.builtin.set_fact:
    hfs_remote_files:
      - local_path: "{{ hfs_status_json_local_path }}"
        remote_name: "status_{{ hfs_tag_name }}_{{ hfs_run_id_resolved }}.json"
      - local_path: "{{ hfs_pipeline_log_local_path }}"
        remote_name: "pipeline_{{ hfs_tag_name }}_{{ hfs_run_id_resolved }}.log"

- name: "Harness | Upload arquivos para folder MACHINE_STAGE"
  ansible.builtin.shell: |
    set -euo pipefail
    url="{{ hfs_file_store_url }}"
    parent="{{ hfs_machine_stage_folder_identifier }}"
    name="{{ item.remote_name }}"
    # identifier não pode ter '.' -> troca por '_'
    identifier="{{ (item.remote_name | regex_replace('[^A-Za-z0-9_]','_')) }}"
    file_path="{{ item.local_path }}"
    tmp="$(mktemp)"

    http="$(curl -sS -o "$tmp" -w "%{http_code}" -X POST "$url" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" \
      -F "type=FILE" \
      -F "parentIdentifier=$parent" \
      -F "name=$name" \
      -F "identifier=$identifier" \
      -F "file=@${file_path}")"

    cat "$tmp"
    echo
    echo "__HTTP_CODE__=$http"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ hfs_x_api_key_resolved }}"
  register: hfs_files_upload
  changed_when: false
  failed_when: false
  loop: "{{ hfs_remote_files }}"

- name: "Harness | Validar upload dos arquivos"
  ansible.builtin.assert:
    that:
      - http_code in ok_codes
    fail_msg: |-
      Falha ao enviar arquivo para o Harness File Store.
      remote_name={{ item.item.remote_name }}
      parentIdentifier={{ hfs_machine_stage_folder_identifier }}
      http_code={{ http_code }}
      body={{ body }}
  vars:
    ok_codes: ["200","201","202","204"]
    http_code: "{{ (item.stdout | regex_search('__HTTP_CODE__=([0-9]{3})','\\1')) | default('') }}"
    body: "{{ (item.stdout | regex_replace('(?s)\\n__HTTP_CODE__=.*$','') | trim) }}"
  loop: "{{ hfs_files_upload.results }}"
