## ansible-harness

---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina)
#
# Objetivo:
# - Garante a árvore: Root -> <env> -> <deployment_ref>
# - Faz UPSERT (POST, se existir faz PUT) de:
#    - status JSON (opcionalmente por stage)
#    - log (por stage)  => evita sobrescrever/“apagar” predeploy quando o deploy roda
#
# Segredo (NÃO NO REPO):
#   HARNESS_X_API_KEY (env var)  OU var harness_x_api_key
#
# Notas:
# - Harness: folder/arquivo já existe pode vir como 400 + code=DUPLICATE_FIELD.
#   -> normaliza 400+DUPLICATE_FIELD para 409 (idempotência).
# - Retry em 500 (instabilidade ocasional do endpoint).
# - Blindagem contra current_machine recursivo: resolve via env MACHINE/MACHINES.
#
# Requisitos (vars):
#   deployment_ref          (ex: DEV000003)
#   filestore_env           (ex: dev)
#   machine_status_file     (path local do controller para status.json)
#   log_content             (string do log a ser enviado)
#   status_tag_value        (ex: dev000003:deploy:ok)
#
# Opcionais:
#   stage_name              (ex: predeploy | deploy)   [default: "unknown"]
#   deployment_ref_folder   (nome amigável da pasta deployment) [default: deployment_ref]
#   harness_account_id
#   harness_org_id
#   harness_project_id
#   harness_filestore_root_identifier  [default: Root]
#   extra_tags (lista de strings -> vira várias tags key=tag)
# =====================================================================================

# -----------------------------------------------------------------------------
# 0) Blindagem contra current_machine recursivo
# -----------------------------------------------------------------------------
- name: "Harness | Ler envs (MACHINE/MACHINES) e normalizar"
  ansible.builtin.set_fact:
    _env_MACHINE: "{{ lookup('ansible.builtin.env', 'MACHINE') | default('', true) | string | trim }}"
    _env_MACHINES: "{{ lookup('ansible.builtin.env', 'MACHINES') | default('', true) | string | trim }}"
    _machine_name: "{{ machine_name | default('') | string | trim }}"

- name: "Harness | Resolver current_machine (força override sem recursão)"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        (lookup('ansible.builtin.env', 'MACHINE') | default('', true) | string | trim)
        if (lookup('ansible.builtin.env', 'MACHINE') | default('', true) | string | trim) | length > 0
        else
          (
            (lookup('ansible.builtin.env', 'MACHINES') | default('', true) | string | trim).split(',')[0] | trim
            if (lookup('ansible.builtin.env', 'MACHINES') | default('', true) | string | trim) | length > 0
            else
              (_machine_name if (_machine_name | length > 0) else (current_machine | default('') | string | trim))
          )
      }}

- name: "Harness | Validar vars mínimas"
  ansible.builtin.assert:
    that:
      - current_machine is defined
      - (current_machine | string | trim | length) > 0
      - deployment_ref is defined
      - filestore_env is defined
      - machine_status_file is defined
      - log_content is defined
      - status_tag_value is defined
    fail_msg: "Faltam vars obrigatórias para harness_filestore_upload.yml (ou current_machine veio vazio)."

# -----------------------------------------------------------------------------
# 0.1) Defaults e normalizações gerais
# -----------------------------------------------------------------------------
- name: "Harness | Definir stage_name default"
  ansible.builtin.set_fact:
    stage_name: "{{ stage_name | default('unknown') | string | trim | lower }}"

- name: "Harness | Definir defaults + normalizações"
  ansible.builtin.set_fact:
    harness_account_id_resolved: "{{ harness_account_id | default('fgDto6qoTT6ctfZS9eWbEw') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('Fiserv') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('sitef') }}"
    harness_filestore_root_identifier_resolved: "{{ harness_filestore_root_identifier | default('Root') }}"
    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    env_lower: "{{ (filestore_env | string | trim | lower) }}"
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"

