---
# Harness File Store upload com DEBUG REAL (mostra HTTP code + body) e FAIL se não 2xx
# Requer ENV: HARNESS_ENDPOINT, HARNESS_ACCOUNT_ID, HARNESS_ORG_ID, HARNESS_PROJECT_ID
# e HARNESS_X_API_KEY (preferido) OU HARNESS_API_KEY

- name: "Harness | Resolver base via ENV (parte 1)"
  ansible.builtin.set_fact:
    hf_stage: "{{ (hf_stage_name | default(stage_name | default('unknown')) | string | trim) }}"
    hf_machine: "{{ (current_machine | default(machine_name | default('controller')) | string | trim) }}"
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

- name: "Harness | DEBUG - variáveis principais"
  ansible.builtin.debug:
    msg:
      - "hf_can_upload={{ hf_can_upload }}"
      - "hf_endpoint={{ hf_endpoint }}"
      - "hf_account={{ hf_account }}"
      - "hf_org={{ hf_org }}"
      - "hf_project={{ hf_project }}"
      - "hf_deploy_ref={{ hf_deploy_ref }}"
      - "hf_env={{ hf_env }}"
      - "hf_stage={{ hf_stage }}"
      - "hf_machine={{ hf_machine }}"
      - "pat_len={{ hf_pat | default('') | length }}"
      - "hf_base_dir={{ hf_base_dir | default('') }}"
  changed_when: false

- name: "Harness | Parar aqui se não puder fazer upload"
  ansible.builtin.debug:
    msg:
      - "Pulando upload (HARNESS_* incompleto ou deploy/machine vazio)."
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

- name: "Harness | DEBUG - nomes finais (procure no File Store por isso)"
  ansible.builtin.debug:
    msg:
      - "LOG_NAME={{ hf_log_name }}"
      - "STATUS_NAME={{ hf_status_name }}"
  when: hf_can_upload
  changed_when: false

- name: "Harness | Tags (lista)"
  ansible.builtin.set_fact:
    hf_tags_list: >-
      {{
        (
          [ status_tag_value | default('') ]
          + (extra_tags | default([]))
          + [
              (hf_deploy_ref | lower) ~ '-stage-' ~ hf_stage,
              (hf_deploy_ref | lower) ~ '-machine-' ~ (hf_machine | lower),
              (hf_deploy_ref | lower) ~ '-env-' ~ hf_env
            ]
        )
        | map('string') | map('trim') | reject('equalto','') | list | unique
      }}
  when: hf_can_upload

- name: "Harness | Tags (string) - SANITIZADO"
  ansible.builtin.set_fact:
    hf_tags: "{{ (hf_tags_list | map('regex_replace', '[^a-zA-Z0-9_.-]', '-') | list) | join(',') }}"
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
# STATUS.JSON via FIND (sem machine_status_file)
# =========================

- name: "Harness | Definir diretório base de status (safe)"
  ansible.builtin.set_fact:
    hf_status_root: "{{ status_dir_resolved | default(status_dir | default('')) }}"
  when: hf_can_upload

- name: "Harness | DEBUG - status root"
  ansible.builtin.debug:
    msg:
      - "hf_status_root={{ hf_status_root }}"
  when: hf_can_upload
  changed_when: false

- name: "Harness | Descobrir status.json local"
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

- name: "Harness | DEBUG - status escolhido"
  ansible.builtin.debug:
    msg:
      - "hf_local_status_file={{ hf_local_status_file | default('') }}"
      - "found={{ (hf_status_find.files | length) if (hf_status_find.files is defined) else 0 }}"
  when: hf_can_upload
  changed_when: false

- name: "Harness | Ler status.json (não falhar)"
  ansible.builtin.slurp:
    src: "{{ hf_local_status_file }}"
  register: hf_status_slurp
  when: hf_can_upload and (hf_local_status_file | default('') | length > 0)
  failed_when: false

- name: "Harness | Escrever STATUS temp"
  ansible.builtin.copy:
    dest: "{{ hf_tmpdir.path }}/{{ hf_status_name }}"
    mode: "0644"
    content: "{{ (hf_status_slurp.content | b64decode) if (hf_status_slurp.content is defined) else '{}' }}"
  when: hf_can_upload

# =========================
# UPLOADS (com output visível)
# =========================

- name: "Harness | Upload LOG (mostra HTTP + body)"
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
    echo "RESPONSE_BODY_BEGIN"
    cat "$RESP" 2>/dev/null || true
    echo
    echo "RESPONSE_BODY_END"
  args:
    executable: /bin/bash
  register: hf_upload_log
  when: hf_can_upload
  changed_when: false

- name: "Harness | Extrair HTTP_CODE LOG"
  ansible.builtin.set_fact:
    hf_log_http_code: "{{ hf_upload_log.stdout | regex_search('HTTP_CODE=([0-9]{3})', '\\1') | default('') }}"
  when: hf_can_upload

- name: "Harness | Falhar se LOG não for 2xx"
  ansible.builtin.fail:
    msg: |
      Upload LOG falhou (HTTP={{ hf_log_http_code }}).
      Saída completa:
      {{ hf_upload_log.stdout }}
  when: hf_can_upload and (hf_log_http_code | int < 200 or hf_log_http_code | int >= 300)

- name: "Harness | Upload STATUS (mostra HTTP + body)"
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
    echo "RESPONSE_BODY_BEGIN"
    cat "$RESP" 2>/dev/null || true
    echo
    echo "RESPONSE_BODY_END"
  args:
    executable: /bin/bash
  register: hf_upload_status
  when: hf_can_upload
  changed_when: false

- name: "Harness | Extrair HTTP_CODE STATUS"
  ansible.builtin.set_fact:
    hf_status_http_code: "{{ hf_upload_status.stdout | regex_search('HTTP_CODE=([0-9]{3})', '\\1') | default('') }}"
  when: hf_can_upload

- name: "Harness | Falhar se STATUS não for 2xx"
  ansible.builtin.fail:
    msg: |
      Upload STATUS falhou (HTTP={{ hf_status_http_code }}).
      Saída completa:
      {{ hf_upload_status.stdout }}
  when: hf_can_upload and (hf_status_http_code | int < 200 or hf_status_http_code | int >= 300)
