- name: "Harness | Definir nomes versionados (evitar DUPLICATE_FIELD)"
  ansible.builtin.set_fact:
    hfs_tag_safe: >-
      {{
        (status_tag_value | default(deployment_ref_resolved) | default('no_tag') | string)
        | regex_replace('[^A-Za-z0-9_-]', '_')
      }}
    hfs_run_safe: >-
      {{
        (run_id | default(ansible_date_time.epoch) | string)
        | regex_replace('[^A-Za-z0-9_-]', '_')
      }}

    hfs_status_filename: "status_{{ hfs_tag_safe }}_{{ hfs_run_safe }}.json"
    hfs_log_filename:    "pipeline_{{ hfs_tag_safe }}_{{ hfs_run_safe }}.log"


-F "name={{ hfs_status_filename }}"
-F "name={{ hfs_log_filename }}"
