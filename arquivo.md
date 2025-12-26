uma coisa que percebi o deploy nao mostra o log depois de executar o script para eu conseguir ver na pipeline do harness tb qual o log

---
# =====================================================================================
# DEPLOY POR MÁQUINA (PIPELINE 2)
# =====================================================================================

- name: "Deploy | Definir stage"
  ansible.builtin.set_fact:
    stage_name: "deploy"
    hf_stage_task: "{{ hf_stage_task | default('deploy') }}"

- name: "Deploy | Resolver run_id (para dedupe de append)"
  ansible.builtin.set_fact:
    run_id: >-
      {{
        (lookup('ansible.builtin.env','HARNESS_EXECUTION_ID') | default('', true) | string | trim)
        if (lookup('ansible.builtin.env','HARNESS_EXECUTION_ID') | default('', true) | string | trim) | length > 0
        else
          (
            (lookup('ansible.builtin.env','BUILD_ID') | default('', true) | string | trim)
            if (lookup('ansible.builtin.env','BUILD_ID') | default('', true) | string | trim) | length > 0
            else
              (
                (lookup('ansible.builtin.env','CI_PIPELINE_ID') | default('', true) | string | trim)
                if (lookup('ansible.builtin.env','CI_PIPELINE_ID') | default('', true) | string | trim) | length > 0
                else
                  (
                    (lookup('ansible.builtin.env','GITHUB_RUN_ID') | default('', true) | string | trim)
                    if (lookup('ansible.builtin.env','GITHUB_RUN_ID') | default('', true) | string | trim) | length > 0
                    else ansible_date_time.iso8601
                  )
              )
          )
      }}

- name: "Deploy | Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    repo_root_safe: "{{ repo_root | default(repo_root_resolved | default(playbook_dir ~ '/..')) }}"
    current_machine: "{{ (machine_name | string | trim) }}"
    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    host_deploy_init_log: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/init_deploy.log"

- name: "Deploy | Definir diretórios principais do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"

# -----------------------------------------------------------------------------
# Resolver machine_file
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
    msg: |
      [deploy] Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# Carregar configs
# -----------------------------------------------------------------------------
- name: "Deploy | Carregar config da máquina {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Deploy | Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg.package is defined
      - (machine_cfg.package | string | trim) | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

- name: "Deploy | Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# SSH + host dinâmico
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

- name: "Deploy | Resolver host alvo (host/ip)"
  ansible.builtin.set_fact:
    target_host: "{{ machine_cfg.host | default(machine_cfg.ip | default('')) | string | trim }}"

- name: "Deploy | Falhar se host/ip não informado"
  ansible.builtin.assert:
    that:
      - (target_host | length) > 0
    fail_msg: "machine_cfg.host/ip vazio em {{ machine_file }}"

- name: "Deploy | Registrar host dinâmico"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ target_host }}"
    ansible_user: "{{ target_user }}"
    ansible_port: "{{ machine_cfg.port | default(22) }}"
    ansible_ssh_private_key_file: "{{ machine_cfg.ssh_key | default(omit) }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"
  changed_when: true

# -----------------------------------------------------------------------------
# ENV final
# -----------------------------------------------------------------------------
- name: "Deploy | Montar env final"
  ansible.builtin.set_fact:
    effective_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': current_machine,
            'SITEF_HOST': target_host,
            'DEPLOYMENT_REF': (deployment_ref | default('')),
            'PACKAGE_NAME': (machine_cfg.package | default('')),
            'ROLLBACK_PACKAGE': (machine_cfg.rollback | default(''))
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# Status + paths filestore
# -----------------------------------------------------------------------------
- name: "Deploy | Definir deployment_ref_lower"
  ansible.builtin.set_fact:
    deployment_ref_lower: "{{ deployment_ref | lower }}"

- name: "Deploy | Definir arquivos de status/log local"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    status_file: "{{ status_dir }}/{{ current_machine }}/status.json"
    pipeline_log_file: "{{ status_dir }}/{{ current_machine }}/pipeline.log"
    filestore_json_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}-{{ stage_name }}.json"
    filestore_log_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}-{{ stage_name }}.log"

- name: "Deploy | Garantir diretório de status da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

# -----------------------------------------------------------------------------
# Ler status existente
# -----------------------------------------------------------------------------
- name: "Deploy | Verificar se status.json já existe (para append)"
  ansible.builtin.stat:
    path: "{{ status_file }}"
  register: status_stat

- name: "Deploy | Ler status.json existente (se existir)"
  ansible.builtin.slurp:
    path: "{{ status_file }}"
  register: status_slurp
  when: status_stat.stat.exists

- name: "Deploy | Parse do status existente (ou base vazio)"
  ansible.builtin.set_fact:
    status_obj: >-
      {{
        (status_slurp.content | b64decode | from_json)
          if (status_stat.stat.exists | default(false))
          else {}
      }}

# -----------------------------------------------------------------------------
# DEDUPE
# -----------------------------------------------------------------------------
- name: "Deploy | Resolver last_upload_run_id do stage (para dedupe)"
  ansible.builtin.set_fact:
    last_upload_run_id: >-
      {{
        (
          status_obj.last_upload[stage_name].run_id
          if (status_obj.last_upload is defined and status_obj.last_upload[stage_name] is defined and status_obj.last_upload[stage_name].run_id is defined)
          else ''
        ) | string
      }}

- name: "Deploy | Calcular should_append_log"
  ansible.builtin.set_fact:
    should_append_log: "{{ (last_upload_run_id | default('') | string | trim) != (run_id | string | trim) }}"

