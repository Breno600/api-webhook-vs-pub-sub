# ansible/deploy_from_status.yml

---
# =====================================================================================
# PIPELINE 2 - DEPLOY POR MÁQUINA (A PARTIR DE STATUS/TAG)
#
# Chamado pelo script bash:
#   ansible-playbook -i localhost, ansible/deploy_from_status.yml \
#     -e "machine_name=sitef-01" \
#     -e "deployment_ref=DEV000000001" \
#     -e "deploy_action=deploy"
#
# Responsabilidades:
# - Resolver paths básicos (repo_root, status_dir, filestore_*)
# - Validar machine_name
# - Incluir deploy_per_machine.yml com as variáveis necessárias
# =====================================================================================

- name: "Deploy por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # Entradas principais
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    machine_name: "{{ machine_name | default('') }}"

    # Paths do repositório (semelhante ao predeploy)
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ (playbook_dir ~ '/..') }}/status/{{ deployment_ref }}"

    # File Store / Harness
    filestore_env: "{{ filestore_env | default('dev') }}"
    filestore_base_dir: >-
      {{
        filestore_base_dir
          | default(
              (filestore_env | string | trim | length > 0)
              | ternary(filestore_env, 'dev')
              ~ '/' ~ deployment_ref
            )
      }}

  tasks:
    # ---------------------------------------
    # Validar machine_name
    # ---------------------------------------
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - machine_name is defined
          - (machine_name | string | trim) | length > 0
        fail_msg: "machine_name não informado"

    # ---------------------------------------
    # Garantir diretório de status
    # ---------------------------------------
    - name: "Garantir diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir_resolved }}"
        state: directory
        mode: "0755"

    # ---------------------------------------
    # Debug opcional
    # ---------------------------------------
    - name: "Mostrar variáveis de entrada e resolvidas (deploy)"
      ansible.builtin.debug:
        msg:
          - "machine_name        = {{ machine_name }}"
          - "deployment_ref      = {{ deployment_ref }}"
          - "repo_root_resolved  = {{ repo_root_resolved }}"
          - "status_dir_resolved = {{ status_dir_resolved }}"
          - "filestore_env       = {{ filestore_env }}"
          - "filestore_base_dir  = {{ filestore_base_dir }}"
          - "deploy_action       = {{ deploy_action | default('deploy') }}"

    # ---------------------------------------
    # Incluir deploy_per_machine.yml
    # ---------------------------------------
    - name: "Incluir tarefas de deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"
        filestore_env: "{{ filestore_env }}"
        filestore_base_dir: "{{ filestore_base_dir }}"
        deploy_action: "{{ deploy_action | default('deploy') }}"


# ansible/deploy_per_machine.yml

---
# =====================================================================================
# Tarefas por máquina - DEPLOY
#
# Responsabilidades:
# - Resolver machine_file com mesma lógica do PREDEPLOY
# - Carregar machine_cfg + package_cfg
# - Mesclar env_vars (machine + package + extras)
# - Executar init_deploy.sh no host remoto
# - Gerar status JSON local
# - Fazer upload do JSON + LOG para o File Store via upload_filestore.sh
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Resolver nome da máquina atual
# -----------------------------------------------------------------------------
- name: "Resolver nome da máquina atual (deploy)"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        (machine_name | default('')) | string | trim
      }}

# -----------------------------------------------------------------------------
# 2) Definir paths base do repositório
# -----------------------------------------------------------------------------
- name: "Definir paths base do repositório (deploy)"
  ansible.builtin.set_fact:
    repo_root_safe: >-
      {{
        repo_root
          | default(playbook_dir ~ '/..')
      }}

# -----------------------------------------------------------------------------
# 3) Definir diretórios principais do repositório
# -----------------------------------------------------------------------------
- name: "Definir diretórios padrão do repositório (deploy)"
  ansible.builtin.set_fact:
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"
    scripts_root_dir: "{{ repo_root_safe }}/scripts"

# -----------------------------------------------------------------------------
# 4) Candidatos de arquivo de máquina (mesmo padrão do PREDEPLOY)
# -----------------------------------------------------------------------------
- name: "Definir candidatos de arquivo da máquina (deploy)"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ repo_root_safe }}/execution/machines/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/execution/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

# -----------------------------------------------------------------------------
# 5) Verificar quais candidatos existem
# -----------------------------------------------------------------------------
- name: "Verificar candidatos de arquivo da máquina (deploy)"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

# -----------------------------------------------------------------------------
# 6) Selecionar o primeiro arquivo existente
# -----------------------------------------------------------------------------
- name: "Selecionar machine_file existente (deploy)"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

# -----------------------------------------------------------------------------
# 7) Falhar se nenhum candidato existir
# -----------------------------------------------------------------------------
- name: "Falhar se arquivo da máquina não existir em nenhum caminho esperado (deploy)"
  ansible.builtin.fail:
    msg: |
      Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 8) Carregar config da máquina
# -----------------------------------------------------------------------------
- name: "Carregar config da máquina {{ current_machine }} (deploy)"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

