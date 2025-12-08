---
# =====================================================================
# PLAY 1 - CONTROLLER (localhost)
# Lê o JSON de status + machine/<nome>.yml e registra 1 host dinâmico
# =====================================================================
- name: "[CONTROLLER] Preparar deploy/rollback de uma máquina"
  hosts: localhost
  gather_facts: false
  connection: local

  vars:
    repo_root: "{{ playbook_dir | dirname }}"

    # Variáveis esperadas via pipeline (-e):
    #  - machine_name (ex: sitef-01)
    #  - deployment_ref (ex: DEV000000001)
    #  - deploy_action (deploy|rollback)
    machine_name: "{{ machine_name }}"
    deployment_ref: "{{ deployment_ref }}"
    deploy_action: "{{ deploy_action | default('deploy') }}"

    status_file: "{{ repo_root }}/status/{{ deployment_ref }}/predeploy-{{ machine_name }}.json"
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"

  tasks:
    - name: "Validar se arquivo de status existe"
      ansible.builtin.stat:
        path: "{{ status_file }}"
      register: st_status

    - name: "Falhar se status da máquina não existe para essa TAG"
      ansible.builtin.fail:
        msg: "Status nao encontrado para machine={{ machine_name }}, tag={{ deployment_ref }} em {{ status_file }}"
      when: not st_status.stat.exists

    - name: "Carregar JSON de status"
      ansible.builtin.set_fact:
        status_data: "{{ lookup('file', status_file) | from_json }}"

    - name: "Falhar se predeploy nao foi sucesso"
      ansible.builtin.fail:
        msg: "Predeploy da machine={{ machine_name }} esta com status='{{ status_data.status }}', nao eh permitido prosseguir."
      when: status_data.status != 'success'

    - name: "Carregar YAML da máquina"
      ansible.builtin.include_vars:
        file: "{{ machine_file }}"
        name: machine_cfg

    # ----------------------------------------------------------
    # Definir usuário e chave padrão (igual ao predeploy)
    # ----------------------------------------------------------
    - name: "Definir usuário alvo e chave padrão"
      ansible.builtin.set_fact:
        target_user: "{{ machine_cfg.user | default('ec2-user') }}"
        target_key: "{{ machine_cfg.private_key_file | default('/home/ec2-user/.ssh/id_rsa') }}"
        raw_ssh_args: "{{ machine_cfg.ssh_common_args | default('') }}"

    - name: "Montar ssh_common_args efetivo"
      ansible.builtin.set_fact:
        effective_ssh_args: >-
          {{ raw_ssh_args }}
          -o IdentitiesOnly=yes
          -i {{ target_key }}

    # ----------------------------------------------------------
    # Definir package conforme deploy_action
    # ----------------------------------------------------------
    - name: "Definir package a ser usado conforme ACTION"
      ansible.builtin.set_fact:
        selected_package: >-
          {{
            machine_cfg.package
            if deploy_action == 'deploy'
            else machine_cfg.rollback | default('')
          }}

    - name: "Falhar se package nao definido para deploy"
      ansible.builtin.fail:
        msg: "Package nao definido para machine={{ machine_name }} em machine/{{ machine_name }}.yml"
      when:
        - deploy_action == "deploy"
        - (selected_package is undefined or selected_package == '')

    - name: "Falhar se rollback nao definido"
      ansible.builtin.fail:
        msg: "Rollback nao definido para machine={{ machine_name }} em machine/{{ machine_name }}.yml"
      when:
        - deploy_action == "rollback"
        - (selected_package is undefined or selected_package == '')

    - name: "Carregar YAML do package selecionado"
      ansible.builtin.include_vars:
        file: "{{ repo_root }}/package/{{ selected_package }}.yml"
        name: package_cfg

    - name: "Definir script e env_vars"
      ansible.builtin.set_fact:
        package_script: "{{ package_cfg.script }}"
        package_components: "{{ package_cfg.components | default([]) }}"
        machine_env_vars: "{{ machine_cfg.env_vars | default({}) }}"

    # ----------------------------------------------------------
    # Registrar host dinâmico
    # status_data.host vem do predeploy (host real)
    # ----------------------------------------------------------
    - name: "Registrar host alvo dinamico para deploy/rollback"
      ansible.builtin.add_host:
        name: dynamic_deploy_target
        ansible_host: "{{ status_data.host }}"
        ansible_user: "{{ target_user }}"
        ansible_ssh_private_key_file: "{{ target_key }}"
        ansible_ssh_common_args: "{{ effective_ssh_args }}"
        groups: dynamic_deploy

        # Metadados disponíveis no PLAY 2
        machine_name: "{{ machine_name }}"
        deploy_action: "{{ deploy_action }}"
        package_name: "{{ selected_package }}"
        package_script: "{{ package_script }}"
        package_components: "{{ package_components }}"
        env_vars: "{{ machine_env_vars }}"

