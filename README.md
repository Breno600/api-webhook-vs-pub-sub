- name: "Harness | Garantir folder ENV (create se não existir)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "identifier={{ env_identifier }}" \
      -F "tags={{ harness_tags | to_json }}"
  args:
    executable: /bin/bash
  register: hfs_env_create_http
  changed_when: (hfs_env_create_http.stdout | default('')) is match('^2..$')
  failed_when: (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes
