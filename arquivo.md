# predeploy_per_machine.yml
---
# =====================================================================================
# PREDEPLOY POR MÁQUINA (COMPLETO / CORRIGIDO)
# Chamado por: predeploy_from_execution.yml
# =====================================================================================

# -----------------------------------------------------------------------------
# 0) Resolver nome da máquina atual + stage + RESET (evitar vazamento entre loops)
# -----------------------------------------------------------------------------
- name: "Predeploy | Resolver nome da máquina atual + reset vars"
  ansible.builtin.set_fact:
    stage_name: "predeploy"
    current_machine: "{{ (machine_name | default(item) | default('')) | string | trim }}"

    # RESET por máquina (IMPORTANTE!)
    machine_file: ""
    package_file: ""

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
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"
    scripts_root_dir: "{{ repo_root_safe }}/scripts"

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

- name: "Predeploy | Verificar candidatos (machine_file)"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

- name: "Predeploy | Selecionar machine_file existente"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - (machine_file | default('') | string | length) == 0
  loop: "{{ machine_candidates_stat.results }}"

- name: "Predeploy | Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: |
      [predeploy] Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: (machine_file | default('') | string | length) == 0

# -----------------------------------------------------------------------------
# 3) Carregar machine_cfg
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

# -----------------------------------------------------------------------------
# 3.1) Resolver package_file (suporta .yml/.yaml e estrutura em pasta)
# -----------------------------------------------------------------------------
- name: "Predeploy | Definir candidatos de arquivo do pacote"
  ansible.builtin.set_fact:
    package_name: "{{ machine_cfg.package | string | trim }}"
    candidate_package_files:
      - "{{ packages_dir }}/{{ package_name }}.yml"
      - "{{ packages_dir }}/{{ package_name }}.yaml"
      - "{{ packages_dir }}/{{ package_name }}/package.yml"
      - "{{ packages_dir }}/{{ package_name }}/package.yaml"

- name: "Predeploy | Verificar candidatos (package_file)"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_package_files }}"
  register: package_candidates_stat

- name: "Predeploy | Selecionar package_file existente"
  ansible.builtin.set_fact:
    package_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - (package_file | default('') | string | length) == 0
  loop: "{{ package_candidates_stat.results }}"

- name: "Predeploy | Listar packages disponíveis (debug) se package_file não encontrado"
  ansible.builtin.find:
    paths: "{{ packages_dir }}"
    file_type: file
    patterns:
      - "*.yml"
      - "*.yaml"
  register: packages_found
  when: (package_file | default('') | string | length) == 0

- name: "Predeploy | Falhar se arquivo do pacote não existir"
  ansible.builtin.fail:
    msg: |
      [predeploy] Package file não encontrado para package="{{ package_name }}" (machine={{ current_machine }}).
      Caminhos testados:
      {{ candidate_package_files | to_nice_yaml }}

      Packages disponíveis em {{ packages_dir }}:
      {{
        (packages_found.files | default([]) | map(attribute='path') | list) | to_nice_yaml
      }}

      ✅ Ajuste esperado:
      - OU criar/commitar o arquivo do pacote com esse nome na TAG {{ deployment_ref }}
      - OU corrigir machine_cfg.package em {{ machine_file }} para um package que exista em /packages
  when: (package_file | default('') | string | length) == 0

# -----------------------------------------------------------------------------
# 3.2) Carregar package_cfg
# -----------------------------------------------------------------------------
- name: "Predeploy | Carregar config do pacote ({{ package_name }})"
  ansible.builtin.include_vars:
    file: "{{ package_file }}"
    name: package_cfg

- name: "Predeploy | Validar components do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      - package_cfg.components is defined
      - (package_cfg.components | length) > 0
    fail_msg: "components ausente/vazio no pacote {{ package_name }} (arquivo {{ package_file }})"

- name: "Predeploy | Validar script do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg.script is defined
      - (package_cfg.script | string | trim | length) > 0
    fail_msg: "package_cfg.script ausente/vazio no pacote {{ package_name }} (arquivo {{ package_file }})"

