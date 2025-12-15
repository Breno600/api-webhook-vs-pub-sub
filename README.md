---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina)
# - Cria (se não existir) pasta raiz -> env -> deployment_ref_folder
# - Faz UPSERT do status.json e do log da máquina
# - Aplica tags (status_tag_value + extra_tags + tags fixas)
#
# SEGREDO (NÃO NO REPO):
#   HARNESS_X_API_KEY (env var)
# =====================================================================================

- name: "Harness | Validar vars mínimas"
  ansible.builtin.assert:
    that:
      - current_machine is defined
      - deployment_ref is defined
      - filestore_env is defined
      - machine_status_file is defined
      - log_content is defined
      - status_tag_value is defined
    fail_msg: "Faltam vars obrigatórias para harness_filestore_upload.yml"

- name: "Harness | Definir defaults + normalizações"
  ansible.builtin.set_fact:
    harness_account_id_resolved: "{{ harness_account_id | default('fgDto6qoTT6ctfZS9eWbEw') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('Fiserv') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('sitef') }}"
    harness_filestore_root_identifier_resolved: "{{ harness_filestore_root_identifier | default('Root') }}"

    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    # pasta ENV sempre em lower
    env_lower: "{{ (filestore_env | string | trim | lower) }}"

    # pasta do deployment: mantém como veio (DEV000000007)
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"

    # lower para nomes/identifiers/tags
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"

- name: "Harness | Resolver token (vars > env)"
  ansible.builtin.set_fact:
    harness_x_api_key: >-
      {{
        (harness_x_api_key | default('') | string | trim)
          if (harness_x_api_key is defined and (harness_x_api_key | string | trim) | length > 0)
          else (lookup('ansible.builtin.env', 'HARNESS_X_API_KEY') | default('', true))
      }}
  no_log: true

- name: "Harness | Falhar se token não está no ambiente"
  ansible.builtin.fail:
    msg: "HARNESS_X_API_KEY não definido no ambiente. Exporte no step do Harness antes de rodar o Ansible."
  when: (harness_x_api_key | length) == 0

- name: "Harness | Montar tags (JSON)"
  ansible.builtin.set_fact:
    harness_tags: >-
      {{
        (
          [
            {"key":"env","value": env_lower},
            {"key":"deployment","value": deployment_ref_lower},
            {"key":"machine","value": (current_machine | lower)},
            {"key":"tag","value": status_tag_value}
          ]
          + (
              (extra_tags | default([]))
              | map('regex_replace', '^(.*)$', '{"key":"tag","value":"\\1"}')
              | map('from_json')
              | list
            )
        )
      }}

# -----------------------------------------------------------------------------
# 1) Garantir folder ENV (filestore_env) embaixo da raiz
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (create se não existir)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_lower }}" \
      -F "tags={{ harness_tags | to_json }}"
  args:
    executable: /bin/bash
  register: hfs_env_create_http
  changed_when: false
  failed_when: false
  no_log: true

# -----------------------------------------------------------------------------
# 2) Garantir folder DEPLOYMENT_REF embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder DEPLOYMENT_REF (create se não existir)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_lower }}" \
      -F "identifier={{ deployment_ref_folder }}" \
      -F "tags={{ harness_tags | to_json }}"
  args:
    executable: /bin/bash
  register: hfs_ref_create_http
  changed_when: false
  failed_when: false
  no_log: true

# -----------------------------------------------------------------------------
# 3) Upload/Upsert STATUS JSON
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do status JSON"
  ansible.builtin.set_fact:
    hfs_status_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.json"
    hfs_status_identifier: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-json"

- name: "Harness | Tentar CREATE status JSON"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ deployment_ref_folder }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@{{ machine_status_file }}"
  args:
    executable: /bin/bash
  register: hfs_status_create_http
  changed_when: false
  failed_when: false
  no_log: true

