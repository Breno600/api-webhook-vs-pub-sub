## predeploy_from_execution.yml


# predeploy_from_execution.yml
# =====================================================================================
# PIPELINE 1 - PREDEPLOY (a partir do arquivo de execução)
# =====================================================================================

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # -------------------------------
    # Entradas principais (RESOLVIDAS)
    # -------------------------------
    execution_file_name_resolved: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref_resolved: "{{ deployment_ref | default('DEV000000001') }}"

    # -------------------------------
    # Paths do repositório
    # -------------------------------
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ (playbook_dir ~ '/..') }}/status/{{ deployment_ref_resolved }}"

    # -------------------------------
    # Nexus (ideal vir de secrets do Harness)
    # -------------------------------
    nexus_base_url_resolved: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user_resolved: "{{ nexus_user | default('') }}"
    nexus_password_resolved: "{{ nexus_password | default('') }}"

    # -------------------------------
    # Harness (pode vir por -e ou env)
    # -------------------------------
    harness_endpoint_resolved: "{{ harness_endpoint | default('') }}"
    harness_account_id_resolved: "{{ harness_account_id | default('') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('') }}"
    harness_api_key_resolved: "{{ harness_api_key | default('') }}"
    harness_x_api_key_resolved: "{{ harness_x_api_key | default('') }}"

    # Stage (usado pelo harness upload)
    hf_stage_name: "predeploy"

  tasks:
    # ---------------------------------------
    # Resolver File Store (SAFE, sem tocar em filestore_base_dir)
    # ---------------------------------------
    - name: "Resolver filestore_env_resolved (safe)"
      ansible.builtin.set_fact:
        filestore_env_resolved: >-
          {{
            (filestore_env | default('', true) | string | trim)
              if ((filestore_env | default('', true) | string | trim) | length > 0)
              else 'dev'
          }}

    - name: "Resolver filestore_base_dir_resolved (safe via override)"
      ansible.builtin.set_fact:
        filestore_base_dir_resolved: >-
          {{
            (filestore_base_dir_override | default('', true) | string | trim)
              if ((filestore_base_dir_override | default('', true) | string | trim) | length > 0)
              else (filestore_env_resolved ~ '/' ~ deployment_ref_resolved)
          }}

    # ---------------------------------------
    # Mostrar variáveis resolvidas (debug)
    # ---------------------------------------
    - name: "Mostrar variáveis de entrada e resolvidas"
      ansible.builtin.debug:
        msg:
          - "execution_file_name_resolved = {{ execution_file_name_resolved }}"
          - "deployment_ref_resolved      = {{ deployment_ref_resolved }}"
          - "repo_root_resolved           = {{ repo_root_resolved }}"
          - "status_dir_resolved          = {{ status_dir_resolved }}"
          - "filestore_env_resolved       = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved  = {{ filestore_base_dir_resolved }}"
          - "nexus_base_url_resolved      = {{ nexus_base_url_resolved }}"
      changed_when: false

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
        file: "{{ repo_root_resolved }}/{{ execution_file_name_resolved }}"
        name: execution_cfg

    # ---------------------------------------
    # Validar se há máquinas
    # ---------------------------------------
    - name: "Falhar se não tiver máquinas no arquivo de execução"
      ansible.builtin.fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: execution_cfg.machines is not defined or execution_cfg.machines | length == 0

    # ---------------------------------------
    # Executar PREDEPLOY por máquina (SEQUENCIAL aqui; paralelo exige refactor por hosts)
    # ---------------------------------------
    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name
      vars:
        deployment_ref: "{{ deployment_ref_resolved }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"

        nexus_base_url: "{{ nexus_base_url_resolved }}"
        nexus_user: "{{ nexus_user_resolved }}"
        nexus_password: "{{ nexus_password_resolved }}"

        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"

        # Harness vars (podem ser vazios; o include resolve com fallback/env)
        harness_endpoint: "{{ harness_endpoint_resolved }}"
        harness_account_id: "{{ harness_account_id_resolved }}"
        harness_org_id: "{{ harness_org_id_resolved }}"
        harness_project_id: "{{ harness_project_id_resolved }}"
        harness_api_key: "{{ harness_api_key_resolved }}"
        harness_x_api_key: "{{ harness_x_api_key_resolved }}"
        hf_stage_name: "{{ hf_stage_name }}"
