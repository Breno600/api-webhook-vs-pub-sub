---
# ==========================================================
# Harness File Store Upload (LOG + STATUS) - ROBUSTO
# - NÃO usa find (evita "não achei arquivos")
# - Usa machine_status_file e pipeline_log_file (ou deriva)
# - Se log não existir, cria tmp com log_content
# - NÃO quebra a pipeline: nunca dá fail
#
# Requer ENV:
#   HARNESS_ENDPOINT, HARNESS_ACCOUNT_ID, HARNESS_ORG_ID, HARNESS_PROJECT_ID
#   HARNESS_API_KEY (ou HARNESS_X_API_KEY)
#
# Opcional:
#   PARENT_IDENTIFIER (default Root)
# ==========================================================

- name: Harness | Resolver variáveis base via ENV (com fallback)
  ansible.builtin.set_fact:
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
  ansible.builtin.set_fact:
    hf_deploy_ref: "{{ deployment_ref | default(lookup('env','GIT_TAG') | default('UNKNOWN', true), true) }}"
    hf_env: "{{ filestore_env | default(lookup('env','FILESTORE_ENV') | default('dev', true), true) }}"
    hf_stage: "{{ hf_stage_name | default(stage_name | default('predeploy', true), true) }}"
    hf_machine: "{{ machine_name | default(current_machine | default(inventory_hostname, true), true) }}"

- name: Harness | Validar se pode tentar upload
  ansible.builtin.set_fact:
    hf_can_upload: >-
      {{ (hf_endpoint | length > 0)
         and (hf_account | length > 0)
         and (hf_org | length > 0)
         and (hf_project | length > 0)
         and (hf_pat | length > 0) }}

- name: Harness | DEBUG - variáveis principais
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
      - "hf_parent={{ hf_parent }}"
      - "pat_len={{ hf_pat | length }}"

- name: Harness | Se não puder upload, só avisar e sair
  when: not hf_can_upload
  ansible.builtin.debug:
    msg:
      - "Upload Harness ignorado: faltam variáveis/credenciais."
      - "Garanta HARNESS_ENDPOINT, HARNESS_ACCOUNT_ID, HARNESS_ORG_ID, HARNESS_PROJECT_ID, HARNESS_API_KEY."

# ----------------------------------------------------------
# Determinar arquivos locais (SEM find)
# ----------------------------------------------------------
- name: Harness | Derivar caminhos (status/log) a partir do machine_status_file
  when: hf_can_upload
  ansible.builtin.set_fact:
    hf_status_file_candidate: >-
      {{ machine_status_file | default('') }}
    hf_log_file_candidate: >-
      {{
        pipeline_log_file
          | default(
              ( (machine_status_file | dirname) ~ '/pipeline.log' )
              if (machine_status_file is defined and (machine_status_file | length) > 0)
              else '',
            true)
      }}

- name: Harness | Stat status.json / pipeline.log
  when: hf_can_upload
  ansible.builtin.stat:
    path: "{{ item }}"
  loop:
    - "{{ hf_status_file_candidate }}"
    - "{{ hf_log_file_candidate }}"
  register: hf_stat_candidates
  failed_when: false

- name: Harness | Resolver existências
  when: hf_can_upload
  ansible.builtin.set_fact:
    hf_status_exists: "{{ (hf_stat_candidates.results[0].stat.exists | default(false)) if (hf_stat_candidates.results|length>0) else false }}"
    hf_log_exists: "{{ (hf_stat_candidates.results[1].stat.exists | default(false)) if (hf_stat_candidates.results|length>1) else false }}"

- name: Harness | Criar tmp dir (controller)
  when: hf_can_upload
  ansible.builtin.tempfile:
    state: directory
    suffix: hf_upload
  register: hf_tmpdir

- name: Harness | Preparar arquivo STATUS para upload (se existir)
  when: hf_can_upload and hf_status_exists
  ansible.builtin.copy:
    src: "{{ hf_status_file_candidate }}"
    dest: "{{ hf_tmpdir.path }}/upload.status.json"
    remote_src: true

