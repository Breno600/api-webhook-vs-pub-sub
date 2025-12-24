---
# ansible/harness_filestore_upload.yml
# Upload de LOG + STATUS para Harness File Store via /ng/api/file-store (multipart/form-data)
# Requer variáveis via ENV ou vars já resolvidas no pipeline.

- name: Harness | Resolver variáveis base via ENV (com fallback)
  set_fact:
    hf_endpoint: "{{ hf_endpoint | default(lookup('env','HARNESS_ENDPOINT') | default('')) }}"
    hf_account:  "{{ hf_account  | default(lookup('env','ACC')              | default(lookup('env','HARNESS_ACCOUNT') | default(''))) }}"
    hf_org:      "{{ hf_org      | default(lookup('env','ORG')              | default(lookup('env','HARNESS_ORG')     | default(''))) }}"
    hf_project:  "{{ hf_project  | default(lookup('env','PROJ')             | default(lookup('env','HARNESS_PROJECT') | default(''))) }}"
    hf_token:    "{{ hf_token    | default(lookup('env','TOKEN')            | default(lookup('env','HARNESS_TOKEN')   | default(lookup('env','HARNESS_API_KEY') | default('')))) }}"
    hf_parent:   "{{ hf_parent   | default(lookup('env','HARNESS_FILESTORE_PARENT') | default('Root')) }}"
  no_log: true

- name: Harness | Validar se pode fazer upload (não vaza token)
  set_fact:
    hf_can_upload: >-
      {{ (hf_endpoint | length > 0)
         and (hf_account  | length > 0)
         and (hf_org      | length > 0)
         and (hf_project  | length > 0)
         and (hf_token    | length > 0) }}

- name: Harness | DEBUG - variáveis principais
  debug:
    msg:
      - "hf_can_upload={{ hf_can_upload }}"
      - "hf_endpoint={{ hf_endpoint }}"
      - "hf_account={{ hf_account }}"
      - "hf_org={{ hf_org }}"
      - "hf_project={{ hf_project }}"
      - "hf_deploy_ref={{ hf_deploy_ref | default(deployment_ref_resolved | default('')) }}"
      - "hf_env={{ hf_env | default(filestore_env_resolved | default('')) }}"
      - "hf_stage={{ hf_stage | default('predeploy') }}"
      - "hf_machine={{ hf_machine | default(machine_name | default('')) }}"
      - "hf_parent={{ hf_parent }}"
      - "pat_len={{ (hf_token | length) }}"
      - "hf_base_dir={{ hf_base_dir | default(filestore_base_dir_resolved | default('')) }}"

- name: Harness | Parar aqui se não puder fazer upload
  when: not hf_can_upload
  debug:
    msg:
      - "Upload para Harness File Store ignorado porque faltam credenciais/variáveis."
      - "Garanta HARNESS_ENDPOINT/ACC/ORG/PROJ/TOKEN no ambiente."
  tags: [harness_filestore]
  # não falha o pipeline, só loga
  # (se você quiser falhar, troque debug por fail)

- name: Harness | Resolver paths (log/status)
  when: hf_can_upload
  set_fact:
    hf_local_log_file: "{{ hf_local_log_file | default(hf_pipeline_log_path | default('')) }}"
    hf_status_root:    "{{ hf_status_root    | default(status_dir_resolved | default('')) }}"
    hf_run_id:         "{{ hf_run_id         | default(run_id | default(ansible_date_time.epoch)) }}"
  tags: [harness_filestore]

- name: Harness | Descobrir status.json local (preferindo máquina)
  when: hf_can_upload
  vars:
    status_candidates:
      - "{{ hf_status_root }}/{{ hf_machine | default('') }}/status.json"
      - "{{ hf_status_root }}/status.json"
  stat:
    path: "{{ item }}"
  loop: "{{ status_candidates }}"
  register: hf_status_stats
  tags: [harness_filestore]

- name: Harness | Escolher status.json mais recente que exista
  when: hf_can_upload
  set_fact:
    hf_local_status_file: >-
      {{
        (hf_status_stats.results
         | selectattr('stat.exists','equalto',true)
         | map(attribute='stat.path')
         | list
         | first) | default('')
      }}
  tags: [harness_filestore]

- name: Harness | DEBUG - status escolhido
  when: hf_can_upload
  debug:
    msg:
      - "hf_local_log_file={{ hf_local_log_file }}"
      - "hf_local_status_file={{ hf_local_status_file }}"
      - "hf_run_id={{ hf_run_id }}"

- name: Harness | Validar arquivos locais
  when: hf_can_upload
  assert:
    that:
      - hf_local_log_file | length > 0
      - hf_local_status_file | length > 0
    fail_msg: "Não achei log/status locais para upload (hf_local_log_file/hf_local_status_file vazios)."
  tags: [harness_filestore]

