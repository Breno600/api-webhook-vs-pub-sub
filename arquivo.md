- name: "Harness | Ler body ENV (sempre)"
  ansible.builtin.shell: "cat /tmp/hfs_env_create.out 2>/dev/null || true"
  register: hfs_env_create_body
  changed_when: false
  failed_when: false

- name: "Harness | Detectar DUPLICATE_FIELD ENV"
  ansible.builtin.set_fact:
    hfs_env_is_duplicate: >-
      {{
        (hfs_env_create_http.stdout | default('')) == '400'
        and ('"code":"DUPLICATE_FIELD"' in (hfs_env_create_body.stdout | default('')))
      }}

- name: "Harness | Dump do Root via /folder (para achar identifier do ENV existente)"
  ansible.builtin.shell: |
    set -euo pipefail
    curl --http1.1 -sS \
      --request POST \
      "{{ harness_api_base }}/folder?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -H "Content-Type: application/json" \
      -H "Expect:" \
      -d '{
        "identifier": "{{ harness_filestore_root_identifier_resolved }}",
        "parentIdentifier": "",
        "name": "{{ harness_filestore_root_identifier_resolved }}",
        "type": "FOLDER"
      }'
  args:
    executable: /bin/bash
  register: hfs_root_folder_dump
  changed_when: false
  failed_when: false
  when: hfs_env_is_duplicate | bool

- name: "Harness | Parse children do Root"
  ansible.builtin.set_fact:
    hfs_root_obj: "{{ (hfs_root_folder_dump.stdout | default('{}')) | from_json }}"
    hfs_root_children: >-
      {{
        (
          (hfs_root_obj.data.children if (hfs_root_obj.data is defined) else hfs_root_obj.children)
          | default([])
        )
      }}
  when: hfs_env_is_duplicate | bool

- name: "Harness | Extrair identifier existente do ENV"
  ansible.builtin.set_fact:
    env_identifier_resolved: >-
      {{
        (
          hfs_root_children
          | selectattr('type','equalto','FOLDER')
          | selectattr('name','equalto', env_lower)
          | map(attribute='identifier')
          | list
          | first
        ) | default('')
      }}
  when: hfs_env_is_duplicate | bool

- name: "Harness | Definir ENV identifier final (criado ou existente)"
  ansible.builtin.set_fact:
    env_identifier_final: >-
      {{
        (env_identifier_resolved | string | trim)
        if (hfs_env_is_duplicate | bool)
        else env_identifier
      }}

#########################################################

when:
  - (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes
  - not (hfs_env_is_duplicate | bool)


- name: "Harness | Falhar se ENV já existe mas não consegui resolver identifier"
  ansible.builtin.fail:
    msg: |
      Folder ENV "{{ env_lower }}" já existe, mas não consegui resolver o identifier pelo endpoint /folder.
      root_children_count={{ (hfs_root_children | default([])) | length }}
  when:
    - hfs_env_is_duplicate | bool
    - (env_identifier_final | default('') | length) == 0
