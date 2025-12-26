- name: "Harness | Garantir folder ENV (POST)"
  ansible.builtin.shell: |
    set -eo pipefail

    : "${HARNESS_X_API_KEY:?HARNESS_X_API_KEY vazio/ausente no environment}"

    http="$(curl --http1.1 -sS -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: ${HARNESS_X_API_KEY}" \
      -H "Expect:" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"

    echo "${http}"
  args:
    executable: /bin/bash
  environment:
    HARNESS_X_API_KEY: "{{ harness_x_api_key }}"
  register: hfs_env_create_http
  changed_when: hfs_env_create_http.stdout in ['200','201']
  failed_when: false
  no_log: false


- name: "Harness | Debug ENV curl (rc/stdout/stderr)"
  ansible.builtin.debug:
    msg:
      - "rc={{ hfs_env_create_http.rc }}"
      - "http_code={{ hfs_env_create_http.stdout | default('') | trim }}"
      - "stderr={{ hfs_env_create_http.stderr | default('') | trim }}"

- name: "Harness | Mostrar body ENV (debug)"
  ansible.builtin.shell: "sed -e 's/[[:cntrl:]]//g' /tmp/hfs_env_create.out 2>/dev/null | head -c 2000 || true"
  args:
    executable: /bin/bash
  register: hfs_env_body_dbg
  changed_when: false
  failed_when: false

- name: "Harness | Debug body ENV (primeiros 2000 chars)"
  ansible.builtin.debug:
    msg: "{{ hfs_env_body_dbg.stdout | default('') }}"
