- name: "Inicializar lista de máquinas para deploy"
  ansible.builtin.set_fact:
    machines_to_deploy: []

- name: "Montar lista de máquinas com predeploy success (machine ou machine_name)"
  ansible.builtin.set_fact:
    machines_to_deploy: "{{ machines_to_deploy + [ (item.machine_name | default(item.machine)) ] }}"
  loop: "{{ predeploy_jsons }}"
  when:
    - item.status is defined
    - item.status == "success"
