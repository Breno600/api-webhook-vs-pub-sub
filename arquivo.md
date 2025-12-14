Me ajuda pq no log da pipeline nao esta mostrando o log depois que executa os inits, é como se executace e nao mostrasse os logs caso de problema ou sucesso, lembrando que esses ansibles estao funcionando 100%, ent tomar bastante cuidado para nao quebrar nada 

## predeploy_from_execution.yml
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

## predeploy_per_machine.yml
---
# =====================================================================================
# PREDEPLOY POR MÁQUINA
# Chamado por predeploy_from_execution.yml
#
# Responsabilidades:
# - Carregar machine + package
# - Mesclar env_vars (machine + package)
# - Preparar diretórios:
#     /opt/SoftwareExpress/sitef-pipeline/deploy
#     /opt/SoftwareExpress/sitef-pipeline/deploy/scripts
#     /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package
# - Copiar package.yml para /deploy/scripts/package/
# - Baixar components do Nexus para /deploy/<path do componente>
#   (ex: /deploy/package/linux/sitef-core-0.0.2-0.rpm)
# - Copiar scripts do pacote para /deploy/scripts
# - Executar init_parallel.sh na máquina alvo com as env_vars
# - Gravar status.json local com status + log_path
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Resolver nome da máquina atual
# -----------------------------------------------------------------------------
- name: "Resolver nome da máquina atual"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        (machine_name | default(item) | default('')) | string | trim
      }}

# -----------------------------------------------------------------------------
# 2) Definir paths base do repositório
# -----------------------------------------------------------------------------
- name: "Definir paths base do repositório"
  ansible.builtin.set_fact:
    repo_root_safe: >-
      {{
        repo_root
          | default(repo_root_resolved | default(playbook_dir ~ '/..'))
      }}

# -----------------------------------------------------------------------------
# 3) Definir diretórios principais do repositório
# -----------------------------------------------------------------------------
- name: "Definir execution_dir do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"

- name: "Definir machines_dir, packages_dir e scripts_dir do repositório"
  ansible.builtin.set_fact:
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"
    scripts_root_dir: "{{ repo_root_safe }}/scripts"

# -----------------------------------------------------------------------------
# 4) Candidatos de arquivo de máquina
# -----------------------------------------------------------------------------
- name: "Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

# -----------------------------------------------------------------------------
# 5) Verificar quais candidatos existem
# -----------------------------------------------------------------------------
- name: "Verificar candidatos de arquivo da máquina"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

# -----------------------------------------------------------------------------
# 6) Selecionar o primeiro arquivo existente
# -----------------------------------------------------------------------------
- name: "Selecionar machine_file existente"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

# -----------------------------------------------------------------------------
# 7) Falhar se nenhum candidato existir
# -----------------------------------------------------------------------------
- name: "Falhar se arquivo da máquina não existir em nenhum caminho esperado"
  ansible.builtin.fail:
    msg: |
      Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 8) Carregar config da máquina
# -----------------------------------------------------------------------------
- name: "Carregar config da máquina {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

# -----------------------------------------------------------------------------
# 9) Validar package definido
# -----------------------------------------------------------------------------
- name: "Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.package is defined
      - (machine_cfg.package | string | trim) | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

# -----------------------------------------------------------------------------
# 10) Carregar config do pacote
# -----------------------------------------------------------------------------
- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 11) Validações mínimas do pacote
# -----------------------------------------------------------------------------
- name: "Validar components do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      - package_cfg.components is defined
      - package_cfg.components | length > 0
    fail_msg: "components ausente/vazio no pacote {{ machine_cfg.package }}"

