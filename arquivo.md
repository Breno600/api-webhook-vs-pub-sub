---
# Upload para Harness File Store (sem loop / recursão)
# Requer ENV: HARNESS_ENDPOINT, HARNESS_ACCOUNT_ID, HARNESS_ORG_ID, HARNESS_PROJECT_ID e HARNESS_API_KEY (ou HARNESS_X_API_KEY)

- name: "Harness | Resolver base via ENV (parte 1)"
  ansible.builtin.set_fact:
    hf_stage: "{{ (hf_stage_name | default(stage_name | default('unknown')) | string | trim) }}"
    hf_machine: "{{ (current_machine | default(machine_name | default('')) | string | trim) }}"
    hf_deploy_ref: "{{ (deployment_ref | default(lookup('env','GIT_TAG') | default('')) | string | trim) }}"
    hf_env: "{{ (filestore_env | default(lookup('env','FILESTORE_ENV') | default('dev')) | string | trim | lower) }}"

    hf_endpoint: "{{ lookup('env','HARNESS_ENDPOINT') | default('https://harness.onefiserv.net', true) }}"
    hf_account: "{{ lookup('env','HARNESS_ACCOUNT_ID') | default('', true) }}"
    hf_org: "{{ lookup('env','HARNESS_ORG_ID') | default('', true) }}"
    hf_project: "{{ lookup('env','HARNESS_PROJECT_ID') | default('', true) }}"

    hf_pat: >-
      {{
        (lookup('env','HARNESS_X_API_KEY') | default('', true))
        if (lookup('env','HARNESS_X_API_KEY') | default('', true) | length > 0)
        else (lookup('env','HARNESS_API_KEY') | default('', true))
      }}

- name: "Harness | Resolver base via ENV (parte 2)"
  ansible.builtin.set_fact:
    hf_base_dir: "{{ (filestore_base_dir | default(hf_env ~ '/' ~ hf_deploy_ref) | string | trim) }}"
  when:
    - hf_env is defined
    - hf_deploy_ref is defined

- name: "Harness | Validar se pode fazer upload"
  ansible.builtin.set_fact:
    hf_can_upload: >-
      {{
        (hf_pat | default('') | length) > 0 and
        (hf_endpoint | default('') | length) > 0 and
        (hf_account | default('') | length) > 0 and
        (hf_org | default('') | length) > 0 and
        (hf_project | default('') | length) > 0 and
        (hf_deploy_ref | default('') | length) > 0 and
        (hf_machine | default('') | length) > 0
      }}

- name: "Harness | Pular upload se faltar credenciais (não falha pipeline)"
  ansible.builtin.debug:
    msg:
      - "Pulando upload (HARNESS_* incompleto ou deploy/machine vazio)."
      - "hf_can_upload={{ hf_can_upload }}"
      - "endpoint={{ hf_endpoint | default('') }}"
      - "account={{ hf_account | default('') }}"
      - "org={{ hf_org | default('') }}"
      - "project={{ hf_project | default('') }}"
      - "deploy_ref={{ hf_deploy_ref | default('') }}"
      - "machine={{ hf_machine | default('') }}"
      - "pat_present={{ (hf_pat | default('') | length) > 0 }}"
  when: not hf_can_upload
  changed_when: false

