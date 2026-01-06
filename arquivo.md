- name: "Predeploy | Verificar candidatos"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

- name: "Predeploy | Selecionar machine_file existente (primeiro encontrado)"
  ansible.builtin.set_fact:
    machine_file: >-
      {{
        (machine_candidates_stat.results
          | selectattr('stat.exists', 'equalto', true)
          | map(attribute='item')
          | list
          | first) | default('')
      }}

- name: "Predeploy | Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: |
      [predeploy] Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file | length == 0
