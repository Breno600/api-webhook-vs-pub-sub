# -----------------------------------------------------------------------------
# Mostrar log no output do Harness (sem depender do FileStore)
# -----------------------------------------------------------------------------
- name: "Deploy | Mostrar caminhos do log (para visualizar no Harness)"
  ansible.builtin.debug:
    msg:
      - "LOG REMOTO (init_deploy.log): {{ host_deploy_init_log }}"
      - "LOG FILESTORE (.log):         {{ filestore_log_path }}"
      - "STATUS FILESTORE (.json):     {{ filestore_json_path }}"

- name: "Deploy | Mostrar últimas linhas do init_deploy.log no output da pipeline (sempre)"
  become: true
  ansible.builtin.shell: |
    echo "===== tail -n 200 {{ host_deploy_init_log }} ====="
    tail -n 200 "{{ host_deploy_init_log }}" 2>/dev/null || echo "init_deploy.log nao existe"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  register: deploy_tail
  changed_when: false
  failed_when: false

- name: "Deploy | DEBUG deploy tail (Harness)"
  ansible.builtin.debug:
    var: deploy_tail.stdout_lines
