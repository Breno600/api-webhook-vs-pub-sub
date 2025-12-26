- name: "Deploy | Zerar deploy.txt antes de executar (evitar acumulo)"
  become: true
  ansible.builtin.shell: |
    : > "{{ host_deploy_scripts }}/deploy.txt"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  changed_when: true


- name: "Deploy | Ler deploy.txt do host (tail) (se existir)"
  become: true
  ansible.builtin.shell: |
    tail -n 300 "{{ host_deploy_scripts }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  register: deploy_txt
  changed_when: false
  failed_when: false


- name: "Deploy | Definir log para upload (apenas execução atual)"
  ansible.builtin.set_fact:
    log_content_to_upload: "{{ deploy_log_content }}"
