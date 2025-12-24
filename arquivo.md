- name: "Harness | Resolver paths (log/status)"
  ansible.builtin.set_fact:
    hf_log_path: >-
      {{
        filestore_log_path
        | default(hf_base_dir ~ '/' ~ (hf_deploy_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '.log')
        | string | trim
      }}
    hf_status_path: >-
      {{
        filestore_status_path
        | default(hf_base_dir ~ '/' ~ (hf_deploy_ref | lower) ~ '-' ~ (hf_machine | lower) ~ '-' ~ hf_env ~ '-' ~ hf_stage ~ '.json')
        | string | trim
      }}
  when: hf_can_upload

- name: "Harness | Resolver nomes (a partir dos paths)"
  ansible.builtin.set_fact:
    hf_log_name: "{{ (hf_log_path | regex_replace('^/+','')) | replace('/','__') }}"
    hf_status_name: "{{ (hf_status_path | regex_replace('^/+','')) | replace('/','__') }}"
  when: hf_can_upload

- name: "Harness | Tags (lista)"
  ansible.builtin.set_fact:
    hf_tags_list: >-
      {{
        (
          [ status_tag_value | default('') ]
          + (extra_tags | default([]))
          + [
              (hf_deploy_ref | lower) ~ ':stage:' ~ hf_stage,
              (hf_deploy_ref | lower) ~ ':machine:' ~ (hf_machine | lower),
              (hf_deploy_ref | lower) ~ ':env:' ~ hf_env
            ]
        )
        | map('string') | map('trim') | reject('equalto','') | list | unique
      }}
  when: hf_can_upload

- name: "Harness | Tags (string)"
  ansible.builtin.set_fact:
    hf_tags: "{{ hf_tags_list | join(',') }}"
  when: hf_can_upload
