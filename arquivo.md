---
# ansible/predeploy_from_execution.yml
#
# PIPELINE 1 - PREDEPLOY (PARTE LEVE)
# - Lê execution/<arquivo>.yml (lista de máquinas)
# - Para cada máquina chama predeploy_per_machine.yml (em loop)
# - Cada máquina ganha um JSON em status/<TAG>/predeploy-<machine>.json

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true   # preciso de ansible_date_time p/ timestamp

  vars:
    # Vêm da pipeline via -e
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

  tasks:

    - name: "Mostrar variáveis de entrada"
      debug:
        msg:
          - "execution_file_name = {{ execution_file_name }}"
          - "deployment_ref     = {{ deployment_ref }}"
          - "repo_root          = {{ repo_root }}"
          - "status_dir         = {{ status_dir }}"

    - name: "Criar diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir }}"
        state: directory
        mode: "0755"

    # Carrega o arquivo de execução
    # execution/machine_list_dev.yml
    # machines:
    #   - sitef-01
    #   - sitef-02
    - name: "Carregar arquivo de execução"
      ansible.builtin.include_vars:
        file: "{{ repo_root }}/{{ execution_file_name }}"
        name: execution_cfg

    - name: "Exibir lista de máquinas"
      debug:
        var: execution_cfg.machines

    - name: "Falhar se não tiver máquinas no arquivo de execução"
      ansible.builtin.fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: >
        execution_cfg.machines is not defined or
        execution_cfg.machines | length == 0

    # ------------------------------------------------------------------
    # Loop chamando o arquivo de tarefas por máquina
    # ------------------------------------------------------------------
    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name
      vars:
        repo_root: "{{ repo_root }}"
        status_dir: "{{ status_dir }}"
        deployment_ref: "{{ deployment_ref }}"




---
# ansible/predeploy_per_machine.yml
#
# Tarefas executadas para CADA máquina no pré-deploy.
# Variáveis de contexto:
#   - machine_name  (vem do loop)
#   - repo_root
#   - status_dir
#   - deployment_ref

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Criar status inicial (queued) para {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}"
      }

# Aqui você coloca a parte “pesada” do pré-deploy (download do Nexus, backup, etc.)
# Por enquanto deixei só um exemplo com echo/sleep pra você validar o fluxo.
- name: "Executar pré-deploy (simulado) para {{ machine_name }}"
  ansible.builtin.shell: |
    echo "Predeploy para {{ machine_name }} (host={{ machine_cfg.host }})"
    # TODO: baixar artefatos do Nexus, fazer backup, etc.
    sleep 3
  register: predeploy_result
  changed_when: true

- name: "Atualizar status para success de {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "stdout": {{ predeploy_result.stdout | to_json }},
        "stderr": {{ predeploy_result.stderr | to_json }}
      }
