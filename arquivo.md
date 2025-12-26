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


- name: "Harness | Detectar DUPLICATE_FIELD REF (robusto)"
  ansible.builtin.set_fact:
    hfs_ref_http_code: "{{ (hfs_ref_create_http.stdout | default('') | string | trim) }}"
    hfs_ref_is_duplicate: >-
      {{
        (hfs_ref_http_code == '400')
        and (
          (
            (hfs_ref_create_body.stdout | default(''))
            | regex_search('\"code\"\\s*:\\s*\"DUPLICATE_FIELD\"')
            | default('')
          ) | length > 0
        )
      }}



##############################
E troca os teus when: hfs_ref_is_duplicate | bool por:

when: (hfs_ref_is_duplicate | default(false)) | bool

##############################

when:
  - (hfs_ref_http_code | default('') | trim) not in hfs_ok_create_codes
  - not (hfs_ref_is_duplicate | bool)


##############################

- name: "Harness | Detectar DUPLICATE_FIELD LEAF (robusto)"
  ansible.builtin.set_fact:
    hfs_leaf_http_code: "{{ (hfs_leaf_create_http.stdout | default('') | string | trim) }}"
    hfs_leaf_is_duplicate: >-
      {{
        (hfs_leaf_http_code == '400')
        and (
          (
            (hfs_leaf_create_body.stdout | default(''))
            | regex_search('\"code\"\\s*:\\s*\"DUPLICATE_FIELD\"')
            | default('')
          ) | length > 0
        )
      }}