- name: Harness | Preparar nomes e identifiers (sanitizados)
  when: hf_can_upload
  vars:
    deploy_ref: "{{ hf_deploy_ref | default(deployment_ref_resolved | default('')) }}"
    env_name:   "{{ hf_env | default(filestore_env_resolved | default('')) }}"
    stage:      "{{ hf_stage | default('predeploy') }}"
    machine:    "{{ hf_machine | default('machine') }}"
    # base legível + run_id pra evitar conflito em re-run
    raw_base:   "{{ env_name }}__{{ deploy_ref }}__{{ machine }}__{{ stage }}__{{ hf_run_id }}"
    # harness identifiers: só [a-zA-Z0-9_], e começa com letra (prefixo "f_")
    base_id:    "{{ ('f_' ~ raw_base) | lower | regex_replace('[^a-z0-9_]', '_') }}"
  set_fact:
    hf_log_name:    "{{ hf_log_name    | default(log_name | default(env_name ~ '__' ~ deploy_ref ~ '__' ~ machine ~ '-' ~ env_name ~ '-' ~ stage ~ '.log')) }}"
    hf_status_name: "{{ hf_status_name | default(status_name | default(env_name ~ '__' ~ deploy_ref ~ '__' ~ machine ~ '-' ~ env_name ~ '-' ~ stage ~ '.json')) }}"
    hf_log_identifier:    "{{ (base_id ~ '__log') }}"
    hf_status_identifier: "{{ (base_id ~ '__status') }}"
  tags: [harness_filestore]

- name: Harness | (opcional) Tags (string)
  when: hf_can_upload
  set_fact:
    hf_tags_str: "{{ hf_tags_str | default(hf_tags | default('')) }}"
  tags: [harness_filestore]

- name: Harness | Upload LOG (multipart) - com retry
  when: hf_can_upload
  shell: |
    set -euo pipefail
    RESP="$(mktemp)"
    OUT="$(mktemp)"

    curl -sS -o "$RESP" -w "HTTP_CODE=%{http_code}\n" \
      -X POST "{{ hf_endpoint }}/ng/api/file-store?accountIdentifier={{ hf_account }}&orgIdentifier={{ hf_org }}&projectIdentifier={{ hf_project }}" \
      -H "x-api-key: {{ hf_token }}" \
      -F "name={{ hf_log_name }}" \
      -F "type=FILE" \
      -F "identifier={{ hf_log_identifier }}" \
      -F "parentIdentifier={{ hf_parent }}" \
      {% if hf_tags_str|length > 0 %}-F "tags={{ hf_tags_str }}"{% endif %} \
      -F "file=@{{ hf_local_log_file }};type=text/plain" \
      > "$OUT"

    echo "RESPONSE_BODY_BEGIN"
    cat "$RESP"
    echo
    echo "RESPONSE_BODY_END"
    cat "$OUT"
  register: hf_upload_log
  retries: 5
  delay: 2
  until: hf_upload_log.rc == 0 and (hf_upload_log.stdout is search("HTTP_CODE=2"))
  changed_when: true
  tags: [harness_filestore]
  no_log: true  # evita vazar token em caso de debug do Ansible

- name: Harness | Mostrar resultado upload LOG (sem token)
  when: hf_can_upload
  debug:
    msg: "{{ hf_upload_log.stdout.splitlines()[-20:] | list }}"
  tags: [harness_filestore]

- name: Harness | Falhar se LOG não for 2xx (caso não tenha caído no until)
  when: hf_can_upload and (hf_upload_log.stdout is not search("HTTP_CODE=2"))
  fail:
    msg: "Upload LOG falhou. Saída:\n{{ hf_upload_log.stdout }}"

- name: Harness | Upload STATUS (multipart) - com retry
  when: hf_can_upload
  shell: |
    set -euo pipefail
    RESP="$(mktemp)"
    OUT="$(mktemp)"

    curl -sS -o "$RESP" -w "HTTP_CODE=%{http_code}\n" \
      -X POST "{{ hf_endpoint }}/ng/api/file-store?accountIdentifier={{ hf_account }}&orgIdentifier={{ hf_org }}&projectIdentifier={{ hf_project }}" \
      -H "x-api-key: {{ hf_token }}" \
      -F "name={{ hf_status_name }}" \
      -F "type=FILE" \
      -F "identifier={{ hf_status_identifier }}" \
      -F "parentIdentifier={{ hf_parent }}" \
      {% if hf_tags_str|length > 0 %}-F "tags={{ hf_tags_str }}"{% endif %} \
      -F "file=@{{ hf_local_status_file }};type=application/json" \
      > "$OUT"

    echo "RESPONSE_BODY_BEGIN"
    cat "$RESP"
    echo
    echo "RESPONSE_BODY_END"
    cat "$OUT"
  register: hf_upload_status
  retries: 5
  delay: 2
  until: hf_upload_status.rc == 0 and (hf_upload_status.stdout is search("HTTP_CODE=2"))
  changed_when: true
  tags: [harness_filestore]
  no_log: true

- name: Harness | Mostrar resultado upload STATUS (sem token)
  when: hf_can_upload
  debug:
    msg: "{{ hf_upload_status.stdout.splitlines()[-20:] | list }}"
  tags: [harness_filestore]

- name: Harness | Falhar se STATUS não for 2xx
  when: hf_can_upload and (hf_upload_status.stdout is not search("HTTP_CODE=2"))
  fail:
    msg: "Upload STATUS falhou. Saída:\n{{ hf_upload_status.stdout }}"