- name: Harness | Preparar arquivo LOG para upload
  when: hf_can_upload
  block:
    - name: Harness | Se pipeline.log existir, copiar
      when: hf_log_exists
      ansible.builtin.copy:
        src: "{{ hf_log_file_candidate }}"
        dest: "{{ hf_tmpdir.path }}/upload.pipeline.log"
        remote_src: true

    - name: Harness | Se pipeline.log NÃO existir, criar via log_content
      when: not hf_log_exists
      ansible.builtin.copy:
        dest: "{{ hf_tmpdir.path }}/upload.pipeline.log"
        mode: "0644"
        content: "{{ log_content | default('') }}"
  rescue:
    - ansible.builtin.debug:
        msg:
          - "Falha ao preparar log local para upload (mas não vou quebrar a pipeline)."

- name: Harness | Calcular nomes/identifiers
  when: hf_can_upload
  ansible.builtin.set_fact:
    hf_run_id: "{{ (hf_deploy_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ (hf_env | lower) ~ '-' ~ hf_stage ~ '-' ~ (ansible_date_time.epoch | string) }}"
    hf_log_name: "{{ hf_env }}__{{ hf_deploy_ref }}__{{ hf_run_id }}.log"
    hf_status_name: "{{ hf_env }}__{{ hf_deploy_ref }}__{{ hf_run_id }}.json"
    hf_log_identifier: "{{ (hf_run_id ~ '-log') | regex_replace('[^a-zA-Z0-9_\\-]', '_') }}"
    hf_status_identifier: "{{ (hf_run_id ~ '-status') | regex_replace('[^a-zA-Z0-9_\\-]', '_') }}"
    hf_url: "{{ hf_endpoint }}/ng/api/file-store?accountIdentifier={{ hf_account }}&orgIdentifier={{ hf_org }}&projectIdentifier={{ hf_project }}"

- name: Harness | DEBUG - arquivos finais
  when: hf_can_upload
  ansible.builtin.debug:
    msg:
      - "status_file_candidate={{ hf_status_file_candidate }} exists={{ hf_status_exists }}"
      - "log_file_candidate={{ hf_log_file_candidate }} exists={{ hf_log_exists }}"
      - "tmpdir={{ hf_tmpdir.path }}"
      - "hf_url={{ hf_url }}"
      - "LOG_NAME={{ hf_log_name }} IDENT={{ hf_log_identifier }}"
      - "STATUS_NAME={{ hf_status_name }} IDENT={{ hf_status_identifier }}"

# ----------------------------------------------------------
# Upload LOG (não falha pipeline)
# ----------------------------------------------------------
- name: Harness | Upload LOG (multipart) - não quebra pipeline
  when: hf_can_upload
  block:
    - name: Harness | Enviar LOG
      ansible.builtin.shell: |
        set -e
        resp="{{ hf_tmpdir.path }}/resp_log.json"
        curl -sS -o "$resp" -w "\nHTTP_CODE=%{http_code}\n" \
          -X POST "{{ hf_url }}" \
          -H "x-api-key: {{ hf_pat }}" \
          -F "name={{ hf_log_name }}" \
          -F "type=FILE" \
          -F "identifier={{ hf_log_identifier }}" \
          -F "parentIdentifier={{ hf_parent }}" \
          -F "file=@{{ hf_tmpdir.path }}/upload.pipeline.log;type=text/plain"
        echo "RESP_FILE=$resp"
      register: hf_upload_log_raw
      no_log: true

    - name: Harness | Extrair HTTP_CODE LOG
      ansible.builtin.set_fact:
        hf_upload_log_http: "{{ (hf_upload_log_raw.stdout | regex_search('HTTP_CODE=([0-9]+)', '\\1')) | default(['0'], true) | first }}"
        hf_upload_log_resp_file: "{{ (hf_upload_log_raw.stdout | regex_search('RESP_FILE=(.*)', '\\1')) | default([''], true) | first }}"

    - name: Harness | Mostrar resultado upload LOG
      ansible.builtin.shell: |
        echo "HTTP_CODE={{ hf_upload_log_http }}"
        if [ -n "{{ hf_upload_log_resp_file }}" ] && [ -f "{{ hf_upload_log_resp_file }}" ]; then
          echo "BODY:"
          cat "{{ hf_upload_log_resp_file }}"
        fi
      register: hf_upload_log_view
      changed_when: false
      failed_when: false

    - name: Harness | Avisar se LOG não for 2xx
      when: hf_upload_log_http | int < 200 or hf_upload_log_http | int >= 300
      ansible.builtin.debug:
        msg:
          - "⚠️ Upload LOG falhou (HTTP={{ hf_upload_log_http }}) mas pipeline continua."
          - "{{ hf_upload_log_view.stdout }}"
  rescue:
    - ansible.builtin.debug:
        msg:
          - "⚠️ Erro inesperado no upload do LOG, mas pipeline continua."