# -----------------------------------------------------------------------------
# 12) Calcular env_vars mescladas (machine + package + extras)
# -----------------------------------------------------------------------------
- name: "Calcular env_vars mescladas"
  ansible.builtin.set_fact:
    merged_env_vars: >-
      {{
        (machine_cfg.env_vars | default({}))
        | combine(package_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': current_machine,
            'SITEF_HOST': machine_cfg.host | default(machine_cfg.ip | default('')),
            'DEPLOYMENT_REF': deployment_ref | default(''),
            'PACKAGE_NAME': machine_cfg.package,
            'ROLLBACK_PACKAGE': machine_cfg.rollback | default('')
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# 13) Usuário/SSH defaults
# -----------------------------------------------------------------------------
- name: "Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.target_user | default('root') }}"

- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

# -----------------------------------------------------------------------------
# 14) Registrar host dinâmico (forçando conexão SSH)
# -----------------------------------------------------------------------------
- name: "Registrar host dinâmico para {{ current_machine }}"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip | default(machine_cfg.host | default(''))) }}"
    ansible_user: "{{ target_user }}"
    ansible_ssh_common_args: "{{ ssh_common_args }}"
    ansible_connection: ssh
  changed_when: true

# -----------------------------------------------------------------------------
# 14.1) Debug: identificar hostname/IP da máquina alvo
# -----------------------------------------------------------------------------
- name: "Mostrar hostname/IP da máquina alvo (debug)"
  ansible.builtin.shell: "hostname -I && hostname"
  delegate_to: "{{ current_machine }}"
  register: target_ident
  changed_when: false

- name: "Debug target_ident"
  ansible.builtin.debug:
    msg:
      - "current_machine = {{ current_machine }}"
      - "machine_cfg.host = {{ machine_cfg.host | default('') }}"
      - "machine_cfg.ip   = {{ machine_cfg.ip | default('') }}"
      - "hostname/ip alvo:"
      - "{{ target_ident.stdout_lines }}"

# -----------------------------------------------------------------------------
# 15) Definir caminhos de status e deploy (BASE = /opt/.../deploy)
# -----------------------------------------------------------------------------
- name: "Definir caminhos de status e deploy"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    machine_status_file: "{{ status_dir }}/{{ current_machine }}/status.json"

    deploy_base_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/components"
    deploy_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    deploy_scripts_package_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"

# -----------------------------------------------------------------------------
# 16) Calcular caminhos de status/log "lógicos"
# -----------------------------------------------------------------------------
- name: "Calcular caminhos de status/log"
  ansible.builtin.set_fact:
    filestore_log_path: >-
      {{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.log
    filestore_status_path: >-
      {{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.json

# -----------------------------------------------------------------------------
# 17) Criar diretório de status local e registrar status 'pre-deploy:queued'
# -----------------------------------------------------------------------------
- name: "Criar diretório de status da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Escrever status inicial (pre-deploy:queued)"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "pre-deploy:queued",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 18) Garantir base do pipeline no host remoto
# -----------------------------------------------------------------------------
- name: "Garantir base do pipeline no host"
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"
  loop:
    - "{{ deploy_base_dir }}"
    - "{{ deploy_scripts_dir }}"
    - "{{ deploy_scripts_package_dir }}"

# -----------------------------------------------------------------------------
# 19) Limpar pasta de scripts do pacote
# -----------------------------------------------------------------------------
- name: "Limpar pasta de scripts do pacote"
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: absent
  become: true
  delegate_to: "{{ current_machine }}"

- name: "Recriar pasta de scripts do pacote"
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 20) Copiar package.yml para /deploy/scripts/package/
# -----------------------------------------------------------------------------
- name: "Copiar package.yml para o host"
  ansible.builtin.copy:
    src: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    dest: "{{ deploy_scripts_package_dir }}/{{ machine_cfg.package }}.yml"
    mode: "0644"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 21) Garantir diretórios dos componentes + baixar do Nexus
# -----------------------------------------------------------------------------
- name: "Garantir diretórios dos componentes no host"
  ansible.builtin.file:
    path: "{{ deploy_base_dir }}/{{ item | dirname }}"
    state: directory
    mode: "0755"
  loop: "{{ package_cfg.components }}"
  become: true
  delegate_to: "{{ current_machine }}"

- name: "Baixar componentes do Nexus"
  ansible.builtin.get_url:
    url: "{{ nexus_base_url.rstrip('/') }}/{{ item }}"
    dest: "{{ deploy_base_dir }}/{{ item }}"
    mode: "0644"
    url_username: "{{ nexus_user | default(omit) }}"
    url_password: "{{ nexus_password | default(omit) }}"
  loop: "{{ package_cfg.components }}"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 22) Copiar scripts do pacote para /deploy/scripts
