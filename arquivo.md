---
# =====================================================================================
# HARNESS FILESTORE UPLOAD (SEM RECURSÃO)
#
# Espera receber (via include_tasks vars):
# - log_content (string)
# - machine_status_file (path local controller)
# - stage_name (opcional, default: predeploy)
# - current_machine (string)  <-- NÃO será redefinido aqui
# - deployment_ref
# - filestore_env
# - filestore_base_dir
# - filestore_log_path (opcional)
# - filestore_status_path (opcional)
# - status_tag_value (string)
# - extra_tags (list)
#
# Credenciais (preferência vars Ansible; fallback env):
# - harness_endpoint / HARNESS_ENDPOINT (ex: https://harness.onefiserv.net)
# - harness_account_id / HARNESS_ACCOUNT_ID
# - harness_org_id / HARNESS_ORG_ID
# - harness_project_id / HARNESS_PROJECT_ID
# - harness_api_key / HARNESS_API_KEY   (PAT)
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Resolver entradas sem tocar no current_machine (evita recursão)
# -----------------------------------------------------------------------------
- name: "Harness | Resolver entradas (safe locals)"
  ansible.builtin.set_fact:
    hf_stage: "{{ stage_name | default('predeploy') }}"
    hf_machine: "{{ (current_machine | default(machine_name | default(''))) | string | trim }}"
    hf_deployment_ref: "{{ (deployment_ref | default(lookup('env','GIT_TAG') | default(''))) | string | trim }}"
    hf_env: "{{ (filestore_env | default('dev')) | string | trim | lower }}"
    hf_base_dir: "{{ (filestore_base_dir | default((filestore_env | default('dev')) ~ '/' ~ (deployment_ref | default('')))) | string | trim }}"
    hf_endpoint: "{{ (harness_endpoint | default(lookup('env','HARNESS_ENDPOINT') | default('https://harness.onefiserv.net'))) | string | trim }}"
    hf_account: "{{ (harness_account_id | default(lookup('env','HARNESS_ACCOUNT_ID') | default(''))) | string | trim }}"
    hf_org: "{{ (harness_org_id | default(lookup('env','HARNESS_ORG_ID') | default(''))) | string | trim }}"
    hf_project: "{{ (harness_project_id | default(lookup('env','HARNESS_PROJECT_ID') | default(''))) | string | trim }}"
    hf_pat: "{{ (harness_api_key | default(lookup('env','HARNESS_API_KEY') | default(''))) | string | trim }}"

- name: "Harness | Validar mínimos (sem falhar a pipeline)"
  ansible.builtin.debug:
    msg:
      - "hf_endpoint={{ hf_endpoint }}"
      - "hf_account={{ hf_account }}"
      - "hf_org={{ hf_org }}"
      - "hf_project={{ hf_project }}"
      - "hf_deployment_ref={{ hf_deployment_ref }}"
      - "hf_machine={{ hf_machine }}"
      - "hf_stage={{ hf_stage }}"
      - "hf_env={{ hf_env }}"
  changed_when: false

- name: "Harness | Pular upload se faltar credenciais do Harness"
  ansible.builtin.debug:
    msg: "Harness creds ausentes (HARNESS_API_KEY/HARNESS_ACCOUNT_ID/HARNESS_ORG_ID/HARNESS_PROJECT_ID). Pulando upload."
  when:
    - hf_pat | length == 0 or hf_account | length == 0 or hf_org | length == 0 or hf_project | length == 0
  changed_when: false

# -----------------------------------------------------------------------------
# 2) Construir nomes seguros para os arquivos
# -----------------------------------------------------------------------------
- name: "Harness | Resolver paths lógicos (fallback) + nomes safe"
  ansible.builtin.set_fact:
    hf_log_path: >-
      {{
        filestore_log_path
          | default(hf_base_dir ~ '/' ~ (hf_deployment_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '.log')
      }}
    hf_status_path: >-
      {{
        filestore_status_path
          | default(hf_base_dir ~ '/' ~ (hf_deployment_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '.json')
      }}
    hf_log_name: "{{ (hf_log_path | regex_replace('^/+','')) | replace('/','__') }}"
    hf_status_name: "{{ (hf_status_path | regex_replace('^/+','')) | replace('/','__') }}"
  when:
    - hf_pat | length > 0
    - hf_account | length > 0
    - hf_org | length > 0
    - hf_project | length > 0

# -----------------------------------------------------------------------------
# 3) Tags (string única)
# -----------------------------------------------------------------------------
- name: "Harness | Montar tags"
  ansible.builtin.set_fact:
    hf_tags_list: >-
      {{
        (
          [ status_tag_value | default('') ]
          + (extra_tags | default([]))
          + [
              (hf_deployment_ref | lower) ~ ':stage:' ~ hf_stage,
              (hf_deployment_ref | lower) ~ ':machine:' ~ (hf_machine | lower),
              (hf_deployment_ref | lower) ~ ':env:' ~ hf_env
            ]
        )
        | map('string')
        | map('trim')
        | reject('equalto','')
        | list
        | unique
      }}
    hf_tags: "{{ hf_tags_list | join(',') }}"
  when:
    - hf_pat | length > 0
    - hf_account | length > 0
    - hf_org | length > 0
    - hf_project | length > 0

# -----------------------------------------------------------------------------
# 4) Criar arquivos temporários (controller)
# -----------------------------------------------------------------------------
- name: "Harness | Criar temp dir (controller)"
  ansible.builtin.tempfile:
    state: directory
    suffix: hf_upload
  register: hf_tmpdir
  when:
    - hf_pat | length > 0
    - hf_account | length > 0
    - hf_org | length > 0
    - hf_project | length > 0

- name: "Harness | Escrever LOG temp (controller)"
  ansible.builtin.copy:
    dest: "{{ hf_tmpdir.path }}/{{ hf_log_name }}"
    mode: "0644"
    content: "{{ log_content | default('') }}"
  when: hf_tmpdir is defined

- name: "Harness | Ler status.json (controller)"
  ansible.builtin.slurp:
    path: "{{ machine_status_file }}"
  register: hf_status_slurp
  when:
    - hf_tmpdir is defined
    - machine_status_file is defined

- name: "Harness | Escrever STATUS temp (controller)"
  ansible.builtin.copy:
    dest: "{{ hf_tmpdir.path }}/{{ hf_status_name }}"
    mode: "0644"
    content: "{{ (hf_status_slurp.content | b64decode) if (hf_status_slurp is defined and hf_status_slurp.content is defined) else '{}' }}"
  when: hf_tmpdir is defined

# -----------------------------------------------------------------------------
# 5) Upload LOG e STATUS (NÃO falha pipeline se Harness der erro)
# -----------------------------------------------------------------------------
- name: "Harness | Upload LOG"
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
    # nunca falha por upload
    exit 0
  args:
    executable: /bin/bash
  register: hf_upload_log
  changed_when: false
  failed_when: false
  when: hf_tmpdir is defined

- name: "Harness | Upload STATUS"
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
    # nunca falha por upload
    exit 0
  args:
    executable: /bin/bash
  register: hf_upload_status
  changed_when: false
  failed_when: false
  when: hf_tmpdir is defined

- name: "Harness | Resultado upload (debug)"
  ansible.builtin.debug:
    msg:
      - "upload_log: {{ (hf_upload_log.stdout_lines | default([])) }}"
      - "upload_status: {{ (hf_upload_status.stdout_lines | default([])) }}"
  changed_when: false
  when: hf_tmpdir is defined