# -----------------------------------------------------------------------------
# 9) Carregar config do pacote
# -----------------------------------------------------------------------------
- name: "Carregar config do pacote {{ machine_cfg.package }} (deploy)"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 10) Montar env_vars mescladas
#
# Exemplo:
#   packages/sitef-core-0.0.2-0.yml
#     env_vars:
#       PACKAGE_VERSION: 'sitef-0.0.20'
#
#   machines/sitef-01.yml
#     env_vars:
#       SITEF_ENV: "prod"
#       OUTRA_VAR: "valor"
#
# Resultado: tudo cai em environment do init_deploy.sh
# -----------------------------------------------------------------------------
- name: "Calcular env_vars mescladas (deploy)"
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
            'ROLLBACK_PACKAGE': machine_cfg.rollback | default(''),
            'DEPLOY_ACTION': deploy_action | default('deploy')
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# 11) Usuário/SSH defaults
# -----------------------------------------------------------------------------
- name: "Definir usuário alvo padrão (deploy)"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.target_user | default(machine_cfg.user | default('ec2-user')) }}"

- name: "Definir ssh_common_args padrão (deploy)"
  ansible.builtin.set_fact:
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

- name: "Aplicar ProxyJump via bastion (quando necessário) (deploy)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

# -----------------------------------------------------------------------------
# 12) Registrar host dinâmico
# -----------------------------------------------------------------------------
- name: "Registrar host dinâmico para {{ current_machine }} (deploy)"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip | default(machine_cfg.host | default(''))) }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ ssh_common_args }}"
  changed_when: true

# -----------------------------------------------------------------------------
# 13) Definir caminhos de status e scripts
# -----------------------------------------------------------------------------
- name: "Definir caminhos de status e scripts (deploy)"
  ansible.builtin.set_fact:
    status_file: "{{ status_dir }}/deploy-{{ current_machine }}.json"
    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    filestore_log_path: >-
      {{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.log
    filestore_status_path: >-
      {{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.json"

# -----------------------------------------------------------------------------
# 14) Criar status inicial (queued)
# -----------------------------------------------------------------------------
- name: "Criar status inicial (queued) do deploy"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }

# -----------------------------------------------------------------------------
# 15) Verificar init_deploy.sh no host remoto
# -----------------------------------------------------------------------------
- name: "Verificar init_deploy.sh no host remoto"
  become: true
  ansible.builtin.stat:
    path: "{{ host_deploy_scripts }}/init_deploy.sh"
  delegate_to: "{{ current_machine }}"
  register: deploy_script_stat

- name: "Falhar se init_deploy.sh não existir"
  ansible.builtin.fail:
    msg: "init_deploy.sh não encontrado em {{ host_deploy_scripts }}. Rode o predeploy antes."
  when: not deploy_script_stat.stat.exists

# -----------------------------------------------------------------------------
# 16) Executar init_deploy.sh no host remoto
# -----------------------------------------------------------------------------
- name: "Executar init_deploy.sh no host {{ current_machine }}"
  become: true
  ansible.builtin.shell: |
    set -e
    cd "{{ host_deploy_scripts }}"
    /bin/bash -x ./init_deploy.sh
  delegate_to: "{{ current_machine }}"
  environment: "{{ merged_env_vars }}"
  register: deploy_result
  ignore_errors: true

# -----------------------------------------------------------------------------
# 17) Ler deploy.txt do host
# -----------------------------------------------------------------------------
- name: "Ler deploy.txt do host"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_deploy_scripts }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  delegate_to: "{{ current_machine }}"
  register: deploy_log
  changed_when: false

# -----------------------------------------------------------------------------
# 18) Atualizar status para success/failed
# -----------------------------------------------------------------------------
- name: "Atualizar status para success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
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
        "log_path": "{{ filestore_log_path }}"
      }
  when: deploy_result.rc == 0

- name: "Atualizar status para failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
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
        "log_path": "{{ filestore_log_path }}"
      }
  when: deploy_result.rc != 0

# -----------------------------------------------------------------------------
# 19) Upload status JSON para Harness File Store
# -----------------------------------------------------------------------------
- name: "Upload status JSON para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root_safe }}/harness/upload_filestore.sh" \
      "{{ status_file }}" \
      "{{ filestore_status_path }}"
  args:
    executable: /bin/bash
  changed_when: false
  failed_when: false

# -----------------------------------------------------------------------------
# 20) Upload log do deploy para Harness File Store
# -----------------------------------------------------------------------------
- name: "Upload log do deploy para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root_safe }}/harness/upload_filestore.sh" \
      "-" \
      "{{ filestore_log_path }}" \
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

# -----------------------------------------------------------------------------
# 21) Falhar pipeline se init_deploy.sh retornou erro
# -----------------------------------------------------------------------------
- name: "Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ current_machine }} (host {{ machine_cfg.host | default(machine_cfg.ip | default('')) }})"
  when: deploy_result.rc != 0