# -----------------------------------------------------------------------------
# status queued
# -----------------------------------------------------------------------------
- name: "Deploy | Atualizar status (deploy:queued) com append em history"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: >-
      {{
        (
          status_obj
          | combine({
              "machine": current_machine,
              "host": target_host,
              "package": (machine_cfg.package | default('')),
              "rollback": (machine_cfg.rollback | default('')),
              "deployment_ref": (deployment_ref | default('')),
              "stage": stage_name,
              "log_path": filestore_log_path,
              "status": "deploy:queued",
              "timestamp": ansible_date_time.iso8601,
              "run_id": run_id,
              "history": (
                (status_obj.history | default([]))
                + [ {
                      "stage": stage_name,
                      "status": "deploy:queued",
                      "run_id": run_id,
                      "timestamp": ansible_date_time.iso8601
                    } ]
              )
            }, recursive=True)
        ) | to_nice_json
      }}
  changed_when: true

# -----------------------------------------------------------------------------
# Executar deploy
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
# Montar log padronizado
# -----------------------------------------------------------------------------
- name: "Deploy | Montar conteúdo do log do DEPLOY"
  ansible.builtin.set_fact:
    deploy_log_content: |
      ---- pipeline {{ stage_name }} ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ target_host }}
      stage={{ stage_name }}
      run_id={{ run_id }}
      rc={{ deploy_result.rc | default('') }}
      ts={{ ansible_date_time.iso8601 }}

      ---- stdout (ansible) ----
      {{ deploy_result.stdout | default('') }}

      ---- stderr (ansible) ----
      {{ deploy_result.stderr | default('') }}

      ---- init_deploy.log (tail) ----
      {{ deploy_init_log_tail.stdout | default('') }}

      ---- deploy.txt ----
      {{ deploy_txt.stdout | default('') }}
      ---- pipeline {{ stage_name }} ----

# -----------------------------------------------------------------------------
# status final
# -----------------------------------------------------------------------------
- name: "Deploy | Ler status.json atual (antes do append final)"
  ansible.builtin.slurp:
    path: "{{ status_file }}"
  register: status_after_queued_slurp

- name: "Deploy | Parse do status atual"
  ansible.builtin.set_fact:
    status_now: "{{ status_after_queued_slurp.content | b64decode | from_json }}"

- name: "Deploy | Atualizar status final (append em history)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: >-
      {{
        (
          status_now
          | combine({
              "status": ("deploy:ok" if (deploy_result.rc | default(1)) == 0 else "deploy:error"),
              "timestamp": ansible_date_time.iso8601,
              "rc": (deploy_result.rc | default(1)),
              "history": (
                (status_now.history | default([]))
                + [ {
                      "stage": stage_name,
                      "status": ("deploy:ok" if (deploy_result.rc | default(1)) == 0 else "deploy:error"),
                      "rc": (deploy_result.rc | default(1)),
                      "run_id": run_id,
                      "timestamp": ansible_date_time.iso8601
                    } ]
              )
            }, recursive=True)
        ) | to_nice_json
      }}
  changed_when: true

# -----------------------------------------------------------------------------
# pipeline.log cumulativo + upload content
# -----------------------------------------------------------------------------
- name: "Deploy | Garantir arquivo de log cumulativo"
  ansible.builtin.file:
    path: "{{ pipeline_log_file }}"
    state: touch
    mode: "0644"

- name: "Deploy | Append do bloco de log do deploy no log cumulativo (dedupe por run_id)"
  ansible.builtin.blockinfile:
    path: "{{ pipeline_log_file }}"
    marker: ""
    insertafter: EOF
    block: |
      {{ deploy_log_content }}
  when: should_append_log | bool
  changed_when: true

- name: "Deploy | Carregar conteúdo completo do log cumulativo (para upload)"
  ansible.builtin.set_fact:
    log_content_to_upload: "{{ lookup('ansible.builtin.file', pipeline_log_file) }}"

# -----------------------------------------------------------------------------
# persistir last_upload
# -----------------------------------------------------------------------------
- name: "Deploy | Ler status.json atual (antes de salvar last_upload)"
  ansible.builtin.slurp:
    path: "{{ status_file }}"
  register: status_before_lastupload_slurp

- name: "Deploy | Parse do status (antes de salvar last_upload)"
  ansible.builtin.set_fact:
    status_before_lastupload: "{{ status_before_lastupload_slurp.content | b64decode | from_json }}"

- name: "Deploy | Atualizar last_upload.<stage>.run_id"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    mode: "0644"
    content: >-
      {{
        (
          status_before_lastupload
          | combine({
              "last_upload": (
                (status_before_lastupload.last_upload | default({}))
                | combine({
                    stage_name: {
                      "run_id": run_id,
                      "timestamp": ansible_date_time.iso8601
                    }
                  }, recursive=True)
              )
            }, recursive=True)
        ) | to_nice_json
      }}
  changed_when: true

# -----------------------------------------------------------------------------
# Upload (SEMPRE) usando seu harness_filestore_upload.yml
# -----------------------------------------------------------------------------
- name: "Deploy | Upload JSON + LOG para Harness File Store"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    # nunca passe current_machine: "{{ current_machine }}"
    current_machine: "{{ machine_name | string | trim }}"

    machine_status_file: "{{ status_file }}"
    log_content: "{{ log_content_to_upload }}"

    # nunca passe stage_name: "{{ stage_name }}"
    stage_name: "deploy"

    # não referencie stage_name aqui também
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':deploy:' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}

    extra_tags: []



# -----------------------------------------------------------------------------
# Falhar pipeline só DEPOIS do upload
# -----------------------------------------------------------------------------
- name: "Deploy | Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ current_machine }} (host {{ target_host }})"
  when: (deploy_result.rc | default(1)) != 0

