# =====================================================================================
# PIPELINE 1 - PREDEPLOY (a partir do arquivo de execução)
# =====================================================================================

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # -------------------------------
    # Entradas principais
    # -------------------------------
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    # -------------------------------
    # Paths do repositório
    # -------------------------------
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ (playbook_dir ~ '/..') }}/status/{{ deployment_ref }}"

    # -------------------------------
    # Nexus (ideal vir de secrets do Harness)
    # -------------------------------
    nexus_base_url: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user: "{{ nexus_user | default(omit) }}"
    nexus_password: "{{ nexus_password | default(omit) }}"

  tasks:
    # ---------------------------------------
    # Resolver File Store sem recursão
    # ---------------------------------------
    - name: "Resolver filestore_env e filestore_base_dir sem recursão"
      ansible.builtin.set_fact:
        filestore_env_resolved: >-
          {{
            (filestore_env | string | trim)
              if (filestore_env is defined and (filestore_env | string | trim) | length > 0)
              else 'dev'
          }}
        filestore_base_dir_resolved: >-
          {{
            (filestore_base_dir | string | trim)
              if (filestore_base_dir is defined and (filestore_base_dir | string | trim) | length > 0)
              else (
                (
                  (filestore_env | string | trim)
                    if (filestore_env is defined and (filestore_env | string | trim) | length > 0)
                    else 'dev'
                ) ~ '/' ~ deployment_ref
              )
          }}

    # ---------------------------------------
    # Mostrar variáveis resolvidas (debug)
    # ---------------------------------------
    - name: "Mostrar variáveis de entrada e resolvidas"
      ansible.builtin.debug:
        msg:
          - "execution_file_name          = {{ execution_file_name }}"
          - "deployment_ref               = {{ deployment_ref }}"
          - "repo_root_resolved           = {{ repo_root_resolved }}"
          - "status_dir_resolved          = {{ status_dir_resolved }}"
          - "filestore_env_resolved       = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved  = {{ filestore_base_dir_resolved }}"
          - "nexus_base_url               = {{ nexus_base_url }}"

    # ---------------------------------------
    # Criar diretório base de status local
    # ---------------------------------------
    - name: "Criar diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir_resolved }}"
        state: directory
        mode: "0755"

    # ---------------------------------------
    # Carregar arquivo de execução
    # ---------------------------------------
    - name: "Carregar arquivo de execução"
      ansible.builtin.include_vars:
        file: "{{ repo_root_resolved }}/{{ execution_file_name }}"
        name: execution_cfg

    # ---------------------------------------
    # Validar se há máquinas
    # ---------------------------------------
    - name: "Falhar se não tiver máquinas no arquivo de execução"
      ansible.builtin.fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: execution_cfg.machines is not defined or execution_cfg.machines | length == 0

    # ---------------------------------------
    # Executar PREDEPLOY por máquina
    # ---------------------------------------
    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name
      vars:
        deployment_ref: "{{ deployment_ref }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"

        nexus_base_url: "{{ nexus_base_url }}"
        nexus_user: "{{ nexus_user | default('') }}"
        nexus_password: "{{ nexus_password | default('') }}"

        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"
