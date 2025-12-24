---
# ==========================================================
# Harness File Store Upload (LOG + STATUS)
# - Descobre pipeline.log e status.json automaticamente
# - Faz upload via /ng/api/file-store
# - Usa ENV: HARNESS_ENDPOINT, HARNESS_ACCOUNT_ID, HARNESS_ORG_ID, HARNESS_PROJECT_ID, HARNESS_API_KEY
#   (também aceita ACC/ORG/PROJ/TOKEN como fallback)
# - Default parentIdentifier = Root
# ==========================================================

- name: Harness | Resolver variáveis base via ENV (com fallback)
  set_fact:
    hf_endpoint: >-
      {{ lookup('env','HARNESS_ENDPOINT')
         | default(lookup('env','HARNESS_URL'), true)
         | default('https://harness.onefiserv.net', true) }}
    hf_account: >-
      {{ lookup('env','HARNESS_ACCOUNT_ID')
         | default(lookup('env','ACC'), true)
         | default('', true) }}
    hf_org: >-
      {{ lookup('env','HARNESS_ORG_ID')
         | default(lookup('env','ORG'), true)
         | default('', true) }}
    hf_project: >-
      {{ lookup('env','HARNESS_PROJECT_ID')
         | default(lookup('env','PROJ'), true)
         | default('', true) }}
    hf_pat: >-
      {{ lookup('env','HARNESS_API_KEY')
         | default(lookup('env','HARNESS_X_API_KEY'), true)
         | default(lookup('env','TOKEN'), true)
         | default('', true) }}
    hf_parent: >-
      {{ lookup('env','PARENT_IDENTIFIER')
         | default('Root', true) }}

- name: Harness | Definir contexto (deploy/env/stage/machine)
  set_fact:
    hf_deploy_ref: "{{ deployment_ref | default(lookup('env','GIT_TAG') | default('UNKNOWN', true), true) }}"
    hf_env: "{{ filestore_env | default(lookup('env','FILESTORE_ENV') | default('dev', true), true) }}"
    hf_stage: "{{ stage_name | default('predeploy', true) }}"
    hf_machine: "{{ machine_name | default(inventory_hostname, true) }}"

- name: Harness | Validar se pode fazer upload (não vaza token)
  set_fact:
    hf_can_upload: >-
      {{ (hf_endpoint | length > 0)
         and (hf_account | length > 0)
         and (hf_org | length > 0)
         and (hf_project | length > 0)
         and (hf_pat | length > 0) }}

- name: Harness | DEBUG - variáveis principais
  debug:
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
      - "hf_parent={{ hf_parent }}"
      - "pat_len={{ hf_pat | length }}"

- name: Harness | Parar aqui se não puder fazer upload
  when: not hf_can_upload
  debug:
    msg:
      - "Upload para Harness File Store ignorado porque faltam credenciais/variáveis."
      - "Garanta HARNESS_ENDPOINT + (HARNESS_ACCOUNT_ID|ACC) + (HARNESS_ORG_ID|ORG) + (HARNESS_PROJECT_ID|PROJ) + (HARNESS_API_KEY|TOKEN)."

# ----------------------------------------------------------
# Resolver status root (onde o playbook salva arquivos)
# ----------------------------------------------------------
- name: Harness | Definir diretório base de status (safe)
  when: hf_can_upload
  set_fact:
    hf_status_root: >-
      {{ status_dir_resolved
         | default((repo_root_resolved ~ '/status/' ~ hf_deploy_ref), true) }}

- name: Harness | DEBUG - status root
  when: hf_can_upload
  debug:
    msg:
      - "hf_status_root={{ hf_status_root }}"

# ----------------------------------------------------------
# Descoberta robusta de pipeline.log e status.json
# ----------------------------------------------------------
- name: Harness | Find pipeline.log e status.json (recurse)
  when: hf_can_upload
  find:
    paths: "{{ hf_status_root }}"
    patterns:
      - "pipeline.log"
      - "status.json"
    recurse: true
    file_type: file
  register: hf_found_files

- name: Harness | Separar logs/status encontrados
  when: hf_can_upload
  set_fact:
    hf_found_logs: >-
      {{ hf_found_files.files
         | selectattr('path','search','pipeline\\.log$')
         | list }}
    hf_found_status: >-
      {{ hf_found_files.files
         | selectattr('path','search','status\\.json$')
         | list }}