# -----------------------------------------------------------------------------
- name: "Copiar scripts do pacote para o host"
  ansible.builtin.copy:
    src: "{{ scripts_root_dir }}/{{ package_cfg.script }}/"
    dest: "{{ deploy_scripts_dir }}/"
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"

# Debug só para conferência
- name: "Listar conteúdo de /deploy/scripts (debug)"
  ansible.builtin.shell: |
    ls -Ral "{{ deploy_scripts_dir }}" || echo "diretório vazio"
  become: true
  delegate_to: "{{ current_machine }}"
  register: scripts_ls
  changed_when: false

- name: "Mostrar conteúdo de scripts (debug)"
  ansible.builtin.debug:
    var: scripts_ls.stdout_lines

# -----------------------------------------------------------------------------
# 23) Executar init_parallel.sh na máquina alvo
# -----------------------------------------------------------------------------
- name: "Executar init_parallel.sh no host"
  ansible.builtin.shell: |
    cd "{{ deploy_scripts_dir }}"
    ./init_parallel.sh
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  become: true
  delegate_to: "{{ current_machine }}"
  register: init_parallel_result
  changed_when: true

# -----------------------------------------------------------------------------
# 24) Atualizar status para pre-deploy:ok ou pre-deploy:error
# -----------------------------------------------------------------------------
- name: "Atualizar status após execução do init_parallel.sh"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "{{ 'pre-deploy:ok' if init_parallel_result.rc == 0 else 'pre-deploy:error' }}",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 25) Falhar play se init_parallel.sh der erro
# -----------------------------------------------------------------------------
- name: "Falhar se init_parallel.sh retornou erro"
  ansible.builtin.fail:
    msg: "init_parallel.sh falhou na máquina {{ current_machine }} com rc={{ init_parallel_result.rc }}"
  when: init_parallel_result.rc != 0

## deploy_from_status.yml
---
# PIPELINE 2 - DEPLOY / ROLLBACK
# - Recebe machine_name (ou lista via loop externo no script do Harness)
# - Usa TAG (deployment_ref) como referência da execução
# - Para deploy:
#     - Reusa área /opt/SoftwareExpress/sitef-pipeline/deploy/scripts preparada no predeploy
#     - Executa init_deploy.sh
# - Para rollback:
#     - Prepara /opt/SoftwareExpress/sitef-pipeline/rollback
#     - Baixa componentes do Nexus
#     - Executa init_rollback.sh
# - Atualiza status em JSON por máquina
# - Faz upload de JSON + LOG para Harness File Store

- name: "Deploy/Rollback por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # TAG da execução (vem do GIT_TAG no Harness)
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    # Nome lógico da máquina (ex: sitef-01)
    machine_name: "{{ machine_name | default('') }}"

    # Ação solicitada: deploy ou rollback (vem do STRATEGY/ACTION no Harness)
    deploy_action: "{{ deploy_action | default('deploy') }}"

    # Paths do repositório
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ repo_root_resolved }}/status/{{ deployment_ref }}"

    # URL default do Nexus (evita recursão)
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

    # -------------------------------------------------------------------
    # Resolver Nexus (base_url, user, password) sem recursão
    # -------------------------------------------------------------------
    - name: "Resolver Nexus (base_url, user, password)"
      ansible.builtin.set_fact:
        nexus_base_url_resolved: >-
          {{
            (nexus_base_url | string | trim)
              if (nexus_base_url is defined and (nexus_base_url | string | trim) | length > 0)
              else nexus_base_url_default
          }}
        nexus_user_resolved: >-
          {{
            (nexus_user | string | trim)
              if (nexus_user is defined)
              else ''
          }}
        nexus_password_resolved: >-
          {{
            (nexus_password | string | trim)
              if (nexus_password is defined)
              else ''
          }}

    # -------------------------------------------------------------------
    # Debug das principais variáveis de entrada / resolvidas
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
          - "nexus_base_url_resolved      = {{ nexus_base_url_resolved }}"
          - "nexus_user_resolved          = {{ '***' if (nexus_user_resolved | length) > 0 else '(vazio)' }}"
          - "filestore_env_resolved       = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved  = {{ filestore_base_dir_resolved }}"

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
          - machine_name is defined
          - (machine_name | string | trim) | length > 0
        fail_msg: "machine_name não informado"

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

    # -------------------------------------------------------------------
    # Branch de ROLLBACK
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


