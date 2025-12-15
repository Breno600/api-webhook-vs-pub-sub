- name: "Harness | Garantir folder ENV"
  ansible.builtin.shell: |
    curl -sS -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier=Root" \
      -F "identifier={{ env_lower }}" \
      -F "tags={{ harness_tags | to_json }}"
  register: hfs_env_create_http
  changed_when: hfs_env_create_http.stdout in ['200','201']
  failed_when: hfs_env_create_http.stdout not in ['200','201','409']

- name: "Harness | Garantir folder DEPLOYMENT_REF"
  ansible.builtin.shell: |
    curl -sS -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_lower }}" \
      -F "identifier={{ deployment_ref_folder }}" \
      -F "tags={{ harness_tags | to_json }}"
  register: hfs_ref_create_http
  changed_when: hfs_ref_create_http.stdout in ['200','201']
  failed_when: hfs_ref_create_http.stdout not in ['200','201','409']