- name: Harness | Escolher log/status mais recentes (por mtime)
  when: hf_can_upload
  set_fact:
    hf_local_log_file: >-
      {{ (hf_found_logs | sort(attribute='mtime', reverse=true) | first).path
         if (hf_found_logs | length) > 0 else '' }}
    hf_local_status_file: >-
      {{ (hf_found_status | sort(attribute='mtime', reverse=true) | first).path
         if (hf_found_status | length) > 0 else '' }}

- name: Harness | DEBUG - arquivos escolhidos
  when: hf_can_upload
  debug:
    msg:
      - "hf_local_log_file={{ hf_local_log_file }}"
      - "hf_local_status_file={{ hf_local_status_file }}"
      - "logs_encontrados={{ hf_found_logs | map(attribute='path') | list }}"
      - "status_encontrados={{ hf_found_status | map(attribute='path') | list }}"

- name: Harness | Validar arquivos locais
  when: hf_can_upload
  assert:
    that:
      - hf_local_log_file | length > 0
      - hf_local_status_file | length > 0
    fail_msg: "Não achei pipeline.log/status.json dentro de {{ hf_status_root }} (find não retornou nada)."

# ----------------------------------------------------------
# Preparar nomes / identifiers (sanitizados)
# ----------------------------------------------------------
- name: Harness | Normalizar deploy_ref e montar run_id
  when: hf_can_upload
  set_fact:
    hf_deploy_ref_lc: "{{ hf_deploy_ref | lower }}"
    hf_run_id: "{{ (hf_deploy_ref | lower) ~ '-' ~ hf_machine ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '-' ~ (ansible_date_time.epoch | string) }}"

- name: Harness | Definir nomes finais (File Store)
  when: hf_can_upload
  set_fact:
    hf_log_name: "{{ hf_env }}__{{ hf_deploy_ref }}__{{ hf_run_id }}.log"
    hf_status_name: "{{ hf_env }}__{{ hf_deploy_ref }}__{{ hf_run_id }}.json"
    hf_log_identifier: "{{ (hf_run_id ~ '-log') | regex_replace('[^a-zA-Z0-9_\\-]', '_') }}"
    hf_status_identifier: "{{ (hf_run_id ~ '-status') | regex_replace('[^a-zA-Z0-9_\\-]', '_') }}"

- name: Harness | DEBUG - nomes finais
  when: hf_can_upload
  debug:
    msg:
      - "LOG_NAME={{ hf_log_name }}"
      - "STATUS_NAME={{ hf_status_name }}"
      - "LOG_IDENTIFIER={{ hf_log_identifier }}"
      - "STATUS_IDENTIFIER={{ hf_status_identifier }}"

# ----------------------------------------------------------
# Upload LOG / STATUS (multipart)
# ----------------------------------------------------------
- name: Harness | Criar tmp dir (controller)
  when: hf_can_upload
  tempfile:
    state: directory
    suffix: hf_upload
  register: hf_tmpdir

- name: Harness | Preparar arquivos temporários (para garantir extensão/nome)
  when: hf_can_upload
  copy:
    src: "{{ item.src }}"
    dest: "{{ hf_tmpdir.path }}/{{ item.dest }}"
    remote_src: true
  loop:
    - { src: "{{ hf_local_log_file }}",    dest: "upload.log" }
    - { src: "{{ hf_local_status_file }}", dest: "upload.json" }

- name: Harness | Upload LOG (multipart) - com retry
  when: hf_can_upload
  vars:
    hf_url: "{{ hf_endpoint }}/ng/api/file-store?accountIdentifier={{ hf_account }}&orgIdentifier={{ hf_org }}&projectIdentifier={{ hf_project }}"
    hf_file: "{{ hf_tmpdir.path }}/upload.log"
  shell: |
    set -e
    resp="{{ hf_tmpdir.path }}/resp_log.json"
    curl -sS -o "$resp" -w "\nHTTP_CODE=%{http_code}\n" \
      -X POST "{{ hf_url }}" \
      -H "x-api-key: {{ hf_pat }}" \
      -F "name={{ hf_log_name }}" \
      -F "type=FILE" \
      -F "identifier={{ hf_log_identifier }}" \
      -F "parentIdentifier={{ hf_parent }}" \
      -F "file=@{{ hf_file }};type=text/plain"
    echo "RESP_FILE=$resp"
  register: hf_upload_log_raw
  no_log: true
  retries: 3
  delay: 3
  until: hf_upload_log_raw.rc == 0

