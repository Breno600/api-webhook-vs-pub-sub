- name: "Upload JSON + LOG do ROLLBACK para Harness File Store (via Ansible) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    stage_name: "rollback"
    machine_status_file: "{{ machine_status_file }}"
    log_content: "{{ rollback_log_content }}"
    status_tag_value: "{{ (deployment_ref | lower) ~ ':rollback:' ~ ('ok' if init_rollback_result.rc == 0 else 'error') }}"
    extra_tags: []
