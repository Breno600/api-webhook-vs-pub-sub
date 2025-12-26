- name: "Harness | Detectar DUPLICATE_FIELD ENV (robusto)"
  ansible.builtin.set_fact:
    hfs_env_http_code: "{{ (hfs_env_create_http.stdout | default('') | string | trim) }}"
    hfs_env_is_duplicate: >-
      {{
        (hfs_env_http_code == '400')
        and (
          (
            (hfs_env_create_body.stdout | default(''))
            | regex_search('\"code\"\\s*:\\s*\"DUPLICATE_FIELD\"')
            | default('')
          ) | length > 0
        )
      }}



when:
  - (hfs_env_http_code | default('') | trim) not in hfs_ok_create_codes
  - not (hfs_env_is_duplicate | bool)


http_code={{ hfs_env_http_code | default('') }}


when: (hfs_env_http_code | default('') | trim) not in hfs_ok_create_codes