# =====================================================================
# PLAY 2 - TARGET (dynamic_deploy)
# Copia scripts e executa init.sh do package (deploy ou rollback)
# =====================================================================
- name: "[TARGET] Executar deploy/rollback na máquina"
  hosts: dynamic_deploy
  become: true
  gather_facts: false

  vars:
    repo_root: "{{ playbook_dir | dirname }}"

    # Puxar action do host dinâmico
    deploy_action: "{{ hostvars[inventory_hostname].deploy_action | default('deploy') }}"

    # Alinhado com o predeploy
    remote_base_dir: "/opt/SoftwareExpress/sitef"
    remote_pkg_dir: "{{ remote_base_dir }}/package/linux"
    remote_scripts_root: "{{ remote_base_dir }}/scripts"
    remote_scripts_dir: "{{ remote_scripts_root }}/{{ package_script }}"

  tasks:
    - name: "Criar diretórios base no target (se ainda não existem)"
      ansible.builtin.file:
        path: "{{ item }}"
        state: directory
        mode: "0755"
        owner: root
        group: root
      loop:
        - "{{ remote_base_dir }}"
        - "{{ remote_pkg_dir }}"
        - "{{ remote_scripts_root }}"
        - "{{ remote_scripts_dir }}"

    - name: "Copiar scripts do package para o target"
      ansible.builtin.copy:
        src: "{{ repo_root }}/scripts/{{ package_script }}/"
        dest: "{{ remote_scripts_dir }}/"
        mode: "0755"

    - name: "Verificar se existe init.sh"
      ansible.builtin.stat:
        path: "{{ remote_scripts_dir }}/init.sh"
      register: st_init

    - name: "Falhar se init.sh nao existir"
      ansible.builtin.fail:
        msg: "init.sh nao encontrado em {{ remote_scripts_dir }}"
      when: not st_init.stat.exists

    # Opcional: evidência rápida do RPM esperado no host
    - name: "Validar que ao menos 1 componente existe no diretório de package"
      ansible.builtin.stat:
        path: "{{ remote_pkg_dir }}/{{ (package_components[0] | default('') | basename) }}"
      register: st_any_rpm
      when: (package_components | length) > 0

    - name: "Falhar se componente esperado não existir"
      ansible.builtin.fail:
        msg: "Componente esperado nao encontrado em {{ remote_pkg_dir }} para o package {{ package_name }}"
      when:
        - (package_components | length) > 0
        - not st_any_rpm.stat.exists

    - name: "Executar init.sh do package ({{ deploy_action }})"
      ansible.builtin.shell: |
        cd "{{ remote_base_dir }}"
        bash "scripts/{{ package_script }}/init.sh"
      environment: "{{ env_vars | default({}) }}"
      register: deploy_result
      ignore_errors: true

    - name: "Exibir stdout do init.sh"
      ansible.builtin.debug:
        var: deploy_result.stdout_lines

    - name: "Falhar se init.sh retornou erro"
      ansible.builtin.fail:
        msg: |
          Falha ao executar init.sh do package {{ package_name }} em {{ inventory_hostname }}.
          STDERR:
          {{ deploy_result.stderr }}
      when: deploy_result.rc != 0
