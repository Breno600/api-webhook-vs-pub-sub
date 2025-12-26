- name: "Rollback | Upload JSON + LOG para Harness File Store"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    # mesma regra do deploy: não referencie a própria var
    current_machine: "{{ machine_name | string | trim }}"

    # mesma regra do deploy: não passe machine_status_file referenciando ele mesmo
    machine_status_file: >-
      {{ (status_dir | default(status_dir_resolved)) ~ '/'
         ~ (machine_name | string | trim) ~ '/status.json' }}

    # o harness_filestore_upload.yml valida isso com esse nome
    log_content: "{{ rollback_log_content }}"

    # constante (igual deploy)
    stage_name: "rollback"

    # sem referenciar stage_name aqui (igual deploy)
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':rollback:' ~ ('ok' if (init_rollback_result.rc | default(1)) == 0 else 'error') }}

    extra_tags: []
