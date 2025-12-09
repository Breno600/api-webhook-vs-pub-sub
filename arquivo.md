- name: Resolver filestore vars sem recursão
  ansible.builtin.set_fact:
    filestore_env_resolved: "{{ filestore_env | default('dev') }}"
    filestore_base_dir_resolved: >-
      {{
        filestore_base_dir
          | default(filestore_env_resolved ~ '/' ~ deployment_ref)
      }}

- name: Mostrar variáveis de entrada
  ansible.builtin.debug:
    msg:
      - "filestore_env      = {{ filestore_env_resolved }}"
      - "filestore_base_dir = {{ filestore_base_dir_resolved }}"
      - "deployment_ref     = {{ deployment_ref }}"
