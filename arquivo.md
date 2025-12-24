- name: "Predeploy | Definir status_tag_value (tag usada no File Store)"
  ansible.builtin.set_fact:
    status_tag_value: "{{ 'predeploy:ok' if (init_parallel_result.rc | default(1)) == 0 else 'predeploy:error' }}"
