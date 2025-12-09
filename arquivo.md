- name: Resolver filestore_env
  ansible.builtin.set_fact:
    filestore_env_resolved: "{{ filestore_env | default('dev') }}"

- name: Resolver filestore_base_dir
  ansible.builtin.set_fact:
    filestore_base_dir_resolved: >-
      {{
        filestore_base_dir
          | default(filestore_env_resolved ~ '/' ~ deployment_ref)
      }}


- name: Resolver filestore vars sem recursão
  ansible.builtin.set_fact:
    filestore_env_resolved: "{{ filestore_env | default('dev') }}"
    filestore_base_dir_resolved: >-
      {{
        filestore_base_dir
          | default((filestore_env | default('dev')) ~ '/' ~ deployment_ref)
      }}
