# predeploy_from_execution.yml
#
# PIPELINE 1 - PREDEPLOY
# - Lê execution/<arquivo>.yml
# - Para cada máquina:
#   * carrega machine + package
#   * exporta env_vars (merge)
#   * prepara área /opt/SoftwareExpress/sitef-pipeline/deploy
#   * limpa scripts
#   * copia package.yml
#   * baixa componentes do Nexus
#   * copia scripts do repo
#   * executa init_parallel.sh
#   * gera status JSON
#   * envia JSON + LOG para Harness File Store
#
# Objetivo deste arquivo:
# - Evitar loop recursivo de variáveis no include_tasks
# - Centralizar resolução de paths
# - Deixar o playbook estável mesmo quando variáveis são sobrescritas por pipeline

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # -------------------------------
    # Entrada principal do pipeline
    # -------------------------------
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    # -------------------------------
    # Paths do repositório
    # -------------------------------
    # playbook_dir aponta para .../ansible
    # repo_root deve ser a raiz do repo
    repo_root_resolved: "{{ playbook_dir }}/.."

    # Diretório de status local no host onde o pipeline roda
    status_dir_resolved: "{{ repo_root_resolved }}/status/{{ deployment_ref }}"

    # -------------------------------
    # Nexus
    # -------------------------------
    # Ideal vir via Harness Secrets
    nexus_base_url: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user: "{{ nexus_user | default(omit) }}"
    nexus_password: "{{ nexus_password | default(omit) }}"

    # -------------------------------
    # Harness File Store (base)
    # -------------------------------
    # dev|cat|prd
    filestore_env: "{{ filestore_env | default('dev') }}"
    # default lógico baseado no env + deployment_ref
    filestore_base_dir: "{{ filestore_base_dir | default('') }}"

  tasks:
    # ---------------------------------------
    # Resolve filestore sem recursão
    # ---------------------------------------
    - name: Resolver filestore vars sem recursão
      ansible.builtin.set_fact:
        filestore_env_resolved: "{{ filestore_env | default('dev') }}"
        filestore_base_dir_resolved: >-
          {{
            (filestore_base_dir | trim)
              if (filestore_base_dir is defined and (filestore_base_dir | trim) | length > 0)
              else ((filestore_env | default('dev')) ~ '/' ~ deployment_ref)
          }}

    # ---------------------------------------
    # Mostrar variáveis de entrada
    # ---------------------------------------
    - name: "Mostrar variáveis de entrada"
      ansible.builtin.debug:
        msg:
          - "execution_file_name     = {{ execution_file_name }}"
          - "deployment_ref          = {{ deployment_ref }}"
          - "repo_root_resolved      = {{ repo_root_resolved }}"
          - "status_dir_resolved     = {{ status_dir_resolved }}"
          - "filestore_env_resolved  = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved = {{ filestore_base_dir_resolved }}"
          - "nexus_base_url          = {{ nexus_base_url }}"

    # ---------------------------------------
    # Criar diretório de status da TAG
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
    # Executar pré-deploy por máquina
    #
    # IMPORTANTE:
    # - NÃO repassar "status_dir: {{ status_dir }}" (autorreferência)
    # - Use apenas variáveis resolvidas
    # ---------------------------------------
    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name
      vars:
        # Passamos o que o tasks file precisa
        deployment_ref: "{{ deployment_ref }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"

        nexus_base_url: "{{ nexus_base_url }}"
        # Se omit vier do pipeline, aqui garantimos string vazia para tasks que esperam string
        nexus_user: "{{ nexus_user | default('') }}"
        nexus_password: "{{ nexus_password | default('') }}"

        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"
