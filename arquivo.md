- name: "Harness | Normalizar http_code ENV"
  ansible.builtin.set_fact:
    hfs_env_http_code: "{{ (hfs_env_create_http.stdout | default('') | string | trim) }}"
