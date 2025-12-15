---
# =====================================================================================
# DEPLOY POR MÁQUINA
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Paths e defaults
# -----------------------------------------------------------------------------
- name: "Deploy | Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    repo_root_safe: "{{ repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..')) }}"
    current_machine: "{{ machine_name | trim }}"
    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    host_deploy_init_log: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log"

- name: "Deploy | Definir diretórios principais do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"

# -----------------------------------------------------------------------------
# 2) Resolver machine_file
# -----------------------------------------------------------------------------
- name: "Deploy | Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

- name: "Deploy | Verificar candidatos"
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
    msg: "Arquivo da máquina {{ current_machine }} não encontrado"
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 3) Carregar configs
# -----------------------------------------------------------------------------
- name: "Deploy | Carregar config da máquina"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Deploy | Carregar config do pacote"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 4) SSH dinâmico
# -----------------------------------------------------------------------------
- name: "Deploy | Registrar host"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip) }}"
    ansible_user: "{{ machine_cfg.user | default('ec2-user') }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null"

# -----------------------------------------------------------------------------
# 5) ENV final
# -----------------------------------------------------------------------------
- name: "Deploy | Montar env final"
  ansible.builtin.set_fact:
    effective_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': current_machine,
            'DEPLOYMENT_REF': deployment_ref,
            'PACKAGE_NAME': machine_cfg.package
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# 6) DEFINIÇÕES IMPORTANTES (CORREÇÃO DO ERRO)
# -----------------------------------------------------------------------------
- name: "Deploy | Definir variáveis base"
  ansible.builtin.set_fact:
    deployment_ref_lower: "{{ deployment_ref | lower }}"
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    status_file: "{{ status_dir }}/{{ current_machine }}/status.json"
    pipeline_log_file: "{{ status_dir }}/{{ current_machine }}/pipeline.log"

    filestore_log_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}.log"

- name: "Deploy | Garantir diretório da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

# -----------------------------------------------------------------------------
# 7) Status inicial (append em history)
# -----------------------------------------------------------------------------
- name: "Deploy | Escrever status deploy:queued"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "package": "{{ machine_cfg.package }}",
        "deployment_ref": "{{ deployment_ref }}",
        "status": "deploy:queued",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}",
        "history": [
          {
            "stage": "deploy",
            "status": "deploy:queued",
            "timestamp": "{{ ansible_date_time.iso8601 }}"
          }
        ]
      }

# -----------------------------------------------------------------------------
# 8) Executar deploy no host
# -----------------------------------------------------------------------------
- name: "Deploy | Executar init_deploy.sh"
  become: true
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ host_deploy_scripts }}"
    /bin/bash -x ./init_deploy.sh 2>&1 | tee -a "{{ host_deploy_init_log }}"
    exit ${PIPESTATUS[0]}
  delegate_to: "{{ current_machine }}"
  environment: "{{ effective_env }}"
  register: deploy_result
  ignore_errors: true

# -----------------------------------------------------------------------------
# 9) Log cumulativo local (append)
# -----------------------------------------------------------------------------
- name: "Deploy | Append no pipeline.log"
  ansible.builtin.blockinfile:
    path: "{{ pipeline_log_file }}"
    insertafter: EOF
    marker: ""
    block: |
      ---- deploy ----
      machine={{ current_machine }}
      rc={{ deploy_result.rc }}
      ts={{ ansible_date_time.iso8601 }}
      {{ deploy_result.stdout | default('') }}

# -----------------------------------------------------------------------------
# 10) Status final (append)
# -----------------------------------------------------------------------------
- name: "Deploy | Atualizar status final"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "package": "{{ machine_cfg.package }}",
        "deployment_ref": "{{ deployment_ref }}",
        "status": "{{ 'deploy:ok' if deploy_result.rc == 0 else 'deploy:error' }}",
        "rc": {{ deploy_result.rc }},
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }

# -----------------------------------------------------------------------------
# 11) Upload para Harness File Store
# -----------------------------------------------------------------------------
- name: "Deploy | Upload JSON + LOG"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ status_file }}"
    log_content: "{{ lookup('ansible.builtin.file', pipeline_log_file) }}"
    deployment_ref_folder: "{{ deployment_ref }}"
    status_tag_value: "{{ deployment_ref_lower }}:deploy:{{ 'ok' if deploy_result.rc == 0 else 'error' }}"

# -----------------------------------------------------------------------------
# 12) Falhar pipeline se erro
# -----------------------------------------------------------------------------
- name: "Deploy | Falhar pipeline se deploy falhou"
  ansible.builtin.fail:
    msg: "Deploy falhou em {{ current_machine }}"
  when: deploy_result.rc != 0
