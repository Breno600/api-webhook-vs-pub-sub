- name: Harness | Resolver arquivos locais recebidos (sem recursão)
  when: hf_can_upload
  ansible.builtin.set_fact:
    hf_status_file_candidate: "{{ hf_machine_status_file | default('') }}"
    hf_log_file_candidate: "{{ hf_pipeline_log_file | default('') }}"

- name: Harness | Se log não foi passado, derivar do diretório do status.json
  when: hf_can_upload and (hf_log_file_candidate | length == 0) and (hf_status_file_candidate | length > 0)
  ansible.builtin.set_fact:
    hf_log_file_candidate: "{{ (hf_status_file_candidate | dirname) ~ '/pipeline.log' }}"

- name: Harness | Stat status.json / pipeline.log
  when: hf_can_upload
  ansible.builtin.stat:
    path: "{{ item }}"
  loop:
    - "{{ hf_status_file_candidate }}"
    - "{{ hf_log_file_candidate }}"
  register: hf_stat_candidates
  failed_when: false
