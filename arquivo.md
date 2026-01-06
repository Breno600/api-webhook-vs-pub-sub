Rodei a pipeline e identifiquei que se eu passo 2 maquinas por exemplo pega o mesmo ip que a primeira maquinas sitef-3 e sitef-1 esta com o mesmo ip, mas nao sao o mesmo ip , segue ansible

sitef-03 host: "100.99.54.6"
sitef-01 host: "100.99.25.45"

# predeploy_from_execution.yml
# =====================================================================================
# PIPELINE 1 - PREDEPLOY (a partir do arquivo de execução)
# =====================================================================================

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    # -------------------------------
    # Entradas principais (RESOLVIDAS)
    # -------------------------------
    execution_file_name_resolved: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref_resolved: "{{ deployment_ref | default('DEV000000001') }}"

    # -------------------------------
    # Paths do repositório
    # -------------------------------
    repo_root_resolved: "{{ playbook_dir }}/.."
    status_dir_resolved: "{{ (playbook_dir ~ '/..') }}/status/{{ deployment_ref_resolved }}"

    # -------------------------------
    # Nexus (ideal vir de secrets do Harness)
    # -------------------------------
    nexus_base_url_resolved: "{{ nexus_base_url | default('https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev') }}"
    nexus_user_resolved: "{{ nexus_user | default('') }}"
    nexus_password_resolved: "{{ nexus_password | default('') }}"

    # -------------------------------
    # Harness (pode vir por -e ou env)
    # -------------------------------
    harness_endpoint_resolved: "{{ harness_endpoint | default('') }}"
    harness_account_id_resolved: "{{ harness_account_id | default('') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('') }}"
    harness_api_key_resolved: "{{ harness_api_key | default('') }}"
    harness_x_api_key_resolved: "{{ harness_x_api_key | default('') }}"

    # Stage (usado pelo harness upload)
    hf_stage_name: "predeploy"

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
                ) ~ '/' ~ deployment_ref_resolved
              )
          }}

    # ---------------------------------------
    # Mostrar variáveis resolvidas (debug)
    # ---------------------------------------
    - name: "Mostrar variáveis de entrada e resolvidas"
      ansible.builtin.debug:
        msg:
          - "execution_file_name_resolved = {{ execution_file_name_resolved }}"
          - "deployment_ref_resolved      = {{ deployment_ref_resolved }}"
          - "repo_root_resolved           = {{ repo_root_resolved }}"
          - "status_dir_resolved          = {{ status_dir_resolved }}"
          - "filestore_env_resolved       = {{ filestore_env_resolved }}"
          - "filestore_base_dir_resolved  = {{ filestore_base_dir_resolved }}"
          - "nexus_base_url_resolved      = {{ nexus_base_url_resolved }}"
      changed_when: false

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
        file: "{{ repo_root_resolved }}/{{ execution_file_name_resolved }}"
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
        deployment_ref: "{{ deployment_ref_resolved }}"
        repo_root: "{{ repo_root_resolved }}"
        status_dir: "{{ status_dir_resolved }}"

        nexus_base_url: "{{ nexus_base_url_resolved }}"
        nexus_user: "{{ nexus_user_resolved }}"
        nexus_password: "{{ nexus_password_resolved }}"

        filestore_env: "{{ filestore_env_resolved }}"
        filestore_base_dir: "{{ filestore_base_dir_resolved }}"

        # Harness vars (podem ser vazios; o include resolve com fallback/env)
        harness_endpoint: "{{ harness_endpoint_resolved }}"
        harness_account_id: "{{ harness_account_id_resolved }}"
        harness_org_id: "{{ harness_org_id_resolved }}"
        harness_project_id: "{{ harness_project_id_resolved }}"
        harness_api_key: "{{ harness_api_key_resolved }}"
        harness_x_api_key: "{{ harness_x_api_key_resolved }}"
        hf_stage_name: "{{ hf_stage_name }}"

# predeploy_per_machine.yml
---
# =====================================================================================
# PREDEPLOY POR MÁQUINA (COMPLETO / CORRIGIDO)
# Chamado por: predeploy_from_execution.yml
# =====================================================================================

# -----------------------------------------------------------------------------
# 0) Resolver nome da máquina atual + stage
# -----------------------------------------------------------------------------
- name: "Predeploy | Resolver nome da máquina atual"
  ansible.builtin.set_fact:
    stage_name: "predeploy"
    current_machine: "{{ (machine_name | default(item) | default('')) | string | trim }}"

- name: "Predeploy | Validar vars mínimas"
  ansible.builtin.assert:
    that:
      - (current_machine | length) > 0
      - deployment_ref is defined
      - (deployment_ref | string | trim | length) > 0
      - filestore_env is defined
      - (filestore_env | string | trim | length) > 0
      - filestore_base_dir is defined
      - (filestore_base_dir | string | trim | length) > 0
      - status_dir is defined
      - (status_dir | string | trim | length) > 0
      - nexus_base_url is defined
      - (nexus_base_url | string | trim | length) > 0
    fail_msg: "Faltam variáveis obrigatórias para o predeploy_per_machine.yml"

# -----------------------------------------------------------------------------
# 1) Paths base do repositório
# -----------------------------------------------------------------------------
- name: "Predeploy | Definir paths base do repositório"
  ansible.builtin.set_fact:
    repo_root_safe: "{{ repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..')) }}"
    execution_dir: "{{ (repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..'))) }}/execution"
    machines_dir: "{{ (repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..'))) }}/machines"
    packages_dir: "{{ (repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..'))) }}/packages"
    scripts_root_dir: "{{ (repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..'))) }}/scripts"

# -----------------------------------------------------------------------------
# 2) Resolver machine_file
# -----------------------------------------------------------------------------
- name: "Predeploy | Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

- name: "Predeploy | Verificar candidatos"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

- name: "Predeploy | Selecionar machine_file existente"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

- name: "Predeploy | Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: |
      [predeploy] Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 3) Carregar machine_cfg + package_cfg
# -----------------------------------------------------------------------------
- name: "Predeploy | Carregar config da máquina {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Predeploy | Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.package is defined
      - (machine_cfg.package | string | trim | length) > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

- name: "Predeploy | Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

- name: "Predeploy | Validar components do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      - package_cfg.components is defined
      - (package_cfg.components | length) > 0
    fail_msg: "components ausente/vazio no pacote {{ machine_cfg.package }}"

- name: "Predeploy | Validar script do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg.script is defined
      - (package_cfg.script | string | trim | length) > 0
    fail_msg: "package_cfg.script ausente/vazio no pacote {{ machine_cfg.package }}"

# -----------------------------------------------------------------------------
# 4) SSH e host dinâmico
# -----------------------------------------------------------------------------
- name: "Predeploy | Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default(machine_cfg.target_user | default('root')) }}"

- name: "Predeploy | Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

- name: "Predeploy | Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

- name: "Predeploy | Registrar host dinâmico para {{ current_machine }}"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}"
    ansible_user: "{{ target_user }}"
    ansible_ssh_common_args: "{{ ssh_common_args }}"
    ansible_connection: ssh
  changed_when: true