## deploy_per_machine.yml

---
# Tarefas por máquina - DEPLOY

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machines/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/deploy-{{ machine_name }}.json"

    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"

- name: "Carregar config da máquina"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/packages/{{ machine_cfg.package }}.yml"
    name: package_cfg

- name: "Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default('ec2-user') }}"

- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{
        machine_cfg.ssh_common_args
          | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null')
      }}

- name: "Registrar host dinâmico"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"

# Monta env final = env do pacote + env da máquina
# Exemplo:
#   package.yml -> env_vars: { PACKAGE_VERSION: 'sitef-0.0.20' }
#   machine.yml -> env_vars: { SITEF_ENV: 'prod', OUTRA_VAR: 'valor' }
# Resultado vai ser passado pro init_deploy.sh
- name: "Montar env final"
  ansible.builtin.set_fact:
    effective_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
      }}

- name: "Criar status inicial (queued)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }

- name: "Verificar init_deploy.sh"
  become: true
  ansible.builtin.stat:
    path: "{{ host_deploy_scripts }}/init_deploy.sh"
  delegate_to: "{{ machine_name }}"
  register: deploy_script_stat

- name: "Falhar se init_deploy.sh não existir"
  ansible.builtin.fail:
    msg: "init_deploy.sh não encontrado em {{ host_deploy_scripts }}. Rode o predeploy antes."
  when: not deploy_script_stat.stat.exists

- name: "Executar init_deploy.sh"
  become: true
  ansible.builtin.shell: |
    set -e
    cd "{{ host_deploy_scripts }}"
    /bin/bash -x ./init_deploy.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ effective_env }}"
  register: deploy_result
  ignore_errors: true

- name: "Ler deploy.txt do host"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_deploy_scripts }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: deploy_log
  changed_when: false

- name: "Atualizar status para success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ deploy_result.rc | default(0) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "script_log": {{ deploy_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: deploy_result.rc == 0

- name: "Atualizar status para failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ deploy_result.rc | default(1) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "script_log": {{ deploy_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: deploy_result.rc != 0

- name: "Upload status JSON para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "{{ status_file }}" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.json"
  changed_when: false
  failed_when: false

- name: "Upload log do deploy para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "-" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log" \
      <<'EOF'
    ---- pipeline deploy ----
    {{ deploy_result.stdout | default('') }}
    {{ deploy_result.stderr | default('') }}
    ---- deploy.txt ----
    {{ deploy_log.stdout | default('') }}
    ---- pipeline deploy ----
    EOF
  args:
    executable: /bin/bash
  changed_when: false
  failed_when: false

- name: "Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ machine_name }} (host {{ machine_cfg.host }})"
  when: deploy_result.rc != 0


## rollback_from_status.yml

# ansible/rollback_from_status.yml
# PIPELINE 3 - ROLLBACK