- name: "Harness | UPDATE status JSON (se create não retornou 2xx)"
  ansible.builtin.shell: |
    set -e
    curl -sS -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ deployment_ref_folder }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@{{ machine_status_file }}"
  args:
    executable: /bin/bash
  when: (hfs_status_create_http.stdout | default('')) is not match('^2..$')
  changed_when: false
  failed_when: false
  no_log: true

# -----------------------------------------------------------------------------
# 4) Upload/Upsert LOG
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do LOG"
  ansible.builtin.set_fact:
    hfs_log_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.log"
    hfs_log_identifier: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-log"

- name: "Harness | Upload do LOG (create/update) com cleanup garantido"
  block:
    - name: "Harness | Criar arquivo temporário do log no controller"
      ansible.builtin.copy:
        dest: "/tmp/{{ hfs_log_name }}"
        mode: "0600"
        content: "{{ log_content }}"

    - name: "Harness | Tentar CREATE log"
      ansible.builtin.shell: |
        set -e
        curl -sS -o /tmp/hfs_log_create.out -w "%{http_code}" \
          --request POST \
          "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ deployment_ref_folder }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F "tags={{ harness_tags | to_json }}" \
          -F "content=@/tmp/{{ hfs_log_name }}"
      args:
        executable: /bin/bash
      register: hfs_log_create_http
      changed_when: false
      failed_when: false
      no_log: true

    - name: "Harness | UPDATE log (se create não retornou 2xx)"
      ansible.builtin.shell: |
        set -e
        curl -sS -o /tmp/hfs_log_update.out -w "%{http_code}" \
          --request PUT \
          "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ deployment_ref_folder }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F "tags={{ harness_tags | to_json }}" \
          -F "content=@/tmp/{{ hfs_log_name }}"
      args:
        executable: /bin/bash
      when: (hfs_log_create_http.stdout | default('')) is not match('^2..$')
      changed_when: false
      failed_when: false
      no_log: true

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/{{ hfs_log_name }}"
        state: absent
      changed_when: false
      failed_when: false


###

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
- name: "Executar init_parallel.sh no host (com log + stdout)"
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ deploy_scripts_dir }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_parallel.sh 2>&1 | tee -a "{{ deploy_scripts_dir }}/init_parallel.log"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  become: true
  delegate_to: "{{ current_machine }}"
  register: init_parallel_result
  changed_when: true

- name: "Mostrar últimas linhas do log do predeploy (sempre)"
  ansible.builtin.shell: |
    echo "===== tail -n 300 {{ deploy_scripts_dir }}/init_parallel.log ====="
    tail -n 300 "{{ deploy_scripts_dir }}/init_parallel.log" 2>/dev/null || echo "log não encontrado"
  become: true
  delegate_to: "{{ current_machine }}"
  register: predeploy_tail
  changed_when: false

- name: "DEBUG predeploy tail"
  ansible.builtin.debug:
    var: predeploy_tail.stdout_lines

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

- name: "Upload File Store (predeploy) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    machine_status_file: "{{ machine_status_file }}"
    log_content: |
      ---- pipeline pre deploy ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=predeploy
      rc={{ init_parallel_result.rc | default(1) }}

      ---- stdout ----
      {{ init_parallel_result.stdout | default('') }}

      ---- stderr ----
      {{ init_parallel_result.stderr | default('') }}
      ---- pipeline pre deploy ----
    status_tag_value: "{{ (deployment_ref | lower) ~ ':pre-deploy:' ~ ('ok' if init_parallel_result.rc == 0 else 'error') }}"
    extra_tags: "{{ [ (deployment_ref | lower) ~ ':deploy:pending' ] if init_parallel_result.rc == 0 else [] }}"

- name: "Falhar se init_parallel.sh retornou erro (DEPOIS do upload)"
  ansible.builtin.fail:
    msg: "init_parallel.sh falhou na máquina {{ current_machine }} com rc={{ init_parallel_result.rc }}"
  when: init_parallel_result.rc != 0