# -----------------------------------------------------------------------------
# 5) Montar env final (package base + machine override) + extras
# -----------------------------------------------------------------------------
- name: "Predeploy | Montar env final (package base + machine override)"
  ansible.builtin.set_fact:
    merged_env_vars: >-
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
# 6) Normalizações + run_id
# -----------------------------------------------------------------------------
- name: "Predeploy | Normalizações"
  ansible.builtin.set_fact:
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"
    env_lower: "{{ (filestore_env | string | trim | lower) }}"
    machine_lower: "{{ (current_machine | string | trim | lower) }}"

- name: "Predeploy | Gerar run_id"
  ansible.builtin.set_fact:
    run_id: "{{ deployment_ref_lower ~ '-' ~ machine_lower ~ '-' ~ env_lower ~ '-' ~ stage_name ~ '-' ~ ansible_date_time.epoch }}"

# -----------------------------------------------------------------------------
# 7) Paths local (controller) + paths remotos do pipeline
# -----------------------------------------------------------------------------
- name: "Predeploy | Definir arquivos/diretórios locais"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    machine_status_file: "{{ status_dir }}/{{ current_machine }}/status.json"
    pipeline_log_file: "{{ status_dir }}/{{ current_machine }}/pipeline.log"

- name: "Predeploy | Definir paths lógicos (File Store) SEPARADOS POR STAGE"
  ansible.builtin.set_fact:
    filestore_log_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ machine_lower }}-{{ env_lower }}-{{ stage_name }}.log"
    filestore_status_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ machine_lower }}-{{ env_lower }}-{{ stage_name }}.json"

- name: "Predeploy | Garantir diretório de status local"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Predeploy | Garantir arquivo pipeline.log (cumulativo local)"
  ansible.builtin.file:
    path: "{{ pipeline_log_file }}"
    state: touch
    mode: "0644"

# -----------------------------------------------------------------------------
# 8) Status.json com history (append) - queued
# -----------------------------------------------------------------------------
- name: "Predeploy | Verificar se status.json já existe"
  ansible.builtin.stat:
    path: "{{ machine_status_file }}"
  register: status_stat

- name: "Predeploy | Ler status.json existente (se existir)"
  ansible.builtin.slurp:
    path: "{{ machine_status_file }}"
  register: status_slurp
  when: status_stat.stat.exists

- name: "Predeploy | Parse do status existente (ou base vazio)"
  ansible.builtin.set_fact:
    status_obj: "{{ (status_slurp.content | b64decode | from_json) if (status_stat.stat.exists | default(false)) else {} }}"

- name: "Predeploy | Escrever status inicial (predeploy:queued) com history append"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: >-
      {{
        (
          status_obj
          | combine({
              "run_id": run_id,
              "stage": stage_name,
              "machine": current_machine,
              "host": (machine_cfg.host | default(machine_cfg.ip | default(''))),
              "package": (machine_cfg.package | default('')),
              "rollback": (machine_cfg.rollback | default('')),
              "deployment_ref": (deployment_ref | default('')),
              "log_path": filestore_log_path,
              "status": "predeploy:queued",
              "timestamp": ansible_date_time.iso8601,
              "history": (
                (status_obj.history | default([]))
                + [ {
                      "run_id": run_id,
                      "stage": stage_name,
                      "status": "predeploy:queued",
                      "timestamp": ansible_date_time.iso8601
                    } ]
              )
            }, recursive=True)
        ) | to_nice_json
      }}
  changed_when: true

# -----------------------------------------------------------------------------
# 9) Preparar diretórios no host remoto
# -----------------------------------------------------------------------------
- name: "Predeploy | Definir diretórios remotos do pipeline"
  ansible.builtin.set_fact:
    deploy_base_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/components"
    deploy_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    deploy_scripts_package_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"

- name: "Predeploy | Garantir base do pipeline no host"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  delegate_to: "{{ current_machine }}"
  loop:
    - "{{ deploy_base_dir }}"
    - "{{ deploy_scripts_dir }}"
    - "{{ deploy_scripts_package_dir }}"

# -----------------------------------------------------------------------------
# 10) Limpar pasta do package scripts e recriar
# -----------------------------------------------------------------------------
- name: "Predeploy | Limpar pasta de scripts do pacote"
  become: true
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: absent
  delegate_to: "{{ current_machine }}"

- name: "Predeploy | Recriar pasta de scripts do pacote"
  become: true
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: directory
    mode: "0755"
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 11) Copiar package.yml para /deploy/scripts/package/
# -----------------------------------------------------------------------------
- name: "Predeploy | Copiar package.yml para o host"
  become: true
  ansible.builtin.copy:
    src: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    dest: "{{ deploy_scripts_package_dir }}/{{ machine_cfg.package }}.yml"
    mode: "0644"
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 12) Garantir diretórios dos componentes + baixar do Nexus
# -----------------------------------------------------------------------------
- name: "Predeploy | Garantir diretórios dos componentes no host"
  become: true
  ansible.builtin.file:
    path: "{{ deploy_base_dir }}/{{ item | dirname }}"
    state: directory
    mode: "0755"
  loop: "{{ package_cfg.components }}"
  delegate_to: "{{ current_machine }}"

- name: "Predeploy | Baixar componentes do Nexus"
  become: true
  ansible.builtin.get_url:
    url: "{{ nexus_base_url.rstrip('/') }}/{{ item }}"
    dest: "{{ deploy_base_dir }}/{{ item }}"
    mode: "0644"
    url_username: "{{ nexus_user | default(omit) }}"
    url_password: "{{ nexus_password | default(omit) }}"
  loop: "{{ package_cfg.components }}"
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 13) Copiar scripts do pacote para /deploy/scripts
# -----------------------------------------------------------------------------
- name: "Predeploy | Copiar scripts do pacote para o host"
  become: true
  ansible.builtin.copy:
    src: "{{ scripts_root_dir }}/{{ package_cfg.script }}/"
    dest: "{{ deploy_scripts_dir }}/"
    mode: "0755"
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 14) Executar init_parallel.sh na máquina alvo (com tee -a)
# -----------------------------------------------------------------------------
- name: "Predeploy | Executar init_parallel.sh no host (com log + stdout)"
  become: true
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ deploy_scripts_dir }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_parallel.sh 2>&1 | tee -a "{{ deploy_scripts_dir }}/init_parallel.log"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  delegate_to: "{{ current_machine }}"
  register: init_parallel_result
  ignore_errors: true
  changed_when: true

- name: "Predeploy | Tail do init_parallel.log (sempre)"
  become: true
  ansible.builtin.shell: |
    echo "===== tail -n 800 {{ deploy_scripts_dir }}/init_parallel.log ====="
    tail -n 800 "{{ deploy_scripts_dir }}/init_parallel.log" 2>/dev/null || echo "log não encontrado"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  register: predeploy_tail
  changed_when: false
  failed_when: false

# -----------------------------------------------------------------------------
# 15) Montar bloco de log + append no pipeline.log (controller)
# -----------------------------------------------------------------------------
- name: "Predeploy | Montar conteúdo do log (bloco predeploy)"
  ansible.builtin.set_fact:
    predeploy_log_block: |
      ---- pipeline predeploy ----
      run_id={{ run_id }}
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage={{ stage_name }}
      rc={{ init_parallel_result.rc | default(1) }}
      ts={{ ansible_date_time.iso8601 }}

      ---- stdout (ansible) ----
      {{ init_parallel_result.stdout | default('') }}

      ---- stderr (ansible) ----
      {{ init_parallel_result.stderr | default('') }}

      ---- init_parallel.log (tail) ----
      {{ predeploy_tail.stdout | default('') }}
      ---- pipeline predeploy ----

