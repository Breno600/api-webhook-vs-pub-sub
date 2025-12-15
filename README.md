---
# =====================================================================================
# DEPLOY POR MÁQUINA
# Chamado por deploy_from_status.yml (PIPELINE 2)
#
# Responsabilidades:
# - Resolver machine_file (fallback)
# - Carregar machine_cfg + package_cfg
# - Montar env_vars efetivo
# - Rodar init_deploy.sh no host (com tee)
# - Ler deploy.txt e init_deploy.log
# - Gerar status.json local (deploy:queued -> deploy:ok|deploy:error)
# - Upload JSON + LOG para Harness File Store (via harness_filestore_upload.yml)
# - Falhar pipeline apenas DEPOIS do upload
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Paths e defaults
# -----------------------------------------------------------------------------
- name: "Deploy | Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    repo_root_safe: "{{ repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..')) }}"
    current_machine: "{{ (machine_name | string | trim) }}"
    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    host_deploy_logs_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/logs"
    host_deploy_init_log: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log"

- name: "Deploy | Definir diretórios principais do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"

# -----------------------------------------------------------------------------
# 2) Resolver machine_file (mesma estratégia do predeploy/rollback)
# -----------------------------------------------------------------------------
- name: "Deploy | Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

- name: "Deploy | Verificar quais candidatos existem"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

- name: "Deploy | Selecionar machine_file existente"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

- name: "Deploy | Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: |
      [deploy] Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 3) Carregar machine_cfg + package_cfg
# -----------------------------------------------------------------------------
- name: "Deploy | Carregar config da máquina {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Deploy | Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.package is defined
      - (machine_cfg.package | string | trim) | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

- name: "Deploy | Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 4) SSH e host dinâmico
# -----------------------------------------------------------------------------
- name: "Deploy | Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default(machine_cfg.target_user | default('ec2-user')) }}"

- name: "Deploy | Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{
        machine_cfg.ssh_common_args
          | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null')
      }}

- name: "Deploy | Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{ target_ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

- name: "Deploy | Registrar host dinâmico"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"
  changed_when: true

# -----------------------------------------------------------------------------
# 5) Montar env final (pacote + máquina + extras)
# -----------------------------------------------------------------------------
- name: "Deploy | Montar env final"
  ansible.builtin.set_fact:
    effective_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': current_machine,
            'SITEF_HOST': (machine_cfg.host | default(machine_cfg.ip | default(''))),
            'DEPLOYMENT_REF': (deployment_ref | default('')),
            'PACKAGE_NAME': (machine_cfg.package | default('')),
            'ROLLBACK_PACKAGE': (machine_cfg.rollback | default(''))
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# 6) Status file local + paths lógicos do filestore
# -----------------------------------------------------------------------------
- name: "Deploy | Definir arquivos de status local"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    status_file: "{{ status_dir }}/{{ current_machine }}/status.json"

    # mantém hierarquia "dev/DEV000000007/" (deployment_ref como veio)
    env_folder: "{{ filestore_env | lower }}"
    deployment_ref_folder: "{{ deployment_ref }}"
    deployment_ref_lower: "{{ deployment_ref | lower }}"

    filestore_json_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}.json"
    filestore_log_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}.log"

- name: "Deploy | Garantir diretório de status da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Deploy | Escrever status inicial (deploy:queued)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package | default('') }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "deploy:queued",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 7) Verificar script e executar deploy (com tee)
# -----------------------------------------------------------------------------
- name: "Deploy | Verificar init_deploy.sh no host"
  become: true
  ansible.builtin.stat:
    path: "{{ host_deploy_scripts }}/init_deploy.sh"
  delegate_to: "{{ current_machine }}"
  register: deploy_script_stat

- name: "Deploy | Falhar se init_deploy.sh não existir"
  ansible.builtin.fail:
    msg: "init_deploy.sh não encontrado em {{ host_deploy_scripts }}. Rode o predeploy antes."
  when: not deploy_script_stat.stat.exists

# Rodar mesmo se der erro para subir status+log e só depois falhar
- name: "Deploy | Executar init_deploy.sh no host (com tee)"
  become: true
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ host_deploy_scripts }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_deploy.sh 2>&1 | tee -a "{{ host_deploy_init_log }}"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  environment: "{{ effective_env }}"
  register: deploy_result
  ignore_errors: true
  changed_when: true

- name: "Deploy | Ler deploy.txt do host (se existir)"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_deploy_scripts }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  register: deploy_txt
  changed_when: false
  failed_when: false

- name: "Deploy | Ler init_deploy.log do host (se existir)"
  become: true
  ansible.builtin.shell: |
    tail -n 2000 "{{ host_deploy_init_log }}" 2>/dev/null || echo "init_deploy.log nao existe"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  register: deploy_init_log_tail
  changed_when: false
  failed_when: false

# -----------------------------------------------------------------------------
# 8) Montar conteúdo do log padronizado (pipeline deploy)
# -----------------------------------------------------------------------------
- name: "Deploy | Montar conteúdo do log do DEPLOY"
  ansible.builtin.set_fact:
    deploy_log_content: |
      ---- pipeline deploy ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=deploy
      rc={{ deploy_result.rc | default('') }}

      ---- stdout (ansible) ----
      {{ deploy_result.stdout | default('') }}

      ---- stderr (ansible) ----
      {{ deploy_result.stderr | default('') }}

      ---- init_deploy.log (tail) ----
      {{ deploy_init_log_tail.stdout | default('') }}

      ---- deploy.txt ----
      {{ deploy_txt.stdout | default('') }}
      ---- pipeline deploy ----

# -----------------------------------------------------------------------------
# 9) Atualizar status final (deploy:ok / deploy:error)
# -----------------------------------------------------------------------------
- name: "Deploy | Atualizar status após execução do init_deploy.sh"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package | default('') }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "{{ 'deploy:ok' if (deploy_result.rc | default(1)) == 0 else 'deploy:error' }}",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ deploy_result.rc | default(1) }},
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 10) Upload JSON + LOG para Harness File Store
# -----------------------------------------------------------------------------
- name: "Deploy | Upload JSON + LOG para Harness File Store"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ status_file }}"
    log_content: "{{ deploy_log_content }}"

    # IMPORTANTE: seu harness_filestore_upload.yml usa parentIdentifier={{ deployment_ref_folder }}
    env_folder: "{{ env_folder }}"
    deployment_ref_folder: "{{ deployment_ref_folder }}"

    # tag no padrão: dev000000007:deploy:ok | dev000000007:deploy:error
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':deploy:' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}

    # (opcional) outras tags extras
    extra_tags: []

# -----------------------------------------------------------------------------
# 11) Falhar pipeline se deploy retornou erro (depois do upload)
# -----------------------------------------------------------------------------
- name: "Deploy | Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ current_machine }} (host {{ machine_cfg.host | default(machine_cfg.ip | default('')) }})"
  when: (deploy_result.rc | default(1)) != 0





