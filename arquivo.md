- name: "Deploy | Upload JSON + LOG para Harness File Store"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    machine_status_file: "{{ status_file }}"
    log_content: "{{ log_content_to_upload }}"
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':deploy:' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}
    extra_tags: []
