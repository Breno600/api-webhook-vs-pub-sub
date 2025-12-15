- name: "Upload File Store (predeploy) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    machine_status_file: "{{ machine_status_file }}"
    log_content: |
      ---- pipeline pre deploy ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=predeploy
      rc={{ init_parallel_result.rc | default(1) }}

      ---- stdout ----
      {{ init_parallel_result.stdout | default('') }}

      ---- stderr ----
      {{ init_parallel_result.stderr | default('') }}
      ---- pipeline pre deploy ----
    status_tag_value: "{{ (deployment_ref | lower) ~ ':pre-deploy:' ~ ('ok' if init_parallel_result.rc == 0 else 'error') }}"
    extra_tags: "{{ [ (deployment_ref | lower) ~ ':deploy:pending' ] if init_parallel_result.rc == 0 else [] }}"


vars:
  hfs_current_machine: "{{ current_machine }}"
