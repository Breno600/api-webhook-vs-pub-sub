- name: "Harness | Definir hfs_tag_safe (sanitizar TAG p/ nome/identifier)"
  ansible.builtin.set_fact:
    hfs_tag_safe: >-
      {{
        (deployment_ref_resolved | default(deployment_ref | default('')) | string | trim)
        | regex_replace('[^A-Za-z0-9_]', '_')
      }}
    hfs_run_id_safe: >-
      {{
        (hfs_run_id_resolved | default(hfs_run_id | default(run_id | default(ansible_date_time.epoch))) | string | trim)
        | regex_replace('[^A-Za-z0-9_]', '_')
      }}
