### deploy_from_status.yml
---
- name: "Deploy/Rollback por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # =====================================================================
    # INPUTS (preferência: vars > env > default)  **SEM AUTO-REFERÊNCIA**
    # =====================================================================

    # deployment_ref: preferir var deployment_ref, senão env GIT_TAG, senão default
    deployment_ref_input: >-
      {{
        (deployment_ref | default('', true) | string | trim)
        if (deployment_ref is defined and ((deployment_ref | string | trim) | length) > 0)
        else (lookup('ansible.builtin.env','GIT_TAG') | default('DEV000000001', true) | string | trim)
      }}

    # machine_name: preferir var machine_name, senão env MACHINE, senão env MACHINES
    machine_name_input: >-
      {{
        (machine_name | default('', true) | string | trim)
        if (machine_name is defined and ((machine_name | string | trim) | length) > 0)
        else (
          (lookup('ansible.builtin.env','MACHINE') | default('', true) | string | trim)
          if (((lookup('ansible.builtin.env','MACHINE') | default('', true) | string | trim) | length) > 0)
          else (lookup('ansible.builtin.env','MACHINES') | default('', true) | string | trim)
        )
      }}

    # deploy_action: preferir var deploy_action, senão env ACTION, senão env STRATEGY, default deploy
    deploy_action_input: >-
      {{
        (deploy_action | default('', true) | string | trim)
        if (deploy_action is defined and ((deploy_action | string | trim) | length) > 0)
        else (
          (lookup('ansible.builtin.env','ACTION') | default('', true) | string | trim)
          if (((lookup('ansible.builtin.env','ACTION') | default('', true) | string | trim) | length) > 0)
          else (lookup('ansible.builtin.env','STRATEGY') | default('deploy', true) | string | trim)
        )
      }}

    # File Store (preferência: vars > env > default)
    filestore_env_input: >-
      {{
        (filestore_env | default('', true) | string | trim)
        if (filestore_env is defined and ((filestore_env | string | trim) | length) > 0)
        else (lookup('ansible.builtin.env','FILESTORE_ENV') | default('dev', true) | string | trim)
      }}

    filestore_base_dir_input: >-
      {{
        (filestore_base_dir | default('', true) | string | trim)
        if (filestore_base_dir is defined and ((filestore_base_dir | string | trim) | length) > 0)
        else (lookup('ansible.builtin.env','FILESTORE_BASEDIR') | default('', true) | string | trim)
      }}

    # Paths do repositório (dependem do playbook_dir)
    repo_root_resolved: "{{ playbook_dir }}/.."

    # Nexus default (evita recursão)
    nexus_base_url_default: "https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"

  tasks:
    # -------------------------------------------------------------------
    # Resolver inputs -> resolved (sem recursão)
    # -------------------------------------------------------------------
    - name: "Resolver inputs principais"
      ansible.builtin.set_fact:
        deployment_ref_resolved: "{{ deployment_ref_input }}"
        machine_name_resolved: "{{ machine_name_input }}"
        deploy_action_resolved: >-
          {{
            (deploy_action_input | string | trim | lower)
            if ((deploy_action_input | string | trim) | length) > 0
            else 'deploy'
          }}
        filestore_env_resolved: "{{ filestore_env_input | default('dev', true) | string | trim }}"

    - name: "Resolver filestore_base_dir (deploy/rollback)"
      ansible.builtin.set_fact:
        filestore_base_dir_resolved: >-
          {{
            (filestore_base_dir_input | string | trim)
            if (((filestore_base_dir_input | string | trim) | length) > 0)
            else ((filestore_env_resolved | default('dev', true) | string | trim) ~ '/' ~ deployment_ref_resolved)
          }}

    - name: "Resolver status_dir"
      ansible.builtin.set_fact:
        status_dir_resolved: "{{ repo_root_resolved }}/status/{{ deployment_ref_resolved }}"

    # -------------------------------------------------------------------
    # Resolver Nexus (base_url, user, password) sem recursão
    # -------------------------------------------------------------------
    - name: "Resolver Nexus (base_url, user, password)"
      ansible.builtin.set_fact:
        nexus_base_url_resolved: >-
          {{
            (nexus_base_url | default('', true) | string | trim)
            if (nexus_base_url is defined and ((nexus_base_url | string | trim) | length) > 0)
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
          - "deployment_ref_resolved      = {{ deployment_ref_resolved }}"
          - "machine_name_resolved       = {{ machine_name_resolved }}"
          - "deploy_action_resolved      = {{ deploy_action_resolved }}"
          - "repo_root_resolved          = {{ repo_root_resolved }}"
          - "status_dir_resolved         = {{ status_dir_resolved }}"
          - "filestore_env_resolved      = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved = {{ filestore_base_dir_resolved }}"
          - "nexus_base_url_resolved     = {{ nexus_base_url_resolved }}"
          - "nexus_user_resolved         = {{ '***' if (nexus_user_resolved | length) > 0 else '(vazio)' }}"

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
          - ((machine_name_resolved | string | trim) | length) > 0
        fail_msg: "machine_name não informado (vars ou env MACHINE/MACHINES)"

    # -------------------------------------------------------------------
    # Branch de DEPLOY
    # -------------------------------------------------------------------
    - name: "Incluir deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      when: deploy_action_resolved == 'deploy'
      vars:
        deployment_ref: "{{ deployment_ref_resolved }}"
        machine_name: "{{ machine_name_resolved }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"
        hf_stage_task: "deploy"

    # -------------------------------------------------------------------
    # Branch de ROLLBACK
    # -------------------------------------------------------------------
    - name: "Incluir rollback por máquina"
      ansible.builtin.include_tasks: rollback_per_machine.yml
      when: deploy_action_resolved == 'rollback'
      vars:
        deployment_ref: "{{ deployment_ref_resolved }}"
        machine_name: "{{ machine_name_resolved }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"
        hf_stage_task: "rollback"

### rollback_from_status.yml

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


# rollback_per_machine.yml
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
- name: "Executar init_rollback.sh no host (com log + stdout)"
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ rollback_scripts_dir }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_rollback.sh 2>&1 | tee -a "{{ rollback_scripts_dir }}/init_rollback.log"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  become: true
  delegate_to: "{{ current_machine }}"
  register: init_rollback_result
  changed_when: true

- name: "Mostrar últimas linhas do log do rollback (sempre)"
  ansible.builtin.shell: |
    echo "===== tail -n 300 {{ rollback_scripts_dir }}/init_rollback.log ====="
    tail -n 300 "{{ rollback_scripts_dir }}/init_rollback.log" 2>/dev/null || echo "log não encontrado"

  become: true
  delegate_to: "{{ current_machine }}"
  register: rollback_tail
  changed_when: false

- name: "DEBUG rollback tail"
  ansible.builtin.debug:
    var: rollback_tail.stdout_lines

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
# 25) Upload SEMPRE + Falhar só DEPOIS (rollback)
# -----------------------------------------------------------------------------
- name: "Montar conteúdo do log do ROLLBACK"
  ansible.builtin.set_fact:
    rollback_log_content: |
      ---- pipeline rollback ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=rollback
      rc={{ init_rollback_result.rc | default(1) }}

      ---- stdout ----
      {{ init_rollback_result.stdout | default('') }}

      ---- stderr ----
      {{ init_rollback_result.stderr | default('') }}
      ---- pipeline rollback ----

- name: "Upload JSON + LOG do ROLLBACK para Harness File Store (via Ansible) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ machine_status_file }}"
    log_content: "{{ rollback_log_content }}"
    status_tag_value: "{{ (deployment_ref | lower) ~ ':rollback:' ~ ('ok' if init_rollback_result.rc == 0 else 'error') }}"
    extra_tags: []

- name: "Falhar se init_rollback.sh retornou erro (DEPOIS do upload)"
  ansible.builtin.fail:
    msg: "init_rollback.sh falhou na máquina {{ current_machine }} com rc={{ init_rollback_result.rc }}"
  when: init_rollback_result.rc != 0
