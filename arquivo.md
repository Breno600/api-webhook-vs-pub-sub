- name: "Deploy | Upload JSON + LOG para Harness File Store"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    # passa DIRETO do input pra nunca virar auto-referência
    current_machine: "{{ machine_name | string | trim }}"

    machine_status_file: "{{ status_file }}"
    log_content: "{{ log_content_to_upload }}"
    stage_name: "{{ stage_name }}"
    hf_stage_task: "{{ stage_name }}"
    filestore_env: "{{ filestore_env }}"
    filestore_base_dir: "{{ filestore_base_dir }}"
    deployment_ref_folder: "{{ deployment_ref }}"
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':' ~ stage_name ~ ':' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}
    extra_tags: []