# -----------------------------------------------------------------------------
# 0.2) Identifiers SAFE (HARNESS RULE: [A-Za-z][A-Za-z0-9_]{0,127})
# -----------------------------------------------------------------------------
- name: "Harness | Identifiers SAFE (HARNESS RULE)"
  ansible.builtin.set_fact:
    env_identifier: "{{ env_lower | regex_replace('[^A-Za-z0-9_]', '_') }}"
    deployment_ref_identifier: "{{ (deployment_ref_folder | string | trim | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    machine_identifier: "{{ (current_machine | string | trim | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"
    stage_identifier: "{{ (stage_name | string | trim | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"

# -----------------------------------------------------------------------------
# 0.3) Token (vars > env)
# -----------------------------------------------------------------------------
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

# -----------------------------------------------------------------------------
# 0.4) Tags
# -----------------------------------------------------------------------------
- name: "Harness | Montar tags base"
  ansible.builtin.set_fact:
    harness_tags:
      - { key: "env",        value: "{{ env_lower }}" }
      - { key: "deployment", value: "{{ deployment_ref_lower }}" }
      - { key: "machine",    value: "{{ current_machine | lower }}" }
      - { key: "stage",      value: "{{ stage_name }}" }
      - { key: "tag",        value: "{{ status_tag_value }}" }

- name: "Harness | Adicionar extra_tags como tag=..."
  ansible.builtin.set_fact:
    harness_tags: "{{ harness_tags + [ {'key':'tag','value': (item | string | trim)} ] }}"
  loop: "{{ extra_tags | default([]) }}"
  when: (item | string | trim | length) > 0

- name: "Harness | Definir http_codes aceitos"
  ansible.builtin.set_fact:
    hfs_ok_create_codes: ["200","201","202","204","409"]
    hfs_ok_update_codes: ["200","201","202","204"]

- name: "Harness | Render tags JSON"
  ansible.builtin.set_fact:
    harness_tags_json: "{{ harness_tags | to_json }}"

# -----------------------------------------------------------------------------
# 1) Garantir folder ENV (filestore_env) embaixo da raiz
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder ENV (POST) com debug"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl -sS -o /tmp/hfs_env_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    if [ "${http}" = "400" ] && grep -q 'DUPLICATE_FIELD' /tmp/hfs_env_create.out; then
      echo "409"
    else
      echo "${http}"
    fi
  args:
    executable: /bin/bash
  register: hfs_env_create_http
  changed_when: hfs_env_create_http.stdout in ['200','201']
  failed_when: false

- name: "Harness | DEBUG ENV (se falhar)"
  ansible.builtin.shell: "sed -n '1,260p' /tmp/hfs_env_create.out || true"
  register: hfs_env_create_body
  changed_when: false
  failed_when: false
  when: (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes

- name: "Harness | Falhar ENV (com body)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ hfs_env_create_http.stdout | default('') }}
      body={{ hfs_env_create_body.stdout | default('') }}
  when: (hfs_env_create_http.stdout | default('')) not in hfs_ok_create_codes

# -----------------------------------------------------------------------------
# 2) Garantir folder DEPLOYMENT_REF embaixo do ENV
# -----------------------------------------------------------------------------
- name: "Harness | Garantir folder DEPLOYMENT_REF (POST) com debug"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl -sS -o /tmp/hfs_ref_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ deployment_ref_folder }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_identifier }}" \
      -F "identifier={{ deployment_ref_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
    )"
    if [ "${http}" = "400" ] && grep -q 'DUPLICATE_FIELD' /tmp/hfs_ref_create.out; then
      echo "409"
    else
      echo "${http}"
    fi
  args:
    executable: /bin/bash
  register: hfs_ref_create_http
  changed_when: hfs_ref_create_http.stdout in ['200','201']
  failed_when: false

- name: "Harness | DEBUG REF (se falhar)"
  ansible.builtin.shell: "sed -n '1,260p' /tmp/hfs_ref_create.out || true"
  register: hfs_ref_create_body
  changed_when: false
  failed_when: false
  when: (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes

- name: "Harness | Falhar REF (com body)"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder DEPLOYMENT_REF no Harness.
      http_code={{ hfs_ref_create_http.stdout | default('') }}
      body={{ hfs_ref_create_body.stdout | default('') }}
  when: (hfs_ref_create_http.stdout | default('')) not in hfs_ok_create_codes

# -----------------------------------------------------------------------------
# 3) UPSERT STATUS JSON (por stage)  (POST -> se 409 faz PUT) + retry em 500
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do STATUS JSON (por stage)"
  ansible.builtin.set_fact:
    hfs_status_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_name }}.json"
    hfs_status_identifier: "{{ deployment_ref_identifier }}_{{ machine_identifier }}_{{ env_identifier }}_{{ stage_identifier }}_json"
    hfs_status_parent_identifier: "{{ deployment_ref_identifier }}"

- name: "Harness | CREATE status (retry se 500)"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl -sS -o /tmp/hfs_status_create.out -w "%{http_code}" \
      --request POST \
      "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"
    if [ "${http}" = "400" ] && grep -q 'DUPLICATE_FIELD' /tmp/hfs_status_create.out; then
      echo "409"
    else
      echo "${http}"
    fi
  args:
    executable: /bin/bash
  register: hfs_status_create_http
  changed_when: (hfs_status_create_http.stdout | default('')) is match('^2..$')
  failed_when: false
  no_log: true
  retries: 4
  delay: 2
  until: (hfs_status_create_http.stdout | default('')) != '500'

- name: "Harness | UPDATE status (se create retornou 409) (retry se 500)"
  ansible.builtin.shell: |
    set -euo pipefail
    http="$(curl -sS -o /tmp/hfs_status_update.out -w "%{http_code}" \
      --request PUT \
      "{{ harness_api_base }}/{{ hfs_status_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ hfs_status_parent_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F 'tags={{ harness_tags_json }}' \
      -F "content=@{{ machine_status_file }}" \
    )"
    echo "${http}"
  args:
    executable: /bin/bash
  register: hfs_status_update_http
  when: (hfs_status_create_http.stdout | default('')) == '409'
  changed_when: (hfs_status_update_http.stdout | default('')) is match('^2..$')
  failed_when: false
  no_log: true
  retries: 4
  delay: 2
  until: (hfs_status_update_http.stdout | default('')) != '500'

- name: "Harness | DEBUG body STATUS (quando falhar)"
  ansible.builtin.shell: |
    echo "CREATE:"; sed -n '1,300p' /tmp/hfs_status_create.out || true
    echo "UPDATE:"; sed -n '1,300p' /tmp/hfs_status_update.out || true
  register: hfs_status_body
  changed_when: false
  failed_when: false
  when:
    - (hfs_status_create_http.stdout | default('')) not in hfs_ok_create_codes
      or ((hfs_status_create_http.stdout | default('')) == '409' and (hfs_status_update_http.stdout | default('')) not in hfs_ok_update_codes)

- name: "Harness | Falhar STATUS (com body)"
  ansible.builtin.fail:
    msg: |
      Falha no upload do STATUS no Harness File Store.
      STATUS create http_code={{ hfs_status_create_http.stdout | default('') }}
      STATUS update http_code={{ hfs_status_update_http.stdout | default('') }}
      {{ hfs_status_body.stdout | default('') }}
  when:
    - (hfs_status_create_http.stdout | default('')) not in hfs_ok_create_codes
      or ((hfs_status_create_http.stdout | default('')) == '409' and (hfs_status_update_http.stdout | default('')) not in hfs_ok_update_codes)

# -----------------------------------------------------------------------------
# 4) UPSERT LOG (por stage) (POST -> se 409 faz PUT) + retry em 500
# -----------------------------------------------------------------------------
- name: "Harness | Definir nome/identifier do LOG (por stage)"
  ansible.builtin.set_fact:
    hfs_log_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}-{{ stage_name }}.log"
    hfs_log_identifier: "{{ deployment_ref_identifier }}_{{ machine_identifier }}_{{ env_identifier }}_{{ stage_identifier }}_log"
    hfs_log_parent_identifier: "{{ deployment_ref_identifier }}"

- name: "Harness | Upload do LOG (create/update) com cleanup garantido"
  block:
    - name: "Harness | Criar arquivo temporário do log no controller"
      ansible.builtin.copy:
        dest: "/tmp/{{ hfs_log_name }}"
        mode: "0600"
        content: "{{ log_content }}"

    - name: "Harness | CREATE log (retry se 500)"
      ansible.builtin.shell: |
        set -euo pipefail
        http="$(curl -sS -o /tmp/hfs_log_create.out -w "%{http_code}" \
          --request POST \
          "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/{{ hfs_log_name }}" \
        )"
        if [ "${http}" = "400" ] && grep -q 'DUPLICATE_FIELD' /tmp/hfs_log_create.out; then
          echo "409"
        else
          echo "${http}"
        fi
      args:
        executable: /bin/bash
      register: hfs_log_create_http
      changed_when: (hfs_log_create_http.stdout | default('')) is match('^2..$')
      failed_when: false
      no_log: true
      retries: 4
      delay: 2
      until: (hfs_log_create_http.stdout | default('')) != '500'

    - name: "Harness | UPDATE log (se create retornou 409) (retry se 500)"
      ansible.builtin.shell: |
        set -euo pipefail
        http="$(curl -sS -o /tmp/hfs_log_update.out -w "%{http_code}" \
          --request PUT \
          "{{ harness_api_base }}/{{ hfs_log_identifier }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
          -H "x-api-key: {{ harness_x_api_key }}" \
          -F "name={{ hfs_log_name }}" \
          -F "type=FILE" \
          -F "parentIdentifier={{ hfs_log_parent_identifier }}" \
          -F "identifier={{ hfs_log_identifier }}" \
          -F 'tags={{ harness_tags_json }}' \
          -F "content=@/tmp/{{ hfs_log_name }}" \
        )"
        echo "${http}"
      args:
        executable: /bin/bash
      register: hfs_log_update_http
      when: (hfs_log_create_http.stdout | default('')) == '409'
      changed_when: (hfs_log_update_http.stdout | default('')) is match('^2..$')
      failed_when: false
      no_log: true
      retries: 4
      delay: 2
      until: (hfs_log_update_http.stdout | default('')) != '500'

    - name: "Harness | DEBUG body LOG (quando falhar)"
      ansible.builtin.shell: |
        echo "CREATE:"; sed -n '1,300p' /tmp/hfs_log_create.out || true
        echo "UPDATE:"; sed -n '1,300p' /tmp/hfs_log_update.out || true
      register: hfs_log_body
      changed_when: false
      failed_when: false
      when:
        - (hfs_log_create_http.stdout | default('')) not in hfs_ok_create_codes
          or ((hfs_log_create_http.stdout | default('')) == '409' and (hfs_log_update_http.stdout | default('')) not in hfs_ok_update_codes)

    - name: "Harness | Falhar LOG (com body)"
      ansible.builtin.fail:
        msg: |
          Falha no upload do LOG no Harness File Store.
          LOG create http_code={{ hfs_log_create_http.stdout | default('') }}
          LOG update http_code={{ hfs_log_update_http.stdout | default('') }}
          {{ hfs_log_body.stdout | default('') }}
      when:
        - (hfs_log_create_http.stdout | default('')) not in hfs_ok_create_codes
          or ((hfs_log_create_http.stdout | default('')) == '409' and (hfs_log_update_http.stdout | default('')) not in hfs_ok_update_codes)

  always:
    - name: "Harness | Limpar arquivo temporário do log"
      ansible.builtin.file:
        path: "/tmp/{{ hfs_log_name }}"
        state: absent
      changed_when: false
      failed_when: false


## deploy_per_machine.yml

---
# =====================================================================================
# DEPLOY POR MÁQUINA
# - Chamado por deploy_from_status.yml (PIPELINE 2)
# - Executa init_deploy.sh no host alvo
# - Mantém status.json com history[]
# - Mantém um pipeline.log cumulativo NO CONTROLLER (não apaga predeploy)
# - Faz upload para Harness File Store separando por stage:
#     <deployment>-<machine>-<env>-deploy.json/.log
#
# Requer:
#   machine_name
#   deployment_ref
#   filestore_env
#   filestore_base_dir
#   status_dir
#
# Observação:
# - Para não duplicar logs quando reexecutar o mesmo stage (deploy), usamos:
#     * "run_id" (se existir no env HARNESS_EXECUTION_ID / BUILD_ID / CI_PIPELINE_ID)
#     * e gravamos no status.json o last_upload.run_id por stage
#   => se o mesmo run_id rodar de novo, não faz append no pipeline.log (evita duplicação).
# =====================================================================================

# -----------------------------------------------------------------------------
# 0) Helpers: run_id (para dedupe) + stage
# -----------------------------------------------------------------------------
- name: "Deploy | Definir stage"
  ansible.builtin.set_fact:
    stage_name: "deploy"

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

# -----------------------------------------------------------------------------
# 1) Paths e defaults
# -----------------------------------------------------------------------------
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
- name: "Deploy | Definir deployment_ref_lower"
  ansible.builtin.set_fact:
    deployment_ref_lower: "{{ deployment_ref | lower }}"

- name: "Deploy | Definir arquivos de status/log local"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    status_file: "{{ status_dir }}/{{ current_machine }}/status.json"

    # log cumulativo no controller (não apaga predeploy)
    pipeline_log_file: "{{ status_dir }}/{{ current_machine }}/pipeline.log"

    env_folder: "{{ filestore_env | lower }}"
    deployment_ref_folder: "{{ deployment_ref }}"

    # paths “lógicos” (apenas referência)
    filestore_json_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}-{{ stage_name }}.json"
    filestore_log_path: "{{ filestore_base_dir }}/{{ deployment_ref_lower }}-{{ current_machine }}-{{ filestore_env }}-{{ stage_name }}.log"

