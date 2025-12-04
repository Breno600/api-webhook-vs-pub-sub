---
# ansible/predeploy_from_execution.yml
#
# Playbook da PIPELINE 1
# - Lê o arquivo de execução (execution/machine_list_*.yml)
# - Para cada máquina:
#     * Lê o YAML em machine/<nome>.yml
#     * Cria/atualiza um JSON de status em status/<TAG>/predeploy-<nome>.json
#       primeiro com status "queued", depois "success" ou "failed"
# - Não conecta nas máquinas ainda (é só a etapa de pré-deploy/planejamento)

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true   # preciso do ansible_date_time para timestamp
  vars:
    # estes vêm da pipeline via -e
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

    # Garante pasta status/<TAG>
    - name: "Criar diretório de status da TAG"
      file:
        path: "{{ status_dir }}"
        state: directory
        mode: "0755"

    # Carrega o YAML de execução (lista de máquinas)
    # ex: execution/machine_list_dev.yml
    # machines:
    #   - sitef-01
    #   - sitef-02
    - name: "Carregar arquivo de execução"
      include_vars:
        file: "{{ repo_root }}/{{ execution_file_name }}"
        name: execution_cfg

    - name: "Validar lista de máquinas do arquivo de execução"
      debug:
        var: execution_cfg.machines

    - name: "Falhar se não tiver máquinas no arquivo de execução"
      fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: >
        execution_cfg.machines is not defined or
        execution_cfg.machines | length == 0

    # ------------------------------------------------------------------
    # Loop principal: para cada máquina listada no arquivo de execução
    # ------------------------------------------------------------------
    - name: "Processar máquinas do arquivo de execução"
      vars:
        machine_name: "{{ item }}"
        machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
        status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
      block:

        - name: "Carregar config da máquina {{ machine_name }}"
          include_vars:
            file: "{{ machine_file }}"
            name: machine_cfg

        - name: "Criar status inicial (queued) para {{ machine_name }}"
          copy:
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

        # Aqui entra a parte "pesada" do pré-deploy (download, backup etc.)
        # Neste exemplo, deixei só um echo/sleep pra você adaptar depois.
        - name: "Executar pré-deploy (simulado) para {{ machine_name }}"
          shell: |
            echo "Predeploy para {{ machine_name }} (host={{ machine_cfg.host }})"
            # aqui no futuro entra o script que baixa do Nexus, faz backup etc.
            sleep 3
          register: predeploy_result
          changed_when: true

        - name: "Atualizar status para success de {{ machine_name }}"
          copy:
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

      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: item