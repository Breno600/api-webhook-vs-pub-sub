- name: "Mostrar últimas linhas do log do predeploy (sempre)"
  ansible.builtin.shell: |
    LOG="/opt/SoftwareExpress/sitef-pipeline/deploy/logs/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.log"
    echo "===== tail -n 200 $LOG ====="
    tail -n 200 "$LOG" 2>/dev/null || echo "log não encontrado"
  become: true
  delegate_to: "{{ current_machine }}"
  register: predeploy_tail
  changed_when: false

- name: "DEBUG predeploy tail"
  ansible.builtin.debug:
    var: predeploy_tail.stdout_lines



-------

- name: "Mostrar últimas linhas do log do rollback (sempre)"
  ansible.builtin.shell: |
    LOG="/opt/SoftwareExpress/sitef-pipeline/rollback/logs/{{ deployment_ref | lower }}-{{ current_machine }}-{{ (filestore_env | default(filestore_env_resolved)) }}-rollback.log"
    echo "===== tail -n 200 $LOG ====="
    tail -n 200 "$LOG" 2>/dev/null || echo "log não encontrado"
  become: true
  delegate_to: "{{ current_machine }}"
  register: rollback_tail
  changed_when: false

- name: "DEBUG rollback tail"
  ansible.builtin.debug:
    var: rollback_tail.stdout_lines