- name: "Predeploy | Append do bloco no pipeline.log (controller)"
  ansible.builtin.blockinfile:
    path: "{{ pipeline_log_file }}"
    marker: ""
    insertafter: EOF
    block: |
      {{ predeploy_log_block }}
  changed_when: true

- name: "Predeploy | Carregar conteúdo completo do pipeline.log (controller)"
  ansible.builtin.set_fact:
    pipeline_log_content_full: "{{ lookup('ansible.builtin.file', pipeline_log_file) }}"

# -----------------------------------------------------------------------------
# 16) Atualizar status final (predeploy:ok / predeploy:error) com history append
# -----------------------------------------------------------------------------
- name: "Predeploy | Ler status.json atual"
  ansible.builtin.slurp:
    path: "{{ machine_status_file }}"
  register: status_after_queued_slurp

- name: "Predeploy | Parse do status atual"
  ansible.builtin.set_fact:
    status_now: "{{ status_after_queued_slurp.content | b64decode | from_json }}"

- name: "Predeploy | Atualizar status final (append em history)"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: >-
      {{
        (
          status_now
          | combine({
              "run_id": run_id,
              "stage": stage_name,
              "status": ("predeploy:ok" if (init_parallel_result.rc | default(1)) == 0 else "predeploy:error"),
              "timestamp": ansible_date_time.iso8601,
              "rc": (init_parallel_result.rc | default(1)),
              "history": (
                (status_now.history | default([]))
                + [ {
                      "run_id": run_id,
                      "stage": stage_name,
                      "status": ("predeploy:ok" if (init_parallel_result.rc | default(1)) == 0 else "predeploy:error"),
                      "rc": (init_parallel_result.rc | default(1)),
                      "timestamp": ansible_date_time.iso8601
                    } ]
              )
            }, recursive=True)
        ) | to_nice_json
      }}
  changed_when: true

- name: "Predeploy | Definir status_tag_value (tag usada no File Store)"
  ansible.builtin.set_fact:
    status_tag_value: "{{ 'predeploy:ok' if (init_parallel_result.rc | default(1)) == 0 else 'predeploy:error' }}"


# -----------------------------------------------------------------------------
# 17) Upload para Harness File Store (SEMPRE) - não pode quebrar a pipeline
# -----------------------------------------------------------------------------
- name: "Predeploy | Upload JSON + LOG para Harness File Store (sempre)"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    stage_name: "predeploy"
    log_content: "{{ pipeline_log_content_full }}"
# -----------------------------------------------------------------------------
# 18) Falhar pipeline se predeploy deu erro (DEPOIS do upload)
# -----------------------------------------------------------------------------
- name: "Predeploy | Falhar se init_parallel.sh retornou erro (depois do upload)"
  ansible.builtin.fail:
    msg: "PREDEPLOY falhou em {{ current_machine }} (rc={{ init_parallel_result.rc | default(1) }})"
  when: (init_parallel_result.rc | default(1)) != 0


