# harness_filestore_upload.yml
---
# Upload para Harness File Store (robusto e SEM loop de variáveis)
# Usa somente variáveis de ambiente HARNESS_* para evitar recursão.

- name: "Harness | Resolver entradas via ENV (sem recursão)"
  ansible.builtin.set_fact:
    hf_stage_name_resolved: "{{ (hf_stage_name | default('unknown') | string | trim) }}"
    hf_machine: "{{ (current_machine | default(machine_name | default('')) | string | trim) }}"
    hf_deployment_ref: "{{ (deployment_ref | default(lookup('env','GIT_TAG') | default('')) | string | trim) }}"
    hf_env: "{{ (filestore_env | default('dev') | string | trim | lower) }}"
    hf_base_dir: "{{ (filestore_base_dir | default((filestore_env | default('dev')) ~ '/' ~ (deployment_ref | default(''))) | string | trim) }}"

    hf_endpoint: "{{ lookup('env','HARNESS_ENDPOINT') | default('https://harness.onefiserv.net', true) }}"
    hf_account: "{{ lookup('env','HARNESS_ACCOUNT_ID') | default('fgDto6qoTT6ctfZS9eWbEw', true) }}"
    hf_org: "{{ lookup('env','HARNESS_ORG_ID') | default('Fiserv', true) }}"
    hf_project: "{{ lookup('env','HARNESS_PROJECT_ID') | default('sitef', true) }}"

    # PAT: aceita HARNESS_X_API_KEY (preferido) ou HARNESS_API_KEY
    hf_pat: >-
      {{
        (lookup('env','HARNESS_X_API_KEY') | default('', true))
        if (lookup('env','HARNESS_X_API_KEY') | default('', true) | length > 0)
        else (lookup('env','HARNESS_API_KEY') | default('', true))
      }}

- name: "Harness | Calcular se pode fazer upload"
  ansible.builtin.set_fact:
    hf_can_upload: >-
      {{
        (hf_pat | length) > 0
        and (hf_account | length) > 0
        and (hf_org | length) > 0
        and (hf_project | length) > 0
        and (hf_endpoint | length) > 0
        and (hf_deployment_ref | length) > 0
        and (hf_machine | length) > 0
      }}

- name: "Harness | Debug mínimos"
  ansible.builtin.debug:
    msg:
      - "hf_can_upload={{ hf_can_upload }}"
      - "hf_endpoint={{ hf_endpoint }}"
      - "hf_account={{ hf_account }}"
      - "hf_org={{ hf_org }}"
      - "hf_project={{ hf_project }}"
      - "hf_deployment_ref={{ hf_deployment_ref }}"
      - "hf_machine={{ hf_machine }}"
      - "hf_stage={{ hf_stage_name_resolved }}"
  changed_when: false

- name: "Harness | Pular upload se faltar credenciais (sem falhar pipeline)"
  ansible.builtin.debug:
    msg: "Credenciais HARNESS_* ausentes/incompletas. Pulando upload."
  when: not hf_can_upload
  changed_when: false

- name: "Harness | Resolver paths + nomes safe"
  ansible.builtin.set_fact:
    hf_log_path: >-
      {{
        filestore_log_path
          | default(hf_base_dir ~ '/' ~ (hf_deployment_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage_name_resolved ~ '.log')
      }}
    hf_status_path: >-
      {{
        filestore_status_path
          | default(hf_base_dir ~ '/' ~ (hf_deployment_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage_name_resolved ~ '.json')
      }}
    hf_log_name: "{{ (hf_log_path | regex_replace('^/+','')) | replace('/','__') }}"
    hf_status_name: "{{ (hf_status_path | regex_replace('^/+','')) | replace('/','__') }}"
  when: hf_can_upload

- name: "Harness | Tags"
  ansible.builtin.set_fact:
    hf_tags_list: >-
      {{
        (
          [ status_tag_value | default('') ]
          + (extra_tags | default([]))
          + [
              (hf_deployment_ref | lower) ~ ':stage:' ~ hf_stage_name_resolved,
              (hf_deployment_ref | lower) ~ ':machine:' ~ (hf_machine | lower),
              (hf_deployment_ref | lower) ~ ':env:' ~ hf_env
            ]
        )
        | map('string') | map('trim') | reject('equalto','') | list | unique
      }}
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
  when: hf_tmpdir is defined and hf_tmpdir.path is defined

- name: "Harness | Ler status.json"
  ansible.builtin.slurp:
    path: "{{ machine_status_file }}"
  register: hf_status_slurp
  when: hf_tmpdir is defined and hf_tmpdir.path is defined

- name: "Harness | Escrever STATUS temp"
  ansible.builtin.copy:
    dest: "{{ hf_tmpdir.path }}/{{ hf_status_name }}"
    mode: "0644"
    content: "{{ (hf_status_slurp.content | b64decode) if (hf_status_slurp is defined and hf_status_slurp.content is defined) else '{}' }}"
  when: hf_tmpdir is defined and hf_tmpdir.path is defined

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
    echo "RESPONSE=$(cat "$RESP" 2>/dev/null || true)"
    exit 0
  args:
    executable: /bin/bash
  register: hf_upload_log
  changed_when: false
  failed_when: false
  when: hf_tmpdir is defined and hf_tmpdir.path is defined

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
    echo "RESPONSE=$(cat "$RESP" 2>/dev/null || true)"
    exit 0
  args:
    executable: /bin/bash
  register: hf_upload_status
  changed_when: false
  failed_when: false
  when: hf_tmpdir is defined and hf_tmpdir.path is defined
