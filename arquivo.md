- name: Resolver filestore vars sem recursão
  ansible.builtin.set_fact:
    filestore_env_resolved: "{{ filestore_env | default('dev') }}"
    filestore_base_dir_resolved: >-
      {{
        filestore_base_dir
          | default((filestore_env | default('dev')) ~ '/' ~ deployment_ref)
      }}
