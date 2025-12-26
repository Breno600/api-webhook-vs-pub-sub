- name: "Upload JSON + LOG do ROLLBACK para Harness File Store (via Ansible) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine_in: "{{ current_machine }}"
    deployment_ref_in: "{{ deployment_ref }}"
    filestore_env_in: "{{ filestore_env }}"
    stage_name_in: "rollback"

    machine_status_file_in: "{{ machine_status_file }}"
    log_content_in: "{{ rollback_log_content }}"
    status_tag_value_in: "{{ (deployment_ref | lower) ~ ':rollback:' ~ ('ok' if init_rollback_result.rc == 0 else 'error') }}"
    extra_tags_in: []