# ----------------------------------------------------------
# Upload STATUS (não falha pipeline)
# ----------------------------------------------------------
- name: Harness | Upload STATUS (multipart) - não quebra pipeline
  when: hf_can_upload and hf_status_exists
  block:
    - name: Harness | Enviar STATUS
      ansible.builtin.shell: |
        set -e
        resp="{{ hf_tmpdir.path }}/resp_status.json"
        curl -sS -o "$resp" -w "\nHTTP_CODE=%{http_code}\n" \
          -X POST "{{ hf_url }}" \
          -H "x-api-key: {{ hf_pat }}" \
          -F "name={{ hf_status_name }}" \
          -F "type=FILE" \
          -F "identifier={{ hf_status_identifier }}" \
          -F "parentIdentifier={{ hf_parent }}" \
          -F "file=@{{ hf_tmpdir.path }}/upload.status.json;type=application/json"
        echo "RESP_FILE=$resp"
      register: hf_upload_status_raw
      no_log: true

    - name: Harness | Extrair HTTP_CODE STATUS
      ansible.builtin.set_fact:
        hf_upload_status_http: "{{ (hf_upload_status_raw.stdout | regex_search('HTTP_CODE=([0-9]+)', '\\1')) | default(['0'], true) | first }}"
        hf_upload_status_resp_file: "{{ (hf_upload_status_raw.stdout | regex_search('RESP_FILE=(.*)', '\\1')) | default([''], true) | first }}"

    - name: Harness | Mostrar resultado upload STATUS
      ansible.builtin.shell: |
        echo "HTTP_CODE={{ hf_upload_status_http }}"
        if [ -n "{{ hf_upload_status_resp_file }}" ] && [ -f "{{ hf_upload_status_resp_file }}" ]; then
          echo "BODY:"
          cat "{{ hf_upload_status_resp_file }}"
        fi
      register: hf_upload_status_view
      changed_when: false
      failed_when: false

    - name: Harness | Avisar se STATUS não for 2xx
      when: hf_upload_status_http | int < 200 or hf_upload_status_http | int >= 300
      ansible.builtin.debug:
        msg:
          - "⚠️ Upload STATUS falhou (HTTP={{ hf_upload_status_http }}) mas pipeline continua."
          - "{{ hf_upload_status_view.stdout }}"
  rescue:
    - ansible.builtin.debug:
        msg:
          - "⚠️ Erro inesperado no upload do STATUS, mas pipeline continua."

- name: Harness | Se status não existir, avisar e seguir
  when: hf_can_upload and not hf_status_exists
  ansible.builtin.debug:
    msg:
      - "⚠️ status.json não existe em {{ hf_status_file_candidate }} — vou subir apenas o LOG."

- name: Harness | Fim (não falha)
  when: hf_can_upload
  ansible.builtin.debug:
    msg:
      - "Upload step finalizado (pipeline não será interrompida)."
