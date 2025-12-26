- name: "Harness | Normalizar http_code ENV"
  ansible.builtin.set_fact:
    hfs_env_http_code: "{{ hfs_env_create_http.stdout | default('', true) | string | trim }}"

- name: "Harness | Detectar DUPLICATE_FIELD ENV"
  ansible.builtin.set_fact:
    hfs_env_is_duplicate: >-
      {{
        (hfs_env_http_code == '400')
        and ('"code":"DUPLICATE_FIELD"' in (hfs_env_create_body.stdout | default('', true)))
      }}
