- name: "Predeploy | Upload JSON + LOG para Harness File Store (sempre)"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    machine_status_file: "{{ machine_status_file }}"
    log_content: "{{ pipeline_log_content_full }}"
    status_tag_value: "{{ deployment_ref_lower ~ ':predeploy:' ~ ('ok' if (init_parallel_result.rc | default(1)) == 0 else 'error') }}"
    extra_tags: "{{ [ deployment_ref_lower ~ ':deploy:pending' ] if (init_parallel_result.rc | default(1)) == 0 else [] }}"
