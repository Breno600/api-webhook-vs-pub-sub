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
