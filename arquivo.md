# predeploy_from_execution.yml
# =====================================================================================
# PIPELINE 1 - PREDEPLOY (a partir do arquivo de execução) - PARALELO POR MÁQUINA
# =====================================================================================

- name: "Predeploy a partir do arquivo de execução (resolver lista)"
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
                ) ~ '/' ~ deployment_ref_resolved
              )
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
    # Registrar hosts dinâmicos (grupo alvo) -> habilita paralelo com forks
    # ---------------------------------------
    - name: "Registrar máquinas no grupo predeploy_targets (para paralelizar com forks)"
      ansible.builtin.add_host:
        name: "{{ item }}"
        groups: predeploy_targets
        ansible_connection: local
      loop: "{{ execution_cfg.machines }}"
      changed_when: false

    - name: "DEBUG | Máquinas alvo"
      ansible.builtin.debug:
        msg:
          - "machines={{ execution_cfg.machines }}"
          - "total={{ execution_cfg.machines | length }}"
      changed_when: false


# =====================================================================================
# PLAY 2 - PREDEPLOY PARALELO POR MÁQUINA
# =====================================================================================
- name: "Predeploy por máquina (PARALELO)"
  hosts: predeploy_targets
  connection: local
  gather_facts: true
  strategy: free

  vars:
    # Repassa os mesmos valores (os -e do Harness continuam valendo)
    execution_file_name_resolved: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref_resolved: "{{ deployment_ref | default('DEV000000001') }}"

    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ (playbook_dir ~ '/..') }}/status/{{ deployment_ref_resolved }}"

    nexus_base_url_resolved: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user_resolved: "{{ nexus_user | default('') }}"
    nexus_password_resolved: "{{ nexus_password | default('') }}"

    harness_endpoint_resolved: "{{ harness_endpoint | default('') }}"
    harness_account_id_resolved: "{{ harness_account_id | default('') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('') }}"
    harness_api_key_resolved: "{{ harness_api_key | default('') }}"
    harness_x_api_key_resolved: "{{ harness_x_api_key | default('') }}"

    hf_stage_name: "predeploy"

  tasks:
    - name: "Executar pré-deploy desta máquina (inventory_hostname)"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      vars:
        machine_name: "{{ inventory_hostname }}"

        deployment_ref: "{{ deployment_ref_resolved }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"

        nexus_base_url: "{{ nexus_base_url_resolved }}"
        nexus_user: "{{ nexus_user_resolved }}"
        nexus_password: "{{ nexus_password_resolved }}"

        filestore_env: >-
          {{
            (filestore_env | string | trim)
              if (filestore_env is defined and (filestore_env | string | trim) | length > 0)
              else 'dev'
          }}
        filestore_base_dir: >-
          {{
            (filestore_base_dir | string | trim)
              if (filestore_base_dir is defined and (filestore_base_dir | string | trim) | length > 0)
              else (
                (
                  (filestore_env | string | trim)
                    if (filestore_env is defined and (filestore_env | string | trim) | length > 0)
                    else 'dev'
                ) ~ '/' ~ deployment_ref_resolved
              )
          }}

        harness_endpoint: "{{ harness_endpoint_resolved }}"
        harness_account_id: "{{ harness_account_id_resolved }}"
        harness_org_id: "{{ harness_org_id_resolved }}"
        harness_project_id: "{{ harness_project_id_resolved }}"
        harness_api_key: "{{ harness_api_key_resolved }}"
        harness_x_api_key: "{{ harness_x_api_key_resolved }}"
        hf_stage_name: "{{ hf_stage_name }}"