- name: "Rollback por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    machine_name: "{{ machine_name | default('') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

    nexus_base_url: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user: "{{ nexus_user | default(omit) }}"
    nexus_password: "{{ nexus_password | default(omit) }}"

    # evita loop recursivo: pegamos do ambiente e caímos pra "dev"
    filestore_env: "{{ lookup('ansible.builtin.env', 'FILESTORE_ENV') | default('dev', true) }}"
    filestore_base_dir: "{{ filestore_base_dir | default(filestore_env ~ '/' ~ deployment_ref) }}"

  tasks:
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - machine_name | length > 0
        fail_msg: "machine_name não informado"

    - name: "Garantir diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir }}"
        state: directory
        mode: "0755"

    - name: "Mostrar variáveis de entrada e resolvidas (rollback)"
      ansible.builtin.debug:
        msg:
          - "machine_name                = {{ machine_name }}"
          - "deployment_ref             = {{ deployment_ref }}"
          - "repo_root                  = {{ repo_root }}"
          - "status_dir                 = {{ status_dir }}"
          - "nexus_base_url             = {{ nexus_base_url }}"
          - "filestore_env              = {{ filestore_env }}"
          - "filestore_base_dir         = {{ filestore_base_dir }}"

    - name: "Incluir rollback por máquina"
      ansible.builtin.include_tasks: rollback_per_machine.yml
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name }}"
        repo_root: "{{ repo_root }}"
        status_dir: "{{ status_dir }}"
        nexus_base_url: "{{ nexus_base_url }}"
        nexus_user: "{{ nexus_user | default('') }}"
        nexus_password: "{{ nexus_password | default('') }}"
        filestore_env: "{{ filestore_env }}"
        filestore_base_dir: "{{ filestore_base_dir }}"


## rollback_per_machine.yml
---
# =====================================================================================
# ROLLBACK POR MÁQUINA
# Chamado pelo play principal (deploy_from_execution.yml ou similar)
#
# Responsabilidades:
# - Resolver machine (sitef-01, etc.)
# - Localizar arquivo de máquina (machines/sitef-01.yml, etc.)
# - Carregar machine_cfg (campos host, package, rollback, env_vars...)
# - Carregar package_cfg do rollback (packages/<rollback>.yml)
# - Mesclar env_vars
# - Preparar diretórios em /opt/SoftwareExpress/sitef-pipeline/rollback
# - Baixar componentes de rollback do Nexus (se houver)
# - Copiar scripts de rollback para o host
# - Executar init_rollback.sh no host
# - Atualizar status.json com rollback:ok ou rollback:error
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Resolver nome da máquina atual
# -----------------------------------------------------------------------------
- name: "Resolver nome da máquina atual (rollback)"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        (machine_name | default(item) | default('')) | string | trim
      }}

# -----------------------------------------------------------------------------
# 2) Definir paths base do repositório
# -----------------------------------------------------------------------------
- name: "Definir paths base do repositório (rollback)"
  ansible.builtin.set_fact:
    repo_root_safe: >-
      {{
        repo_root
          | default(repo_root_resolved | default(playbook_dir ~ '/..'))
      }}

# -----------------------------------------------------------------------------
# 3) Definir diretórios principais do repositório
# -----------------------------------------------------------------------------
- name: "Definir execution_dir, machines_dir, packages_dir e scripts_dir (rollback)"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"
    scripts_root_dir: "{{ repo_root_safe }}/scripts"

# -----------------------------------------------------------------------------
# 4) Candidatos de arquivo de máquina
# -----------------------------------------------------------------------------
- name: "Definir candidatos de arquivo da máquina (rollback)"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

# -----------------------------------------------------------------------------
# 5) Verificar quais candidatos existem
# -----------------------------------------------------------------------------
- name: "Verificar candidatos de arquivo da máquina (rollback)"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

# -----------------------------------------------------------------------------
# 6) Selecionar o primeiro arquivo existente
# -----------------------------------------------------------------------------
- name: "Selecionar machine_file existente (rollback)"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

# -----------------------------------------------------------------------------
# 7) Falhar se nenhum candidato existir
# -----------------------------------------------------------------------------
- name: "Falhar se arquivo da máquina não existir em nenhum caminho esperado (rollback)"
  ansible.builtin.fail:
    msg: |
      [rollback] Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 8) Carregar config da máquina
# -----------------------------------------------------------------------------
- name: "Carregar config da máquina {{ current_machine }} (rollback)"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

# -----------------------------------------------------------------------------
# 9) Validar rollback definido
# -----------------------------------------------------------------------------
- name: "Validar rollback definido na máquina"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.rollback is defined
      - (machine_cfg.rollback | string | trim) | length > 0
    fail_msg: "machine_cfg.rollback não definido em {{ machine_file }}"

