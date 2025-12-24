- name: Harness | DEBUG - validar conteúdo antes do upload (tamanho + head)
  when: hf_can_upload
  ansible.builtin.shell: |
    set -e
    echo "== LOCAL TMP FILES =="
    ls -lah "{{ hf_tmpdir.path }}" || true
    echo "SIZE_LOG_BYTES=$(wc -c < "{{ hf_tmpdir.path }}/upload.pipeline.log" 2>/dev/null || echo 0)"
    echo "SIZE_STATUS_BYTES=$(wc -c < "{{ hf_tmpdir.path }}/upload.status.json" 2>/dev/null || echo 0)"
    echo "---- HEAD LOG ----"
    head -n 30 "{{ hf_tmpdir.path }}/upload.pipeline.log" || true
    echo "---- HEAD STATUS ----"
    head -n 60 "{{ hf_tmpdir.path }}/upload.status.json" || true
  register: hf_debug_payload
  changed_when: false
  failed_when: false

- debug:
    var: hf_debug_payload.stdout_lines
  when: hf_can_upload


- name: Harness | Enviar LOG (multipart) - modo compatível
  when: hf_can_upload
  ansible.builtin.shell: |
    set -euo pipefail
    resp="{{ hf_tmpdir.path }}/resp_log.json"

    curl --http1.1 -sS -D "{{ hf_tmpdir.path }}/hdr_log.txt" \
      -o "$resp" -w "\nHTTP_CODE=%{http_code}\n" \
      "{{ hf_url }}" \
      -H "x-api-key: {{ hf_pat }}" \
      --form-string "name={{ hf_log_name }}" \
      --form-string "type=FILE" \
      --form-string "identifier={{ hf_log_identifier }}" \
      --form-string "parentIdentifier={{ hf_parent }}" \
      -F "file=@{{ hf_tmpdir.path }}/upload.pipeline.log;filename={{ hf_log_name }};type=text/plain"

    echo "RESP_FILE=$resp"
  register: hf_upload_log_raw
  no_log: true


- name: Harness | Enviar STATUS (multipart) - modo compatível
  when: hf_can_upload and hf_status_exists
  ansible.builtin.shell: |
    set -euo pipefail
    resp="{{ hf_tmpdir.path }}/resp_status.json"

    curl --http1.1 -sS -D "{{ hf_tmpdir.path }}/hdr_status.txt" \
      -o "$resp" -w "\nHTTP_CODE=%{http_code}\n" \
      "{{ hf_url }}" \
      -H "x-api-key: {{ hf_pat }}" \
      --form-string "name={{ hf_status_name }}" \
      --form-string "type=FILE" \
      --form-string "identifier={{ hf_status_identifier }}" \
      --form-string "parentIdentifier={{ hf_parent }}" \
      -F "file=@{{ hf_tmpdir.path }}/upload.status.json;filename={{ hf_status_name }};type=application/json"

    echo "RESP_FILE=$resp"
  register: hf_upload_status_raw
  no_log: true


- name: Harness | DEBUG - headers + body LOG (sempre)
  when: hf_can_upload
  ansible.builtin.shell: |
    echo "== HDR LOG =="; sed -n '1,120p' "{{ hf_tmpdir.path }}/hdr_log.txt" || true
    echo "== BODY LOG =="; cat "{{ hf_upload_log_resp_file }}" || true
  changed_when: false
  failed_when: false
