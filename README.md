---
# =====================================================================================
# DEPLOY POR MÁQUINA
# Chamado por deploy_from_status.yml
#
# Responsabilidades:
# - Localizar arquivo da máquina (com fallback)
# - Carregar machine_cfg + package_cfg
# - Preparar env final
# - Executar init_deploy.sh
# - Ler deploy.txt
# - Gerar status JSON + log padronizado
# - Upload JSON + LOG no Harness File Store (via Ansible)
# - Falhar pipeline se deploy falhou
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Definir paths e defaults
# -----------------------------------------------------------------------------
- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    repo_root_safe: "{{ repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..')) }}"
    current_machine: "{{ (machine_name | string | trim) }}"
    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"

- name: "Definir diretórios principais do repositório (deploy)"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"

# -----------------------------------------------------------------------------
# 2) Candidatos de arquivo de máquina (mesma estratégia do predeploy/rollback)
# -----------------------------------------------------------------------------
- name: "Definir candidatos de arquivo da máquina (deploy)"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

- name: "Verificar quais candidatos existem (deploy)"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

- name: "Selecionar machine_file existente (deploy)"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

- name: "Falhar se arquivo da máquina não existir (deploy)"
  ansible.builtin.fail:
    msg: |
      [deploy] Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 3) Carregar machine_cfg + package_cfg
# -----------------------------------------------------------------------------
- name: "Carregar config da máquina (deploy) {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Validar package definido (deploy)"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.package is defined
      - (machine_cfg.package | string | trim) | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

- name: "Carregar config do pacote (deploy) {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 4) SSH e host dinâmico
# -----------------------------------------------------------------------------
- name: "Definir usuário alvo padrão (deploy)"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default(machine_cfg.target_user | default('ec2-user')) }}"

- name: "Definir ssh_common_args padrão (deploy)"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{
        machine_cfg.ssh_common_args
          | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null')
      }}

- name: "Aplicar ProxyJump via bastion (quando necessário) (deploy)"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{ target_ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

- name: "Registrar host dinâmico (deploy)"
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
- name: "Montar env final (deploy)"
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
# 6) Status file local (controller) e paths lógicos do filestore
# -----------------------------------------------------------------------------
- name: "Definir arquivos de status local (deploy)"
  ansible.builtin.set_fact:
    status_file: "{{ status_dir }}/{{ current_machine }}/status.json"
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    filestore_json_path: "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.json"
    filestore_log_path: "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.log"

- name: "Garantir diretório de status da máquina (deploy)"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Escrever status inicial (deploy:queued)"
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
# 7) Verificar script e executar deploy (com captura de stdout/stderr)
# -----------------------------------------------------------------------------
- name: "Verificar init_deploy.sh no host"
  become: true
  ansible.builtin.stat:
    path: "{{ host_deploy_scripts }}/init_deploy.sh"
  delegate_to: "{{ current_machine }}"
  register: deploy_script_stat

- name: "Falhar se init_deploy.sh não existir"
  ansible.builtin.fail:
    msg: "init_deploy.sh não encontrado em {{ host_deploy_scripts }}. Rode o predeploy antes."
  when: not deploy_script_stat.stat.exists

# Rodar mesmo se der erro pra gente conseguir subir log/JSON e só depois falhar pipeline
- name: "Executar init_deploy.sh no host"
  become: true
  ansible.builtin.shell: |
    cd "{{ host_deploy_scripts }}"
    /bin/bash -x ./init_deploy.sh
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  environment: "{{ effective_env }}"
  register: deploy_result
  ignore_errors: true
  changed_when: true

- name: "Ler deploy.txt do host (se existir)"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_deploy_scripts }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  register: deploy_txt
  changed_when: false
  failed_when: false

# -----------------------------------------------------------------------------
# 8) Montar log padronizado (igual predeploy/rollback)
# -----------------------------------------------------------------------------
- name: "Montar conteúdo do log do DEPLOY"
  ansible.builtin.set_fact:
    deploy_log_content: |
      ---- pipeline deploy ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=deploy
      rc={{ deploy_result.rc | default('') }}

      ---- stdout ----
      {{ deploy_result.stdout | default('') }}

      ---- stderr ----
      {{ deploy_result.stderr | default('') }}

      ---- deploy.txt ----
      {{ deploy_txt.stdout | default('') }}
      ---- pipeline deploy ----

# -----------------------------------------------------------------------------
# 9) Atualizar status final (deploy:ok / deploy:error)
# -----------------------------------------------------------------------------
- name: "Atualizar status após execução do init_deploy.sh"
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
# 10) Upload JSON + LOG para Harness File Store (via Ansible)
# -----------------------------------------------------------------------------
- name: "Upload JSON + LOG do DEPLOY para Harness File Store (via Ansible)"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ status_file }}"
    log_content: "{{ deploy_log_content }}"

    # tag no padrão: dev000000007:deploy:ok | dev000000007:deploy:error
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':deploy:' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}

    # (opcional) se quiser adicionar outras tags, usa essa lista
    extra_tags: []

# -----------------------------------------------------------------------------
# 11) Falhar pipeline se deploy retornou erro (depois do upload)
# -----------------------------------------------------------------------------
- name: "Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ current_machine }} (host {{ machine_cfg.host | default(machine_cfg.ip | default('')) }})"
  when: (deploy_result.rc | default(1)) != 0