# -----------------------------------------------------------------------------
# 4) SSH e host dinâmico
# -----------------------------------------------------------------------------
- name: "Predeploy | Resolver host alvo"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default(machine_cfg.target_user | default('root')) }}"
    target_host: "{{ machine_cfg.host | default(machine_cfg.ip | default('')) | string | trim }}"
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

- name: "Predeploy | Falhar se host/ip não informado"
  ansible.builtin.assert:
    that:
      - (target_host | length) > 0
    fail_msg: "machine_cfg.host/ip vazio em {{ machine_file }}"

- name: "Predeploy | Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | trim | length) > 0

- name: "Predeploy | Registrar host dinâmico para {{ current_machine }}"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ target_host }}"
    ansible_user: "{{ target_user }}"
    ansible_port: "{{ machine_cfg.port | default(22) }}"
    ansible_ssh_private_key_file: "{{ machine_cfg.ssh_key | default(omit) }}"
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
            'SITEF_HOST': target_host,
            'DEPLOYMENT_REF': (deployment_ref | default('')),
            'PACKAGE_NAME': (package_name | default('')),
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
# 7) Paths local (controller) + paths filestore
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
# 8) Status.json com history - queued
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
              "host": target_host,
              "package": package_name,
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
    remote_init_log: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_parallel_{{ run_id }}.log"

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
- name: "Predeploy | Copiar package file para o host"
  become: true
  ansible.builtin.copy:
    src: "{{ package_file }}"
    dest: "{{ deploy_scripts_package_dir }}/{{ package_name }}.yml"
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
    url_username: "{{ (nexus_user | default('') | string | trim) if ((nexus_user | default('') | string | trim) | length > 0) else omit }}"
    url_password: "{{ (nexus_password | default('') | string | trim) if ((nexus_password | default('') | string | trim) | length > 0) else omit }}"
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
# 14) Executar init_parallel.sh na máquina alvo (log por execução, SEM -a)
# -----------------------------------------------------------------------------
- name: "Predeploy | Zerar log remoto desta execução"
  become: true
  ansible.builtin.shell: |
    : > "{{ remote_init_log }}"
  args:
    executable: /bin/bash
  delegate_to: "{{ current_machine }}"
  changed_when: true

- name: "Predeploy | Executar init_parallel.sh no host (com tee sem -a)"
  become: true
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ deploy_scripts_dir }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_parallel.sh 2>&1 | tee "{{ remote_init_log }}"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  delegate_to: "{{ current_machine }}"
  register: init_parallel_result
  ignore_errors: true
  changed_when: true

- name: "Predeploy | Tail do log remoto desta execução (sempre)"
  become: true
  ansible.builtin.shell: |
    echo "===== tail -n 800 {{ remote_init_log }} ====="
    tail -n 800 "{{ remote_init_log }}" 2>/dev/null || echo "log não encontrado"
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
      host={{ target_host }}
      package={{ package_name }}
      stage={{ stage_name }}
      rc={{ init_parallel_result.rc | default(1) }}
      ts={{ ansible_date_time.iso8601 }}
      remote_log={{ remote_init_log }}

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

# ✅ FileStore: enviar só o log desta execução (não o cumulativo)
- name: "Predeploy | Definir log_content_to_upload (somente esta execução)"
  ansible.builtin.set_fact:
    log_content_to_upload: "{{ predeploy_log_block }}"

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
    log_content: "{{ log_content_to_upload }}"

# -----------------------------------------------------------------------------
# 18) Falhar pipeline se predeploy deu erro (DEPOIS do upload)
# -----------------------------------------------------------------------------
- name: "Predeploy | Falhar se init_parallel.sh retornou erro (depois do upload)"
  ansible.builtin.fail:
    msg: "PREDEPLOY falhou em {{ current_machine }} (rc={{ init_parallel_result.rc | default(1) }})"
  when: (init_parallel_result.rc | default(1)) != 0