- name: "Harness | Resolver paths (log/status)"
  ansible.builtin.set_fact:
    hf_log_path: >-
      {{
        filestore_log_path
        | default(hf_base_dir ~ '/' ~ (hf_deploy_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '.log')
        | string | trim
      }}
    hf_status_path: >-
      {{
        filestore_status_path
        | default(hf_base_dir ~ '/' ~ (hf_deploy_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '.json')
        | string | trim
      }}
  when: hf_can_upload

- name: "Harness | Resolver nomes (a partir dos paths)"
  ansible.builtin.set_fact:
    hf_log_name: "{{ (hf_log_path | regex_replace('^/+','')) | replace('/','__') }}"
    hf_status_name: "{{ (hf_status_path | regex_replace('^/+','')) | replace('/','__') }}"
  when: hf_can_upload

- name: "Harness | Tags (lista)"
  ansible.builtin.set_fact:
    hf_tags_list: >-
      {{
        (
          [ status_tag_value | default('') ]
          + (extra_tags | default([]))
          + [
              (hf_deploy_ref | lower) ~ ':stage:' ~ hf_stage,
              (hf_deploy_ref | lower) ~ ':machine:' ~ (hf_machine | lower),
              (hf_deploy_ref | lower) ~ ':env:' ~ hf_env
            ]
        )
        | map('string') | map('trim') | reject('equalto','') | list | unique
      }}
  when: hf_can_upload

- name: "Harness | Tags (string)"
  ansible.builtin.set_fact:
    hf_tags: "{{ hf_tags_list | join(',') }}"
  when: hf_can_upload

- name: "Harness | Temp dir (controller)"
  ansible.builtin.tempfile:
    state: directory
    suffix: hf_upload
  register: hf_tmpdir
  when: hf_can_upload

- name: "Harness | Escrever LOG temp"
  ansible.builtin.copy:
    dest: "{{ hf_tmpdir.path }}/{{ hf_log_name }}"
    mode: "0644"
    content: "{{ log_content | default('') }}"
  when: hf_can_upload

# =========================
# STATUS.JSON (SEM machine_status_file)
# =========================

- name: "Harness | Definir diretório base de status (safe)"
  ansible.builtin.set_fact:
    hf_status_root: "{{ status_dir_resolved | default(status_dir | default('')) }}"
  when: hf_can_upload

- name: "Harness | Descobrir status.json local (sem usar machine_status_file)"
  ansible.builtin.find:
    paths: "{{ hf_status_root }}"
    recurse: true
    file_type: file
    patterns:
      - "status.json"
      - "*status*.json"
  register: hf_status_find
  when: hf_can_upload and (hf_status_root | length > 0)
  failed_when: false

- name: "Harness | Escolher status.json mais recente (preferindo a máquina)"
  ansible.builtin.set_fact:
    hf_local_status_file: >-
      {{
        (
          (
            hf_status_find.files
            | selectattr('path', 'search', (hf_machine | default('')))
            | list
            | sort(attribute='mtime', reverse=true)
            | map(attribute='path')
            | list
            | first
          )
          | default(
              (
                hf_status_find.files
                | sort(attribute='mtime', reverse=true)
                | map(attribute='path')
                | list
                | first
              ),
              true
            )
        )
        | default('', true)
      }}
  when: hf_can_upload and (hf_status_find.files is defined) and (hf_status_find.files | length > 0)

- name: "Harness | Debug se não encontrou status.json"
  ansible.builtin.debug:
    msg:
      - "Não encontrei status.json para upload."
      - "hf_status_root={{ hf_status_root }}"
      - "hf_machine={{ hf_machine }}"
      - "arquivos_encontrados={{ (hf_status_find.files | length) if (hf_status_find.files is defined) else 0 }}"
  when: hf_can_upload and (hf_local_status_file | default('') | length == 0)
  changed_when: false

- name: "Harness | Ler status.json (não falhar)"
  ansible.builtin.slurp:
    src: "{{ hf_local_status_file }}"
  register: hf_status_slurp
  when: hf_can_upload and (hf_local_status_file | length > 0)
  failed_when: false

- name: "Harness | Escrever STATUS temp"
  ansible.builtin.copy:
    dest: "{{ hf_tmpdir.path }}/{{ hf_status_name }}"
    mode: "0644"
    content: "{{ (hf_status_slurp.content | b64decode) if (hf_status_slurp.content is defined) else '{}' }}"
  when: hf_can_upload

# =========================
# UPLOADS
# =========================

- name: "Harness | Upload LOG (não falha pipeline)"
  ansible.builtin.shell: |
    set -euo pipefail
    API="{{ hf_endpoint.rstrip('/') }}/ng/api/file-store?accountIdentifier={{ hf_account }}&orgIdentifier={{ hf_org }}&projectIdentifier={{ hf_project }}"
    FILE="{{ hf_tmpdir.path }}/{{ hf_log_name }}"
    RESP="{{ hf_tmpdir.path }}/resp_log.json"
    code=$(curl -sS -o "$RESP" -w "%{http_code}" -X POST "$API" \
      -H "x-api-key: {{ hf_pat }}" \
      -F "parentIdentifier=Root" \
      -F "name={{ hf_log_name }}" \
      -F "type=FILE" \
      -F "tags={{ hf_tags }}" \
      -F "file=@${FILE};type=text/plain" \
    || true)
    echo "HTTP_CODE=${code}"
    cat "$RESP" 2>/dev/null || true
    exit 0
  args:
    executable: /bin/bash
  changed_when: false
  failed_when: false
  when: hf_can_upload

- name: "Harness | Upload STATUS (não falha pipeline)"
  ansible.builtin.shell: |
    set -euo pipefail
    API="{{ hf_endpoint.rstrip('/') }}/ng/api/file-store?accountIdentifier={{ hf_account }}&orgIdentifier={{ hf_org }}&projectIdentifier={{ hf_project }}"
    FILE="{{ hf_tmpdir.path }}/{{ hf_status_name }}"
    RESP="{{ hf_tmpdir.path }}/resp_status.json"
    code=$(curl -sS -o "$RESP" -w "%{http_code}" -X POST "$API" \
      -H "x-api-key: {{ hf_pat }}" \
      -F "parentIdentifier=Root" \
      -F "name={{ hf_status_name }}" \
      -F "type=FILE" \
      -F "tags={{ hf_tags }}" \
      -F "file=@${FILE};type=application/json" \
    || true)
    echo "HTTP_CODE=${code}"
    cat "$RESP" 2>/dev/null || true
    exit 0
  args:
    executable: /bin/bash
  changed_when: false
  failed_when: false
  when: hf_can_upload
