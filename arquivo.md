- name: "Harness | Definir nomes versionados (evitar DUPLICATE_FIELD) - parte 1"
  ansible.builtin.set_fact:
    # usa a tag do status (ou deployment_ref) pro nome do arquivo
    hfs_tag_safe: >-
      {{
        (status_tag_value | default(deployment_ref) | default('no_tag') | string | trim)
        | regex_replace('[^A-Za-z0-9_-]', '_')
      }}
    # run id (ou epoch) pro sufixo versionado
    hfs_run_safe: >-
      {{
        (run_id | default(ansible_date_time.epoch) | string | trim)
        | regex_replace('[^A-Za-z0-9_-]', '_')
      }}

- name: "Harness | Definir nomes versionados (evitar DUPLICATE_FIELD) - parte 2"
  ansible.builtin.set_fact:
    hfs_status_filename: "status_{{ hfs_tag_safe }}_{{ hfs_run_safe }}.json"
    hfs_log_filename:    "pipeline_{{ hfs_tag_safe }}_{{ hfs_run_safe }}.log"
