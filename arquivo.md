- name: "Deploy | Upload JSON + LOG para Harness File Store"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    # nunca passe current_machine: "{{ current_machine }}"
    current_machine: "{{ machine_name | string | trim }}"

    machine_status_file: "{{ status_file }}"
    log_content: "{{ log_content_to_upload }}"

    # nunca passe stage_name: "{{ stage_name }}"
    stage_name: "deploy"

    # não referencie stage_name aqui também
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':deploy:' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}

    extra_tags: []
