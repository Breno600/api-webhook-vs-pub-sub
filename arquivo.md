- name: "Harness | Resolver arquivos locais (anti-recursão)"
  ansible.builtin.set_fact:
    hf_local_status_file: >-
      {{
        (
          machine_status_file
          if (machine_status_file is defined
              and (machine_status_file|string|trim) != ''
              and ('{{' not in (machine_status_file|string)))
          else
            (
              status_json_file
              if (status_json_file is defined
                  and (status_json_file|string|trim) != ''
                  and ('{{' not in (status_json_file|string)))
              else
                (lookup('env','MACHINE_STATUS_FILE') | default('', true))
            )
        )
      }}
  when: hf_can_upload

- name: "Harness | Stat status.json (não falhar)"
  ansible.builtin.stat:
    path: "{{ hf_local_status_file }}"
  register: hf_status_stat
  when: hf_can_upload and (hf_local_status_file | length > 0)
  failed_when: false


- name: "Harness | Ler status.json (não falhar)"
  ansible.builtin.slurp:
    src: "{{ hf_local_status_file }}"
  register: hf_status_slurp
  when: hf_can_upload
        and (hf_local_status_file | length > 0)
        and (hf_status_stat.stat.exists | default(false))
  failed_when: false