# -----------------------------------------------------------------------------
# 10) Carregar config do pacote de rollback
# -----------------------------------------------------------------------------
- name: "Carregar config do pacote de rollback {{ machine_cfg.rollback }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.rollback }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 11) Validações mínimas do pacote de rollback
# -----------------------------------------------------------------------------
- name: "Validar components e script do pacote de rollback"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      - package_cfg.script is defined
      - (package_cfg.script | string | trim) | length > 0
      - package_cfg.components is defined
      - package_cfg.components | length > 0
    fail_msg: "Pacote de rollback {{ machine_cfg.rollback }} inválido (falta script ou components)"

# -----------------------------------------------------------------------------
# 12) Calcular env_vars mescladas (machine + package rollback + extras)
# -----------------------------------------------------------------------------
- name: "Calcular env_vars mescladas (rollback)"
  ansible.builtin.set_fact:
    merged_env_vars: >-
      {{
        (machine_cfg.env_vars | default({}))
        | combine(package_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': current_machine,
            'SITEF_HOST': machine_cfg.host | default(machine_cfg.ip | default('')),
            'DEPLOYMENT_REF': deployment_ref | default(''),
            'PACKAGE_NAME': machine_cfg.package | default(''),
            'ROLLBACK_PACKAGE': machine_cfg.rollback | default(''),
            'ROLLBACK': '1'
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# 13) Usuário/SSH defaults
# -----------------------------------------------------------------------------
- name: "Definir usuário alvo padrão (rollback)"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.target_user | default('root') }}"

- name: "Definir ssh_common_args padrão (rollback)"
  ansible.builtin.set_fact:
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

- name: "Aplicar ProxyJump via bastion (quando necessário) (rollback)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

# -----------------------------------------------------------------------------
# 14) Registrar host dinâmico (forçando conexão SSH)
# -----------------------------------------------------------------------------
- name: "Registrar host dinâmico para {{ current_machine }} (rollback)"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip | default(machine_cfg.host | default(''))) }}"
    ansible_user: "{{ target_user }}"
    ansible_ssh_common_args: "{{ ssh_common_args }}"
    ansible_connection: ssh
  changed_when: true

# -----------------------------------------------------------------------------
# 14.1) Debug: identificar hostname/IP da máquina alvo
# -----------------------------------------------------------------------------
- name: "Mostrar hostname/IP da máquina alvo (debug rollback)"
  ansible.builtin.shell: "hostname -I && hostname"
  delegate_to: "{{ current_machine }}"
  register: target_ident
  changed_when: false

- name: "Debug target_ident (rollback)"
  ansible.builtin.debug:
    msg:
      - "current_machine = {{ current_machine }}"
      - "machine_cfg.host = {{ machine_cfg.host | default('') }}"
      - "machine_cfg.ip   = {{ machine_cfg.ip | default('') }}"
      - "hostname/ip alvo:"
      - "{{ target_ident.stdout_lines }}"

# -----------------------------------------------------------------------------
# 15) Definir caminhos de status e rollback no host
# -----------------------------------------------------------------------------
- name: "Definir caminhos de status e rollback"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ (status_dir | default(status_dir_resolved)) }}/{{ current_machine }}"
    machine_status_file: "{{ (status_dir | default(status_dir_resolved)) }}/{{ current_machine }}/status.json"

    rollback_base_dir: "/opt/SoftwareExpress/sitef-pipeline/rollback/components"
    rollback_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts"
    rollback_scripts_package_dir: "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package"

# -----------------------------------------------------------------------------
# 16) Calcular caminhos de status/log "lógicos" (rollback)
# -----------------------------------------------------------------------------
- name: "Calcular caminhos de status/log (rollback)"
  ansible.builtin.set_fact:
    filestore_log_path: >-
      {{ (filestore_base_dir | default(filestore_base_dir_resolved)) }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ (filestore_env | default(filestore_env_resolved)) }}-rollback.log
    filestore_status_path: >-
      {{ (filestore_base_dir | default(filestore_base_dir_resolved)) }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ (filestore_env | default(filestore_env_resolved)) }}-rollback.json

