# ansible/harness_filestore_upload.yml
---
# Upload para Harness File Store (sem loop de vars)
# Usa SOMENTE variáveis de ambiente HARNESS_*.

- name: "Harness | Resolver entradas via ENV (sem recursão)"
  ansible.builtin.set_fact:
    hf_stage: "{{ (hf_stage_name | default(stage_name | default('unknown')) | string | trim) }}"
    hf_machine: "{{ (current_machine | default(machine_name | default('')) | string | trim) }}"
    hf_deploy_ref: "{{ (deployment_ref | default(lookup('env','GIT_TAG') | default('')) | string | trim) }}"
    hf_env: "{{ (filestore_env | default(lookup('env','FILESTORE_ENV') | default('dev')) | string | trim | lower) }}"
    hf_base_dir: "{{ (filestore_base_dir | default(hf_env ~ '/' ~ hf_deploy_ref) | string | trim) }}"

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

- name: "Harness | Validar se pode fazer upload"
  ansible.builtin.set_fact:
    hf_can_upload: >-
      {{
        (hf_pat | length) > 0 and
        (hf_endpoint | length) > 0 and
        (hf_account | length) > 0 and
        (hf_org | length) > 0 and
        (hf_project | length) > 0 and
        (hf_deploy_ref | length) > 0 and
        (hf_machine | length) > 0
      }}

- name: "Harness | Pular upload se faltar credenciais (não falha pipeline)"
  ansible.builtin.debug:
    msg: "Credenciais HARNESS_* ausentes/incompletas. Pulando upload."
  when: not hf_can_upload
  changed_when: false

- name: "Harness | Resolver nomes/paths"
  ansible.builtin.set_fact:
    hf_log_path: "{{ filestore_log_path | default(hf_base_dir ~ '/' ~ (hf_deploy_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '.log') }}"
    hf_status_path: "{{ filestore_status_path | default(hf_base_dir ~ '/' ~ (hf_deploy_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '.json') }}"
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
              (hf_deploy_ref | lower) ~ ':stage:' ~ hf_stage,
              (hf_deploy_ref | lower) ~ ':machine:' ~ (hf_machine | lower),
              (hf_deploy_ref | lower) ~ ':env:' ~ hf_env
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
  when: hf_can_upload

- name: "Harness | Ler status.json"
  ansible.builtin.slurp:
    path: "{{ machine_status_file }}"
  register: hf_status_slurp
  when: hf_can_upload

- name: "Harness | Escrever STATUS temp"
  ansible.builtin.copy:
    dest: "{{ hf_tmpdir.path }}/{{ hf_status_name }}"
    mode: "0644"
    content: "{{ (hf_status_slurp.content | b64decode) if (hf_status_slurp.content is defined) else '{}' }}"
  when: hf_can_upload

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
