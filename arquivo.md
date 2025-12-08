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

    # Nexus
    nexus_base_url: "https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"
    nexus_user: "{{ nexus_user | default('AS4hZF20') }}"
    nexus_password: "{{ nexus_password | default('l7WwGfJd_Grmh-5Kn7B__U8nqgdNWh1XhrYtVQQ5I_6k') }}"

  tasks:

    - name: "Mostrar variáveis de entrada"
      ansible.builtin.debug:
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
      ansible.builtin.debug:
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
        nexus_base_url: "{{ nexus_base_url }}"
        nexus_user: "{{ nexus_user }}"
        nexus_password: "{{ nexus_password }}"