# -----------------------------------------------------------------------------
# 17) Criar diretório de status local e registrar status 'rollback:queued'
# -----------------------------------------------------------------------------
- name: "Criar diretório de status da máquina (rollback)"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Escrever status inicial (rollback:queued)"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package | default('') }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "rollback:queued",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 18) Garantir base de rollback no host remoto
# -----------------------------------------------------------------------------
- name: "Garantir base de rollback no host"
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"
  loop:
    - "{{ rollback_base_dir }}"
    - "{{ rollback_scripts_dir }}"
    - "{{ rollback_scripts_package_dir }}"

# -----------------------------------------------------------------------------
# 19) Limpar pasta de scripts do pacote de rollback
# -----------------------------------------------------------------------------
- name: "Limpar pasta de scripts do pacote de rollback"
  ansible.builtin.file:
    path: "{{ rollback_scripts_package_dir }}"
    state: absent
  become: true
  delegate_to: "{{ current_machine }}"

- name: "Recriar pasta de scripts do pacote de rollback"
  ansible.builtin.file:
    path: "{{ rollback_scripts_package_dir }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 20) Copiar package.yml de rollback para /rollback/scripts/package/
# -----------------------------------------------------------------------------
- name: "Copiar package.yml de rollback para o host"
  ansible.builtin.copy:
    src: "{{ packages_dir }}/{{ machine_cfg.rollback }}.yml"
    dest: "{{ rollback_scripts_package_dir }}/{{ machine_cfg.rollback }}.yml"
    mode: "0644"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 21) Garantir diretórios dos componentes de rollback + baixar do Nexus
# -----------------------------------------------------------------------------
- name: "Garantir diretórios dos componentes de rollback no host"
  ansible.builtin.file:
    path: "{{ rollback_base_dir }}/{{ item | dirname }}"
    state: directory
    mode: "0755"
  loop: "{{ package_cfg.components }}"
  become: true
  delegate_to: "{{ current_machine }}"

- name: "Baixar componentes de rollback do Nexus"
  ansible.builtin.get_url:
    url: "{{ nexus_base_url.rstrip('/') }}/{{ item }}"
    dest: "{{ rollback_base_dir }}/{{ item }}"
    mode: "0644"
    url_username: "{{ nexus_user | default(omit) }}"
    url_password: "{{ nexus_password | default(omit) }}"
  loop: "{{ package_cfg.components }}"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 22) Copiar scripts de rollback para /rollback/scripts
# -----------------------------------------------------------------------------
- name: "Copiar scripts de rollback para o host"
  ansible.builtin.copy:
    src: "{{ scripts_root_dir }}/{{ package_cfg.script }}/"
    dest: "{{ rollback_scripts_dir }}/"
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"

# Debug só para conferência
- name: "Listar conteúdo de /rollback/scripts (debug)"
  ansible.builtin.shell: |
    ls -Ral "{{ rollback_scripts_dir }}" || echo "diretório vazio"
  become: true
  delegate_to: "{{ current_machine }}"
  register: scripts_ls_rb
  changed_when: false

- name: "Mostrar conteúdo de scripts de rollback (debug)"
  ansible.builtin.debug:
    var: scripts_ls_rb.stdout_lines

# -----------------------------------------------------------------------------
# 23) Executar init_rollback.sh na máquina alvo
# -----------------------------------------------------------------------------
- name: "Executar init_rollback.sh no host"
  ansible.builtin.shell: |
    cd "{{ rollback_scripts_dir }}"
    ./init_rollback.sh
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  become: true
  delegate_to: "{{ current_machine }}"
  register: init_rollback_result
  changed_when: true

# -----------------------------------------------------------------------------
# 24) Atualizar status para rollback:ok ou rollback:error
# -----------------------------------------------------------------------------
- name: "Atualizar status após execução do init_rollback.sh"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package | default('') }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "{{ 'rollback:ok' if init_rollback_result.rc == 0 else 'rollback:error' }}",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 25) Falhar play se init_rollback.sh der erro
# -----------------------------------------------------------------------------
- name: "Falhar se init_rollback.sh retornou erro"
  ansible.builtin.fail:
    msg: "init_rollback.sh falhou na máquina {{ current_machine }} com rc={{ init_rollback_result.rc }}"
  when: init_rollback_result.rc != 0

