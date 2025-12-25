---
- name: "Deploy/Rollback por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # =====================================================================
    # INPUTS (preferência: vars > env > default)
    # =====================================================================
    deployment_ref: >-
      {{
        (deployment_ref | default('', true) | string | trim)
          if (deployment_ref is defined and (deployment_ref | string | trim) | length > 0)
          else (lookup('ansible.builtin.env','GIT_TAG') | default('DEV000000001', true) | string | trim)
      }}

    machine_name: >-
      {{
        (machine_name | default('', true) | string | trim)
          if (machine_name is defined and (machine_name | string | trim) | length > 0)
          else (
            (lookup('ansible.builtin.env','MACHINE') | default('', true) | string | trim)
              if (lookup('ansible.builtin.env','MACHINE') | default('', true) | string | trim) | length > 0
              else (lookup('ansible.builtin.env','MACHINES') | default('', true) | string | trim)
          )
      }}

    deploy_action: >-
      {{
        (deploy_action | default('', true) | string | trim)
          if (deploy_action is defined and (deploy_action | string | trim) | length > 0)
          else (
            (lookup('ansible.builtin.env','ACTION') | default('', true) | string | trim)
              if (lookup('ansible.builtin.env','ACTION') | default('', true) | string | trim) | length > 0
              else (lookup('ansible.builtin.env','STRATEGY') | default('deploy', true) | string | trim)
          )
      }}

    # Paths do repositório
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ repo_root_resolved }}/status/{{ deployment_ref }}"

    # File Store (preferência: vars > env > default)
    filestore_env_input: >-
      {{
        (filestore_env | default('', true) | string | trim)
          if (filestore_env is defined and (filestore_env | string | trim) | length > 0
          else (lookup('ansible.builtin.env','FILESTORE_ENV') | default('dev', true) | string | trim)
      }}

    filestore_base_dir_input: >-
      {{
        (filestore_base_dir | default('', true) | string | trim)
          if (filestore_base_dir is defined and (filestore_base_dir | string | trim) | length > 0
          else (lookup('ansible.builtin.env','FILESTORE_BASEDIR') | default('', true) | string | trim)
      }}

    # Nexus default (evita recursão)
    nexus_base_url_default: "https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"

  tasks:
    # -------------------------------------------------------------------
    # Normalizar deploy_action (deploy/rollback)
    # -------------------------------------------------------------------
    - name: "Normalizar deploy_action (deploy/rollback)"
      ansible.builtin.set_fact:
        deploy_action_resolved: >-
          {{
            (deploy_action | string | lower | trim)
              if (deploy_action is defined and (deploy_action | string | trim) | length > 0)
              else 'deploy'
          }}

    # -------------------------------------------------------------------
    # Resolver filestore_env e filestore_base_dir sem recursão
    # -------------------------------------------------------------------
    - name: "Resolver filestore_env e filestore_base_dir (deploy/rollback)"
      ansible.builtin.set_fact:
        filestore_env_resolved: "{{ filestore_env_input | default('dev', true) | string | trim }}"
        filestore_base_dir_resolved: >-
          {{
            (filestore_base_dir_input | string | trim)
              if (filestore_base_dir_input | string | trim) | length > 0
              else ((filestore_env_input | default('dev', true) | string | trim) ~ '/' ~ deployment_ref)
          }}

    # -------------------------------------------------------------------
    # Resolver Nexus (base_url, user, password) sem recursão
    # -------------------------------------------------------------------
    - name: "Resolver Nexus (base_url, user, password)"
      ansible.builtin.set_fact:
        nexus_base_url_resolved: >-
          {{
            (nexus_base_url | default('', true) | string | trim)
              if (nexus_base_url is defined and (nexus_base_url | string | trim) | length > 0)
              else nexus_base_url_default
          }}
        nexus_user_resolved: "{{ (nexus_user | default('', true) | string | trim) if (nexus_user is defined) else '' }}"
        nexus_password_resolved: "{{ (nexus_password | default('', true) | string | trim) if (nexus_password is defined) else '' }}"

    # -------------------------------------------------------------------
    # Debug das principais variáveis (sem vazar segredo)
    # -------------------------------------------------------------------
    - name: "Mostrar variáveis de entrada e resolvidas (deploy/rollback)"
      ansible.builtin.debug:
        msg:
          - "deployment_ref               = {{ deployment_ref }}"
          - "machine_name                 = {{ machine_name }}"
          - "deploy_action                = {{ deploy_action }}"
          - "deploy_action_resolved       = {{ deploy_action_resolved }}"
          - "repo_root_resolved           = {{ repo_root_resolved }}"
          - "status_dir_resolved          = {{ status_dir_resolved }}"
          - "filestore_env_resolved       = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved  = {{ filestore_base_dir_resolved }}"
          - "nexus_base_url_resolved      = {{ nexus_base_url_resolved }}"
          - "nexus_user_resolved          = {{ '***' if (nexus_user_resolved | length) > 0 else '(vazio)' }}"

    # -------------------------------------------------------------------
    # Garantir diretório base de status da TAG
    # -------------------------------------------------------------------
    - name: "Garantir diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir_resolved }}"
        state: directory
        mode: "0755"

    # -------------------------------------------------------------------
    # Validar machine_name
    # -------------------------------------------------------------------
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - (machine_name | string | trim) | length > 0
        fail_msg: "machine_name não informado (vars ou env MACHINE/MACHINES)"

    # -------------------------------------------------------------------
    # Branch de DEPLOY
    # -------------------------------------------------------------------
    - name: "Incluir deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      when: deploy_action_resolved == 'deploy'
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name | string | trim }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"
        hf_stage_task: "deploy"

    # -------------------------------------------------------------------
    # Branch de ROLLBACK (mantém compatível com seu padrão)
    # -------------------------------------------------------------------
    - name: "Incluir rollback por máquina"
      ansible.builtin.include_tasks: rollback_per_machine.yml
      when: deploy_action_resolved == 'rollback'
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name | string | trim }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        nexus_base_url: "{{ nexus_base_url_resolved }}"
        nexus_user: "{{ nexus_user_resolved }}"
        nexus_password: "{{ nexus_password_resolved }}"
        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"
        hf_stage_task: "rollback"