- name: Harness | Extrair HTTP_CODE LOG
  when: hf_can_upload
  set_fact:
    hf_upload_log_http: "{{ (hf_upload_log_raw.stdout | regex_search('HTTP_CODE=([0-9]+)', '\\1')) | default(['0'], true) | first }}"
    hf_upload_log_resp_file: "{{ (hf_upload_log_raw.stdout | regex_search('RESP_FILE=(.*)', '\\1')) | default([''], true) | first }}"

- name: Harness | Mostrar resultado upload LOG (sem token)
  when: hf_can_upload
  shell: |
    set -e
    echo "HTTP_CODE={{ hf_upload_log_http }}"
    if [ -n "{{ hf_upload_log_resp_file }}" ] && [ -f "{{ hf_upload_log_resp_file }}" ]; then
      echo "BODY:"
      cat "{{ hf_upload_log_resp_file }}"
    else
      echo "BODY: (sem arquivo de resposta)"
    fi
  register: hf_upload_log_view
  changed_when: false

- name: Harness | Falhar se LOG não for 2xx
  when: hf_can_upload and (hf_upload_log_http | int < 200 or hf_upload_log_http | int >= 300)
  fail:
    msg: |
      Upload LOG falhou (HTTP={{ hf_upload_log_http }}).
      Saída:
      {{ hf_upload_log_view.stdout }}

- name: Harness | Upload STATUS (multipart) - com retry
  when: hf_can_upload
  vars:
    hf_url: "{{ hf_endpoint }}/ng/api/file-store?accountIdentifier={{ hf_account }}&orgIdentifier={{ hf_org }}&projectIdentifier={{ hf_project }}"
    hf_file: "{{ hf_tmpdir.path }}/upload.json"
  shell: |
    set -e
    resp="{{ hf_tmpdir.path }}/resp_status.json"
    curl -sS -o "$resp" -w "\nHTTP_CODE=%{http_code}\n" \
      -X POST "{{ hf_url }}" \
      -H "x-api-key: {{ hf_pat }}" \
      -F "name={{ hf_status_name }}" \
      -F "type=FILE" \
      -F "identifier={{ hf_status_identifier }}" \
      -F "parentIdentifier={{ hf_parent }}" \
      -F "file=@{{ hf_file }};type=application/json"
    echo "RESP_FILE=$resp"
  register: hf_upload_status_raw
  no_log: true
  retries: 3
  delay: 3
  until: hf_upload_status_raw.rc == 0

- name: Harness | Extrair HTTP_CODE STATUS
  when: hf_can_upload
  set_fact:
    hf_upload_status_http: "{{ (hf_upload_status_raw.stdout | regex_search('HTTP_CODE=([0-9]+)', '\\1')) | default(['0'], true) | first }}"
    hf_upload_status_resp_file: "{{ (hf_upload_status_raw.stdout | regex_search('RESP_FILE=(.*)', '\\1')) | default([''], true) | first }}"

- name: Harness | Mostrar resultado upload STATUS (sem token)
  when: hf_can_upload
  shell: |
    set -e
    echo "HTTP_CODE={{ hf_upload_status_http }}"
    if [ -n "{{ hf_upload_status_resp_file }}" ] && [ -f "{{ hf_upload_status_resp_file }}" ]; then
      echo "BODY:"
      cat "{{ hf_upload_status_resp_file }}"
    else
      echo "BODY: (sem arquivo de resposta)"
    fi
  register: hf_upload_status_view
  changed_when: false

- name: Harness | Falhar se STATUS não for 2xx
  when: hf_can_upload and (hf_upload_status_http | int < 200 or hf_upload_status_http | int >= 300)
  fail:
    msg: |
      Upload STATUS falhou (HTTP={{ hf_upload_status_http }}).
      Saída:
      {{ hf_upload_status_view.stdout }}

- name: Harness | OK - uploads concluídos
  when: hf_can_upload
  debug:
    msg:
      - "Uploads concluídos no Harness File Store ✅"
      - "LOG={{ hf_log_name }}"
      - "STATUS={{ hf_status_name }}"
      - "parentIdentifier={{ hf_parent }}"
