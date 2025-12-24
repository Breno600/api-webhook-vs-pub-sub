- name: "Harness | Definir diretório base de status (safe)"
  ansible.builtin.set_fact:
    hf_status_root: "{{ status_dir_resolved | default(status_dir | default('')) }}"
  when: hf_can_upload

- name: "Harness | Descobrir status.json local (sem usar machine_status_file)"
  ansible.builtin.find:
    paths: "{{ hf_status_root }}"
    recurse: true
    file_type: file
    patterns:
      - "status.json"
      - "*status*.json"
  register: hf_status_find
  when: hf_can_upload and (hf_status_root | length > 0)
  failed_when: false

- name: "Harness | Escolher status.json mais recente (preferindo a máquina)"
  ansible.builtin.set_fact:
    hf_local_status_file: >-
      {{
        (
          (
            hf_status_find.files
            | selectattr('path', 'search', (machine_name_resolved | default(machine_name | default(''))))
            | list
            | sort(attribute='mtime', reverse=true)
            | map(attribute='path')
            | list
            | first
          )
          | default(
              (
                hf_status_find.files
                | sort(attribute='mtime', reverse=true)
                | map(attribute='path')
                | list
                | first
              ),
              true
            )
        )
        | default('', true)
      }}
  when: hf_can_upload and (hf_status_find.files is defined) and (hf_status_find.files | length > 0)


- name: "Harness | Ler status.json (não falhar)"
  ansible.builtin.slurp:
    src: "{{ hf_local_status_file }}"
  register: hf_status_slurp
  when: hf_can_upload and (hf_local_status_file | length > 0)
  failed_when: false
