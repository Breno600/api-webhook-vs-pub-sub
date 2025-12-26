- name: "Deploy | Zerar init_deploy.log antes de executar (evitar acumulo)"
  become: true
  ansible.builtin.shell: |
    : > "{{ host_deploy_init_log }}"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  changed_when: true


- name: "Deploy | Executar init_deploy.sh no host (com tee)"
  become: true
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ host_deploy_scripts }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_deploy.sh 2>&1 | tee "{{ host_deploy_init_log }}"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  environment: "{{ effective_env }}"
  register: deploy_result
  ignore_errors: true
  changed_when: true