segue log completo da pipeline 

Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export GIT_TAG=e29b3779a677a3c82dd51cfe01cfed26b33476b4
Clonando repo em /tmp/tmp.ekQ9MCDbN3...
Cloning into '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 1519, done.
remote: Counting objects:   0% (1/1469)
remote: Counting objects:   1% (15/1469)
remote: Counting objects:   2% (30/1469)
remote: Counting objects:   3% (45/1469)
remote: Counting objects:   4% (59/1469)
remote: Counting objects:   5% (74/1469)
remote: Counting objects:   6% (89/1469)
remote: Counting objects:   7% (103/1469)
remote: Counting objects:   8% (118/1469)
remote: Counting objects:   9% (133/1469)
remote: Counting objects:  10% (147/1469)
remote: Counting objects:  11% (162/1469)
remote: Counting objects:  12% (177/1469)
remote: Counting objects:  13% (191/1469)
remote: Counting objects:  14% (206/1469)
remote: Counting objects:  15% (221/1469)
remote: Counting objects:  16% (236/1469)
remote: Counting objects:  17% (250/1469)
remote: Counting objects:  18% (265/1469)
remote: Counting objects:  19% (280/1469)
remote: Counting objects:  20% (294/1469)
remote: Counting objects:  21% (309/1469)
remote: Counting objects:  22% (324/1469)
remote: Counting objects:  23% (338/1469)
remote: Counting objects:  24% (353/1469)
remote: Counting objects:  25% (368/1469)
remote: Counting objects:  26% (382/1469)
remote: Counting objects:  27% (397/1469)
remote: Counting objects:  28% (412/1469)
remote: Counting objects:  29% (427/1469)
remote: Counting objects:  30% (441/1469)
remote: Counting objects:  31% (456/1469)
remote: Counting objects:  32% (471/1469)
remote: Counting objects:  33% (485/1469)
remote: Counting objects:  34% (500/1469)
remote: Counting objects:  35% (515/1469)
remote: Counting objects:  36% (529/1469)
remote: Counting objects:  37% (544/1469)
remote: Counting objects:  38% (559/1469)
remote: Counting objects:  39% (573/1469)
remote: Counting objects:  40% (588/1469)
remote: Counting objects:  41% (603/1469)
remote: Counting objects:  42% (617/1469)
remote: Counting objects:  43% (632/1469)
remote: Counting objects:  44% (647/1469)
remote: Counting objects:  45% (662/1469)
remote: Counting objects:  46% (676/1469)
remote: Counting objects:  47% (691/1469)
remote: Counting objects:  48% (706/1469)
remote: Counting objects:  49% (720/1469)
remote: Counting objects:  50% (735/1469)
remote: Counting objects:  51% (750/1469)
remote: Counting objects:  52% (764/1469)
remote: Counting objects:  53% (779/1469)
remote: Counting objects:  54% (794/1469)
remote: Counting objects:  55% (808/1469)
remote: Counting objects:  56% (823/1469)
remote: Counting objects:  57% (838/1469)
remote: Counting objects:  58% (853/1469)
remote: Counting objects:  59% (867/1469)
remote: Counting objects:  60% (882/1469)
remote: Counting objects:  61% (897/1469)
remote: Counting objects:  62% (911/1469)
remote: Counting objects:  63% (926/1469)
remote: Counting objects:  64% (941/1469)
remote: Counting objects:  65% (955/1469)
remote: Counting objects:  66% (970/1469)
remote: Counting objects:  67% (985/1469)
remote: Counting objects:  68% (999/1469)
remote: Counting objects:  69% (1014/1469)
remote: Counting objects:  70% (1029/1469)
remote: Counting objects:  71% (1043/1469)
remote: Counting objects:  72% (1058/1469)
remote: Counting objects:  73% (1073/1469)
remote: Counting objects:  74% (1088/1469)
remote: Counting objects:  75% (1102/1469)
remote: Counting objects:  76% (1117/1469)
remote: Counting objects:  77% (1132/1469)
remote: Counting objects:  78% (1146/1469)
remote: Counting objects:  79% (1161/1469)
remote: Counting objects:  80% (1176/1469)
remote: Counting objects:  81% (1190/1469)
remote: Counting objects:  82% (1205/1469)
remote: Counting objects:  83% (1220/1469)
remote: Counting objects:  84% (1234/1469)
remote: Counting objects:  85% (1249/1469)
remote: Counting objects:  86% (1264/1469)
remote: Counting objects:  87% (1279/1469)
remote: Counting objects:  88% (1293/1469)
remote: Counting objects:  89% (1308/1469)
remote: Counting objects:  90% (1323/1469)
remote: Counting objects:  91% (1337/1469)
remote: Counting objects:  92% (1352/1469)
remote: Counting objects:  93% (1367/1469)
remote: Counting objects:  94% (1381/1469)
remote: Counting objects:  95% (1396/1469)
remote: Counting objects:  96% (1411/1469)
remote: Counting objects:  97% (1425/1469)
remote: Counting objects:  98% (1440/1469)
remote: Counting objects:  99% (1455/1469)
remote: Counting objects: 100% (1469/1469)
remote: Counting objects: 100% (1469/1469), done.
remote: Compressing objects:   0% (1/687)
remote: Compressing objects:   1% (7/687)
remote: Compressing objects:   2% (14/687)
remote: Compressing objects:   3% (21/687)
remote: Compressing objects:   4% (28/687)
remote: Compressing objects:   5% (35/687)
remote: Compressing objects:   6% (42/687)
remote: Compressing objects:   7% (49/687)
remote: Compressing objects:   8% (55/687)
remote: Compressing objects:   9% (62/687)
remote: Compressing objects:  10% (69/687)
remote: Compressing objects:  11% (76/687)
remote: Compressing objects:  12% (83/687)
remote: Compressing objects:  13% (90/687)
remote: Compressing objects:  14% (97/687)
remote: Compressing objects:  15% (104/687)
remote: Compressing objects:  16% (110/687)
remote: Compressing objects:  17% (117/687)
remote: Compressing objects:  18% (124/687)
remote: Compressing objects:  19% (131/687)
remote: Compressing objects:  20% (138/687)
remote: Compressing objects:  21% (145/687)
remote: Compressing objects:  22% (152/687)
remote: Compressing objects:  23% (159/687)
remote: Compressing objects:  24% (165/687)
remote: Compressing objects:  25% (172/687)
remote: Compressing objects:  26% (179/687)
remote: Compressing objects:  27% (186/687)
remote: Compressing objects:  28% (193/687)
remote: Compressing objects:  29% (200/687)
remote: Compressing objects:  30% (207/687)
remote: Compressing objects:  31% (213/687)
remote: Compressing objects:  32% (220/687)
remote: Compressing objects:  33% (227/687)
remote: Compressing objects:  34% (234/687)
remote: Compressing objects:  35% (241/687)
remote: Compressing objects:  36% (248/687)
remote: Compressing objects:  37% (255/687)
remote: Compressing objects:  38% (262/687)
remote: Compressing objects:  39% (268/687)
remote: Compressing objects:  40% (275/687)
remote: Compressing objects:  41% (282/687)
remote: Compressing objects:  42% (289/687)
remote: Compressing objects:  43% (296/687)
remote: Compressing objects:  44% (303/687)
remote: Compressing objects:  45% (310/687)
remote: Compressing objects:  46% (317/687)
remote: Compressing objects:  47% (323/687)
remote: Compressing objects:  48% (330/687)
remote: Compressing objects:  49% (337/687)
remote: Compressing objects:  50% (344/687)
remote: Compressing objects:  51% (351/687)
remote: Compressing objects:  52% (358/687)
remote: Compressing objects:  53% (365/687)
remote: Compressing objects:  54% (371/687)
remote: Compressing objects:  55% (378/687)
remote: Compressing objects:  56% (385/687)
remote: Compressing objects:  57% (392/687)
remote: Compressing objects:  58% (399/687)
remote: Compressing objects:  59% (406/687)
remote: Compressing objects:  60% (413/687)
remote: Compressing objects:  61% (420/687)
remote: Compressing objects:  62% (426/687)
remote: Compressing objects:  63% (433/687)
remote: Compressing objects:  64% (440/687)
remote: Compressing objects:  65% (447/687)
remote: Compressing objects:  66% (454/687)
remote: Compressing objects:  67% (461/687)
remote: Compressing objects:  68% (468/687)
remote: Compressing objects:  69% (475/687)
remote: Compressing objects:  70% (481/687)
remote: Compressing objects:  71% (488/687)
remote: Compressing objects:  72% (495/687)
remote: Compressing objects:  73% (502/687)
remote: Compressing objects:  74% (509/687)
remote: Compressing objects:  75% (516/687)
remote: Compressing objects:  76% (523/687)
remote: Compressing objects:  77% (529/687)
remote: Compressing objects:  78% (536/687)
remote: Compressing objects:  79% (543/687)
remote: Compressing objects:  80% (550/687)
remote: Compressing objects:  81% (557/687)
remote: Compressing objects:  82% (564/687)
remote: Compressing objects:  83% (571/687)
remote: Compressing objects:  84% (578/687)
remote: Compressing objects:  85% (584/687)
remote: Compressing objects:  86% (591/687)
remote: Compressing objects:  87% (598/687)
remote: Compressing objects:  88% (605/687)
remote: Compressing objects:  89% (612/687)
remote: Compressing objects:  90% (619/687)
remote: Compressing objects:  91% (626/687)
remote: Compressing objects:  92% (633/687)
remote: Compressing objects:  93% (639/687)
remote: Compressing objects:  94% (646/687)
remote: Compressing objects:  95% (653/687)
remote: Compressing objects:  96% (660/687)
remote: Compressing objects:  97% (667/687)
remote: Compressing objects:  98% (674/687)
remote: Compressing objects:  99% (681/687)
remote: Compressing objects: 100% (687/687)
remote: Compressing objects: 100% (687/687), done.
Receiving objects:   0% (1/1519)
Receiving objects:   1% (16/1519)
Receiving objects:   2% (31/1519)
Receiving objects:   3% (46/1519)
Receiving objects:   4% (61/1519)
Receiving objects:   5% (76/1519)
Receiving objects:   6% (92/1519)
Receiving objects:   7% (107/1519)
Receiving objects:   8% (122/1519)
Receiving objects:   9% (137/1519)
Receiving objects:  10% (152/1519)
Receiving objects:  11% (168/1519)
Receiving objects:  12% (183/1519)
Receiving objects:  13% (198/1519)
Receiving objects:  14% (213/1519)
Receiving objects:  15% (228/1519)
Receiving objects:  16% (244/1519)
Receiving objects:  17% (259/1519)
Receiving objects:  18% (274/1519)
Receiving objects:  19% (289/1519)
Receiving objects:  20% (304/1519)
Receiving objects:  21% (319/1519)
Receiving objects:  22% (335/1519)
Receiving objects:  23% (350/1519)
Receiving objects:  24% (365/1519)
Receiving objects:  25% (380/1519)
Receiving objects:  26% (395/1519)
Receiving objects:  27% (411/1519)
Receiving objects:  28% (426/1519)
Receiving objects:  29% (441/1519)
Receiving objects:  30% (456/1519)
Receiving objects:  31% (471/1519)
Receiving objects:  32% (487/1519)
Receiving objects:  33% (502/1519)
Receiving objects:  34% (517/1519)
Receiving objects:  35% (532/1519)
Receiving objects:  36% (547/1519)
Receiving objects:  37% (563/1519)
Receiving objects:  38% (578/1519)
Receiving objects:  39% (593/1519)
Receiving objects:  40% (608/1519)
Receiving objects:  41% (623/1519)
Receiving objects:  42% (638/1519)
Receiving objects:  43% (654/1519)
Receiving objects:  44% (669/1519)
Receiving objects:  45% (684/1519)
Receiving objects:  46% (699/1519)
Receiving objects:  47% (714/1519)
Receiving objects:  48% (730/1519)
Receiving objects:  49% (745/1519)
Receiving objects:  50% (760/1519)
Receiving objects:  51% (775/1519)
Receiving objects:  52% (790/1519)
Receiving objects:  53% (806/1519)
Receiving objects:  54% (821/1519)
Receiving objects:  55% (836/1519)
Receiving objects:  56% (851/1519)
Receiving objects:  57% (866/1519)
Receiving objects:  58% (882/1519)
Receiving objects:  59% (897/1519)
Receiving objects:  60% (912/1519)
Receiving objects:  61% (927/1519)
Receiving objects:  62% (942/1519)
Receiving objects:  63% (957/1519)
Receiving objects:  64% (973/1519)
Receiving objects:  65% (988/1519)
Receiving objects:  66% (1003/1519)
Receiving objects:  67% (1018/1519)
Receiving objects:  68% (1033/1519)
Receiving objects:  69% (1049/1519)
Receiving objects:  70% (1064/1519)
Receiving objects:  71% (1079/1519)
Receiving objects:  72% (1094/1519)
Receiving objects:  73% (1109/1519)
Receiving objects:  74% (1125/1519)
Receiving objects:  75% (1140/1519)
Receiving objects:  76% (1155/1519)
Receiving objects:  77% (1170/1519)
Receiving objects:  78% (1185/1519)
Receiving objects:  79% (1201/1519)
Receiving objects:  80% (1216/1519)
Receiving objects:  81% (1231/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  82% (1246/1519), 899.71 KiB | 1.50 MiB/s
remote: Total 1519 (delta 1049), reused 1036 (delta 749), pack-reused 50
Receiving objects:  83% (1261/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  84% (1276/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  85% (1292/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  86% (1307/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  87% (1322/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  88% (1337/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  89% (1352/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  90% (1368/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  91% (1383/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  92% (1398/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  93% (1413/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  94% (1428/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  95% (1444/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  96% (1459/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  97% (1474/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  98% (1489/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects:  99% (1504/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects: 100% (1519/1519), 899.71 KiB | 1.50 MiB/s
Receiving objects: 100% (1519/1519), 1.02 MiB | 1.75 MiB/s, done.
Resolving deltas:   0% (0/1068)
Resolving deltas:   1% (11/1068)
Resolving deltas:   2% (22/1068)
Resolving deltas:   3% (33/1068)
Resolving deltas:   4% (43/1068)
Resolving deltas:   5% (54/1068)
Resolving deltas:   6% (65/1068)
Resolving deltas:   7% (75/1068)
Resolving deltas:   8% (86/1068)
Resolving deltas:   9% (97/1068)
Resolving deltas:  10% (107/1068)
Resolving deltas:  11% (118/1068)
Resolving deltas:  12% (129/1068)
Resolving deltas:  13% (139/1068)
Resolving deltas:  14% (150/1068)
Resolving deltas:  15% (161/1068)
Resolving deltas:  16% (171/1068)
Resolving deltas:  17% (182/1068)
Resolving deltas:  18% (193/1068)
Resolving deltas:  19% (203/1068)
Resolving deltas:  20% (214/1068)
Resolving deltas:  21% (225/1068)
Resolving deltas:  22% (235/1068)
Resolving deltas:  23% (246/1068)
Resolving deltas:  24% (257/1068)
Resolving deltas:  25% (267/1068)
Resolving deltas:  26% (278/1068)
Resolving deltas:  27% (289/1068)
Resolving deltas:  28% (300/1068)
Resolving deltas:  29% (310/1068)
Resolving deltas:  30% (321/1068)
Resolving deltas:  31% (332/1068)
Resolving deltas:  32% (342/1068)
Resolving deltas:  33% (353/1068)
Resolving deltas:  34% (364/1068)
Resolving deltas:  35% (374/1068)
Resolving deltas:  36% (385/1068)
Resolving deltas:  37% (396/1068)
Resolving deltas:  38% (406/1068)
Resolving deltas:  39% (417/1068)
Resolving deltas:  40% (428/1068)
Resolving deltas:  41% (438/1068)
Resolving deltas:  42% (449/1068)
Resolving deltas:  43% (460/1068)
Resolving deltas:  44% (470/1068)
Resolving deltas:  45% (481/1068)
Resolving deltas:  46% (492/1068)
Resolving deltas:  47% (502/1068)
Resolving deltas:  48% (513/1068)
Resolving deltas:  49% (524/1068)
Resolving deltas:  50% (534/1068)
Resolving deltas:  51% (545/1068)
Resolving deltas:  52% (556/1068)
Resolving deltas:  53% (567/1068)
Resolving deltas:  54% (577/1068)
Resolving deltas:  55% (588/1068)
Resolving deltas:  56% (599/1068)
Resolving deltas:  57% (609/1068)
Resolving deltas:  58% (620/1068)
Resolving deltas:  59% (631/1068)
Resolving deltas:  60% (641/1068)
Resolving deltas:  61% (652/1068)
Resolving deltas:  62% (663/1068)
Resolving deltas:  63% (673/1068)
Resolving deltas:  64% (684/1068)
Resolving deltas:  65% (695/1068)
Resolving deltas:  66% (705/1068)
Resolving deltas:  67% (716/1068)
Resolving deltas:  68% (727/1068)
Resolving deltas:  69% (737/1068)
Resolving deltas:  70% (748/1068)
Resolving deltas:  71% (759/1068)
Resolving deltas:  72% (769/1068)
Resolving deltas:  73% (780/1068)
Resolving deltas:  74% (791/1068)
Resolving deltas:  75% (801/1068)
Resolving deltas:  76% (812/1068)
Resolving deltas:  77% (823/1068)
Resolving deltas:  78% (834/1068)
Resolving deltas:  79% (844/1068)
Resolving deltas:  80% (855/1068)
Resolving deltas:  81% (866/1068)
Resolving deltas:  82% (876/1068)
Resolving deltas:  83% (887/1068)
Resolving deltas:  84% (898/1068)
Resolving deltas:  85% (908/1068)
Resolving deltas:  86% (919/1068)
Resolving deltas:  87% (930/1068)
Resolving deltas:  88% (940/1068)
Resolving deltas:  89% (951/1068)
Resolving deltas:  90% (962/1068)
Resolving deltas:  91% (972/1068)
Resolving deltas:  92% (983/1068)
Resolving deltas:  93% (994/1068)
Resolving deltas:  94% (1004/1068)
Resolving deltas:  95% (1015/1068)
Resolving deltas:  96% (1026/1068)
Resolving deltas:  97% (1036/1068)
Resolving deltas:  98% (1047/1068)
Resolving deltas:  99% (1058/1068)
Resolving deltas: 100% (1068/1068)
Resolving deltas: 100% (1068/1068), done.
Note: switching to 'e29b3779a677a3c82dd51cfe01cfed26b33476b4'.
You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.
If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:
  git switch -c &lt;new-branch-name>
Or undo this operation with:
  git switch -
Turn off this advice by setting config variable advice.detachedHead to false
HEAD is now at e29b377 Atualização de pacote para sitef-1 e sitef-3

== PIPELINE PREDEPLOY ==
TAG   : e29b3779a677a3c82dd51cfe01cfed26b33476b4
EXEC  : execution/machine_list_dev.yml
BRANCH: develop

PLAY [Predeploy a partir do arquivo de execução] *******************************
TASK [Gathering Facts] *********************************************************
ok: [localhost]
TASK [Resolver filestore_env e filestore_base_dir sem recursão] ****************
ok: [localhost]
TASK [Mostrar variáveis de entrada e resolvidas] *******************************
ok: [localhost] => {
    "msg": [
        "execution_file_name_resolved = execution/machine_list_dev.yml",
        "deployment_ref_resolved      = e29b3779a677a3c82dd51cfe01cfed26b33476b4",
        "repo_root_resolved           = /tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../status/e29b3779a677a3c82dd51cfe01cfed26b33476b4",
        "filestore_env_resolved       = dev",
        "filestore_base_dir_resolved  = dev/e29b3779a677a3c82dd51cfe01cfed26b33476b4",
        "nexus_base_url_resolved      = https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"
    ]
}
TASK [Criar diretório de status da TAG] ****************************************
changed: [localhost]
TASK [Carregar arquivo de execução] ********************************************
ok: [localhost]
TASK [Falhar se não tiver máquinas no arquivo de execução] *********************
skipping: [localhost]
TASK [Executar pré-deploy por máquina] *****************************************
included: /tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/predeploy_per_machine.yml for localhost => (item=sitef-1)
included: /tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/predeploy_per_machine.yml for localhost => (item=sitef-3)
TASK [Predeploy | Resolver nome da máquina atual] ******************************
ok: [localhost]
TASK [Predeploy | Validar vars mínimas] ****************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Predeploy | Definir paths base do repositório] ***************************
ok: [localhost]
TASK [Predeploy | Definir candidatos de arquivo da máquina] ********************
ok: [localhost]
TASK [Predeploy | Verificar candidatos] ****************************************
ok: [localhost] => (item=/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-1.yml)
ok: [localhost] => (item=/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/sitef-1.yml)
ok: [localhost] => (item=/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../machines/sitef-1.yml)
ok: [localhost] => (item=/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-1.yml)
TASK [Predeploy | Selecionar machine_file existente] ***************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-1.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-1.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/sitef-1.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/sitef-1.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../machines/sitef-1.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 311, 'inode': 6291671, 'dev': 64781, 'nlink': 1, 'atime': 1767702735.2955709, 'mtime': 1767702735.2815707, 'ctime': 1767702735.2815707, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': '0faf2311947eda6449d828abb09a2e27431cebd5', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '3691582101', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../machines/sitef-1.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../machines/sitef-1.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-1.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-1.yml', 'ansible_loop_var': 'item'}) 
TASK [Predeploy | Falhar se arquivo da máquina não existir] ********************
skipping: [localhost]
TASK [Predeploy | Carregar config da máquina sitef-1] **************************
ok: [localhost]
TASK [Predeploy | Validar package definido] ************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Predeploy | Carregar config do pacote sitef-8.0.1-1] *********************
ok: [localhost]
TASK [Predeploy | Validar components do pacote] ********************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Predeploy | Validar script do pacote] ************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Predeploy | Definir usuário alvo padrão] *********************************
ok: [localhost]
TASK [Predeploy | Definir ssh_common_args padrão] ******************************

ok: [localhost]
TASK [Predeploy | Aplicar ProxyJump via bastion (quando necessário)] ***********
skipping: [localhost]

TASK [Predeploy | Registrar host dinâmico para sitef-1] ************************
changed: [localhost]
TASK [Predeploy | Montar env final (package base + machine override)] **********
ok: [localhost]
TASK [Predeploy | Normalizações] ***********************************************
ok: [localhost]
TASK [Predeploy | Gerar run_id] ************************************************
ok: [localhost]
TASK [Predeploy | Definir arquivos/diretórios locais] **************************
ok: [localhost]
TASK [Predeploy | Definir paths lógicos (File Store) SEPARADOS POR STAGE] ******
ok: [localhost]
TASK [Predeploy | Garantir diretório de status local] **************************
changed: [localhost]
TASK [Predeploy | Garantir arquivo pipeline.log (cumulativo local)] ************
changed: [localhost]

TASK [Predeploy | Verificar se status.json já existe] **************************
ok: [localhost]
TASK [Predeploy | Ler status.json existente (se existir)] **********************
skipping: [localhost]
TASK [Predeploy | Parse do status existente (ou base vazio)] *******************
ok: [localhost]
TASK [Predeploy | Escrever status inicial (predeploy:queued) com history append] ***
changed: [localhost]
TASK [Predeploy | Definir diretórios remotos do pipeline] **********************
ok: [localhost]
TASK [Predeploy | Garantir base do pipeline no host] ***************************
ok: [localhost -> sitef-1(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/components)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package)
TASK [Predeploy | Limpar pasta de scripts do pacote] ***************************
changed: [localhost -> sitef-1(100.99.25.45)]
TASK [Predeploy | Recriar pasta de scripts do pacote] **************************
changed: [localhost -> sitef-1(100.99.25.45)]
TASK [Predeploy | Copiar package.yml para o host] ******************************
changed: [localhost -> sitef-1(100.99.25.45)]
TASK [Predeploy | Garantir diretórios dos componentes no host] *****************
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/sitef-core-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/sitefservice-7.0.0-1.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/sitef-voltage-7.0.0-3.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/basemultibandeira-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/comuniccnf-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/gerpdv-8.0.1-14.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/drvcom-8.0.1-5.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/infrapdv-8.0.3-3.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/infrahsm-8.0.2-8.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/nservices-8.0.1-7.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/bin-8.0.11-5.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/cardse-8.0.3-26.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/cardse-bin-1.0.3-115.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/servconfig-8.0.0-37.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/atualiza-prods-1.0.2-406.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/omni-data-agent-0.1.0-1.el8.el8.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/sitefsql-5.4.0.14-x86_64.zip)
TASK [Predeploy | Baixar componentes do Nexus] *********************************
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/sitef-core-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/sitefservice-7.0.0-1.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/sitef-voltage-7.0.0-3.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/basemultibandeira-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/comuniccnf-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/gerpdv-8.0.1-14.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/drvcom-8.0.1-5.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/infrapdv-8.0.3-3.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/infrahsm-8.0.2-8.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/nservices-8.0.1-7.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/bin-8.0.11-5.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/cardse-8.0.3-26.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/cardse-bin-1.0.3-115.x86_64.rpm)
ok: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/servconfig-8.0.0-37.x86_64.rpm)
changed: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/atualiza-prods-1.0.2-406.x86_64.rpm)
changed: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/omni-data-agent-0.1.0-1.el8.el8.x86_64.rpm)
changed: [localhost -> sitef-1(100.99.25.45)] => (item=packages/linux/sitefsql-5.4.0.14-x86_64.zip)
TASK [Predeploy | Copiar scripts do pacote para o host] ************************
changed: [localhost -> sitef-1(100.99.25.45)]
TASK [Predeploy | Executar init_parallel.sh no host (com log + stdout)] ********
changed: [localhost -> sitef-1(100.99.25.45)]
TASK [Predeploy | Tail do init_parallel.log (sempre)] **************************
ok: [localhost -> sitef-1(100.99.25.45)]
TASK [Predeploy | Montar conteúdo do log (bloco predeploy)] ********************
ok: [localhost]
TASK [Predeploy | Append do bloco no pipeline.log (controller)] ****************
changed: [localhost]
TASK [Predeploy | Carregar conteúdo completo do pipeline.log (controller)] *****
ok: [localhost]

TASK [Predeploy | Ler status.json atual] ***************************************
ok: [localhost]
TASK [Predeploy | Parse do status atual] ***************************************
ok: [localhost]
TASK [Predeploy | Atualizar status final (append em history)] ******************
changed: [localhost]
TASK [Predeploy | Definir status_tag_value (tag usada no File Store)] **********
ok: [localhost]
TASK [Predeploy | Upload JSON + LOG para Harness File Store (sempre)] **********
included: /tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir defaults + normalizações] ******************************
ok: [localhost]
TASK [Harness | Definir stage_label] *******************************************
ok: [localhost]
TASK [Harness | Definir machine_stage_name] ************************************
ok: [localhost]
TASK [Harness | Resolver token (vars > env)] ***********************************
ok: [localhost]
TASK [Harness | Falhar se token vazio] *****************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir http_codes aceitos] ************************************
ok: [localhost]
TASK [Harness | Montar tags base] **********************************************
ok: [localhost]
TASK [Harness | Adicionar extra_tags como tag=...] *****************************
skipping: [localhost]
TASK [Harness | Render tags JSON] **********************************************
ok: [localhost]
TASK [Harness | Identifiers (RAW)] *********************************************
ok: [localhost]
TASK [Harness | Identifiers (FINAL a partir do RAW)] ***************************
ok: [localhost]
TASK [Harness | Limitar leaf_identifier a 128 (se precisar)] *******************
ok: [localhost]
TASK [Harness | Limitar leaf_identifier a 128 (se precisar)] *******************
ok: [localhost]
TASK [Harness | Garantir folder ENV (POST)] ************************************
ok: [localhost]
TASK [Harness | Ler body ENV] **************************************************
ok: [localhost]
TASK [Harness | Marcar ENV duplicate?] *****************************************
ok: [localhost]
TASK [Harness | Falhar ENV se não OK] ******************************************
skipping: [localhost]
TASK [Harness | Garantir folder GIT_TAG (POST)] ********************************
changed: [localhost]
TASK [Harness | Ler body GIT_TAG] **********************************************
ok: [localhost]
TASK [Harness | Marcar GIT_TAG duplicate?] *************************************
ok: [localhost]
TASK [Harness | Falhar GIT_TAG se não OK] **************************************
skipping: [localhost]

TASK [Harness | Garantir folder LEAF (POST)] ***********************************
changed: [localhost]
TASK [Harness | Ler body LEAF] *************************************************
ok: [localhost]
TASK [Harness | Marcar LEAF duplicate?] ****************************************
ok: [localhost]
TASK [Harness | Falhar LEAF se não OK] *****************************************
skipping: [localhost]
TASK [Harness | Definir identifiers do STATUS] *********************************
ok: [localhost]
TASK [Harness | Limitar identifier STATUS a 128] *******************************
ok: [localhost]
TASK [Harness | CREATE status.json] ********************************************
changed: [localhost]
TASK [Harness | Ler body status CREATE] ****************************************
ok: [localhost]
TASK [Harness | UPDATE status.json (se já existe)] *****************************
skipping: [localhost]
TASK [Harness | Falhar status se create e update não deram certo] **************
skipping: [localhost]
TASK [Harness | Definir identifiers do LOG] ************************************
ok: [localhost]

TASK [Harness | Limitar identifier LOG a 128] **********************************
ok: [localhost]
TASK [Harness | Criar arquivo temporário do log no controller] *****************
changed: [localhost]
TASK [Harness | CREATE pipeline.log] *******************************************
changed: [localhost]
TASK [Harness | Ler body log CREATE] *******************************************
ok: [localhost]
TASK [Harness | UPDATE pipeline.log (se já existe)] ****************************
skipping: [localhost]
TASK [Harness | Falhar LOG se create e update não deram certo] *****************
skipping: [localhost]
TASK [Harness | Limpar arquivo temporário do log] ******************************
ok: [localhost]
TASK [Predeploy | Falhar se init_parallel.sh retornou erro (depois do upload)] ***
skipping: [localhost]
TASK [Predeploy | Resolver nome da máquina atual] ******************************
ok: [localhost]
TASK [Predeploy | Validar vars mínimas] ****************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Predeploy | Definir paths base do repositório] ***************************
ok: [localhost]
TASK [Predeploy | Definir candidatos de arquivo da máquina] ********************
ok: [localhost]
TASK [Predeploy | Verificar candidatos] ****************************************
ok: [localhost] => (item=/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-3.yml)
ok: [localhost] => (item=/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/sitef-3.yml)
ok: [localhost] => (item=/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../machines/sitef-3.yml)
ok: [localhost] => (item=/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-3.yml)
TASK [Predeploy | Selecionar machine_file existente] ***************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-3.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-3.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/sitef-3.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../execution/sitef-3.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../machines/sitef-3.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 311, 'inode': 6291673, 'dev': 64781, 'nlink': 1, 'atime': 1767702735.2965708, 'mtime': 1767702735.2815707, 'ctime': 1767702735.2815707, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': 'c0c8162b3e0e60067b29ca1c77492c66f57ba295', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '3536383381', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../machines/sitef-3.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../machines/sitef-3.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-3.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-3.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost]
TASK [Predeploy | Falhar se arquivo da máquina não existir] ********************
skipping: [localhost]
TASK [Predeploy | Carregar config da máquina sitef-3] **************************
ok: [localhost]
TASK [Predeploy | Validar package definido] ************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Predeploy | Carregar config do pacote sitef-8.0.1-1] *********************
ok: [localhost]
TASK [Predeploy | Validar components do pacote] ********************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Predeploy | Validar script do pacote] ************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Predeploy | Definir usuário alvo padrão] *********************************
ok: [localhost]
TASK [Predeploy | Definir ssh_common_args padrão] ******************************
ok: [localhost]
TASK [Predeploy | Aplicar ProxyJump via bastion (quando necessário)] ***********
skipping: [localhost]
TASK [Predeploy | Registrar host dinâmico para sitef-3] ************************
changed: [localhost]
TASK [Predeploy | Montar env final (package base + machine override)] **********
ok: [localhost]
TASK [Predeploy | Normalizações] ***********************************************
ok: [localhost]
TASK [Predeploy | Gerar run_id] ************************************************
ok: [localhost]
TASK [Predeploy | Definir arquivos/diretórios locais] **************************
ok: [localhost]
TASK [Predeploy | Definir paths lógicos (File Store) SEPARADOS POR STAGE] ******
ok: [localhost]
TASK [Predeploy | Garantir diretório de status local] **************************
changed: [localhost]
TASK [Predeploy | Garantir arquivo pipeline.log (cumulativo local)] ************
changed: [localhost]
TASK [Predeploy | Verificar se status.json já existe] **************************
ok: [localhost]
TASK [Predeploy | Ler status.json existente (se existir)] **********************
skipping: [localhost]
TASK [Predeploy | Parse do status existente (ou base vazio)] *******************
ok: [localhost]
TASK [Predeploy | Escrever status inicial (predeploy:queued) com history append] ***
changed: [localhost]
TASK [Predeploy | Definir diretórios remotos do pipeline] **********************
ok: [localhost]
TASK [Predeploy | Garantir base do pipeline no host] ***************************
ok: [localhost -> sitef-3(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/components)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package)
TASK [Predeploy | Limpar pasta de scripts do pacote] ***************************
changed: [localhost -> sitef-3(100.99.25.45)]
TASK [Predeploy | Recriar pasta de scripts do pacote] **************************
changed: [localhost -> sitef-3(100.99.25.45)]
TASK [Predeploy | Copiar package.yml para o host] ******************************
changed: [localhost -> sitef-3(100.99.25.45)]
TASK [Predeploy | Garantir diretórios dos componentes no host] *****************
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/sitef-core-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/sitefservice-7.0.0-1.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/sitef-voltage-7.0.0-3.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/basemultibandeira-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/comuniccnf-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/gerpdv-8.0.1-14.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/drvcom-8.0.1-5.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/infrapdv-8.0.3-3.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/infrahsm-8.0.2-8.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/nservices-8.0.1-7.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/bin-8.0.11-5.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/cardse-8.0.3-26.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/cardse-bin-1.0.3-115.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/servconfig-8.0.0-37.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/atualiza-prods-1.0.2-406.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/omni-data-agent-0.1.0-1.el8.el8.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/sitefsql-5.4.0.14-x86_64.zip)
TASK [Predeploy | Baixar componentes do Nexus] *********************************
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/sitef-core-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/sitefservice-7.0.0-1.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/sitef-voltage-7.0.0-3.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/basemultibandeira-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/comuniccnf-8.0.0-0.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/gerpdv-8.0.1-14.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/drvcom-8.0.1-5.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/infrapdv-8.0.3-3.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/infrahsm-8.0.2-8.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/nservices-8.0.1-7.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/bin-8.0.11-5.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/cardse-8.0.3-26.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/cardse-bin-1.0.3-115.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/servconfig-8.0.0-37.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/atualiza-prods-1.0.2-406.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/omni-data-agent-0.1.0-1.el8.el8.x86_64.rpm)
ok: [localhost -> sitef-3(100.99.25.45)] => (item=packages/linux/sitefsql-5.4.0.14-x86_64.zip)
TASK [Predeploy | Copiar scripts do pacote para o host] ************************
ok: [localhost -> sitef-3(100.99.25.45)]
TASK [Predeploy | Executar init_parallel.sh no host (com log + stdout)] ********
changed: [localhost -> sitef-3(100.99.25.45)]
TASK [Predeploy | Tail do init_parallel.log (sempre)] **************************
ok: [localhost -> sitef-3(100.99.25.45)]
TASK [Predeploy | Montar conteúdo do log (bloco predeploy)] ********************
ok: [localhost]
TASK [Predeploy | Append do bloco no pipeline.log (controller)] ****************
changed: [localhost]
TASK [Predeploy | Carregar conteúdo completo do pipeline.log (controller)] *****
ok: [localhost]
TASK [Predeploy | Ler status.json atual] ***************************************
ok: [localhost]