- name: "Deploy | Garantir diretório de status da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

# -----------------------------------------------------------------------------
# 6.1) Ler status existente (pra append no JSON)
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
# 6.2) DEDUPE: decidir se vai fazer append no pipeline.log neste run_id
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
# 6.3) Escrever/atualizar status inicial (deploy:queued) + append no history[]
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
              "host": (machine_cfg.host | default(machine_cfg.ip | default(''))),
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
# 7) Verificar script e executar deploy (com tee -a no host)
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
# 8) Montar conteúdo do log padronizado (pipeline deploy)
# -----------------------------------------------------------------------------
- name: "Deploy | Montar conteúdo do log do DEPLOY"
  ansible.builtin.set_fact:
    deploy_log_content: |
      ---- pipeline {{ stage_name }} ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
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
# 9) Atualizar status final (deploy:ok / deploy:error) com append em history[]
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
# 9.5) Append no LOG cumulativo local (controller) com DEDUPE por run_id
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
# 9.6) Persistir last_upload.run_id do stage (evita duplicar em rerun)
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
# 10) Upload JSON + LOG para Harness File Store (por stage)
# -----------------------------------------------------------------------------
- name: "Deploy | Upload JSON + LOG para Harness File Store"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ status_file }}"
    log_content: "{{ log_content_to_upload }}"
    stage_name: "{{ stage_name }}"
    deployment_ref_folder: "{{ deployment_ref }}"
    status_tag_value: >-
      {{ (deployment_ref | lower) ~ ':' ~ stage_name ~ ':' ~ ('ok' if (deploy_result.rc | default(1)) == 0 else 'error') }}
    extra_tags: []

# -----------------------------------------------------------------------------
# 11) Falhar pipeline se deploy retornou erro (depois do upload)
# -----------------------------------------------------------------------------
- name: "Deploy | Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ current_machine }} (host {{ machine_cfg.host | default(machine_cfg.ip | default('')) }})"
  when: (deploy_result.rc | default(1)) != 0
