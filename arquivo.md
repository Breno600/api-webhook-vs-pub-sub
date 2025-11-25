- name: "Registrar hosts do grupo (dinâmico)"
  ansible.builtin.add_host:
    name: "{{ host_ip }}"
    ansible_host: "{{ host_ip }}"
    ansible_user: ansible
    ansible_ssh_private_key_file: "~/.ssh/id_rsa"
    groups: dynamic_group
  loop: "{{ group_cfg.machines }}"
  loop_control:
    loop_var: machine_file
  vars:
    host_ip: "{{ machine_file
                | regex_replace('^ip-', '')
                | regex_replace('\\.ya?ml$', '') }}"