TASK [Predeploy | Parse do status atual] ***************************************
ok: [localhost]
TASK [Predeploy | Atualizar status final (append em history)] ******************
changed: [localhost]
TASK [Predeploy | Definir status_tag_value (tag usada no File Store)] **********
ok: [localhost]
TASK [Predeploy | Upload JSON + LOG para Harness File Store (sempre)] **********
included: /tmp/tmp.ekQ9MCDbN3/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir defaults + normalizações] ******************************
ok: [localhost]
TASK [Harness | Definir stage_label] *******************************************
ok: [localhost]

TASK [Harness | Definir machine_stage_name] ************************************
ok: [localhost]
TASK [Harness | Resolver token (vars > env)] ***********************************
ok: [localhost]
TASK [Harness | Falhar se token vazio] *****************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir http_codes aceitos] ************************************
ok: [localhost]
TASK [Harness | Montar tags base] **********************************************
ok: [localhost]
TASK [Harness | Adicionar extra_tags como tag=...] *****************************
skipping: [localhost]
TASK [Harness | Render tags JSON] **********************************************
ok: [localhost]
TASK [Harness | Identifiers (RAW)] *********************************************
ok: [localhost]
TASK [Harness | Identifiers (FINAL a partir do RAW)] ***************************
ok: [localhost]
TASK [Harness | Limitar leaf_identifier a 128 (se precisar)] *******************
ok: [localhost]
TASK [Harness | Limitar leaf_identifier a 128 (se precisar)] *******************
ok: [localhost]
TASK [Harness | Garantir folder ENV (POST)] ************************************
ok: [localhost]
TASK [Harness | Ler body ENV] **************************************************
ok: [localhost]
TASK [Harness | Marcar ENV duplicate?] *****************************************
ok: [localhost]
TASK [Harness | Falhar ENV se não OK] ******************************************
skipping: [localhost]
TASK [Harness | Garantir folder GIT_TAG (POST)] ********************************
ok: [localhost]
TASK [Harness | Ler body GIT_TAG] **********************************************
ok: [localhost]
TASK [Harness | Marcar GIT_TAG duplicate?] *************************************
ok: [localhost]
TASK [Harness | Falhar GIT_TAG se não OK] **************************************
skipping: [localhost]
TASK [Harness | Garantir folder LEAF (POST)] ***********************************
changed: [localhost]
TASK [Harness | Ler body LEAF] *************************************************
ok: [localhost]
TASK [Harness | Marcar LEAF duplicate?] ****************************************
ok: [localhost]
TASK [Harness | Falhar LEAF se não OK] *****************************************
skipping: [localhost]
TASK [Harness | Definir identifiers do STATUS] *********************************
ok: [localhost]
TASK [Harness | Limitar identifier STATUS a 128] *******************************
ok: [localhost]
TASK [Harness | CREATE status.json] ********************************************
changed: [localhost]
TASK [Harness | Ler body status CREATE] ****************************************
ok: [localhost]
TASK [Harness | UPDATE status.json (se já existe)] *****************************
skipping: [localhost]
TASK [Harness | Falhar status se create e update não deram certo] **************
skipping: [localhost]
TASK [Harness | Definir identifiers do LOG] ************************************
ok: [localhost]
TASK [Harness | Limitar identifier LOG a 128] **********************************
ok: [localhost]
TASK [Harness | Criar arquivo temporário do log no controller] *****************
changed: [localhost]
TASK [Harness | CREATE pipeline.log] *******************************************
changed: [localhost]
TASK [Harness | Ler body log CREATE] *******************************************
ok: [localhost]
TASK [Harness | UPDATE pipeline.log (se já existe)] ****************************
skipping: [localhost]
TASK [Harness | Falhar LOG se create e update não deram certo] *****************
skipping: [localhost]
TASK [Harness | Limpar arquivo temporário do log] ******************************
ok: [localhost]
TASK [Predeploy | Falhar se init_parallel.sh retornou erro (depois do upload)] ***
skipping: [localhost]
PLAY RECAP *********************************************************************
localhost                  : ok=154  changed=32   unreachable=0    failed=0    skipped=26   rescued=0    ignored=0   
Command finished with status SUCCESS
