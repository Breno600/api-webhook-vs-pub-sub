# ansible/predeploy_from_execution.yml
- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

    # Preferível vir do Harness Secrets
    nexus_base_url: "{{ nexus_base_url | default('https://nexus.exemplo/repository/raw-dev') }}"
    nexus_user: "{{ nexus_user | default(lookup('env','NEXUS_USER')) }}"
    nexus_password: "{{ nexus_password | default(lookup('env','NEXUS_PASSWORD')) }}"

  tasks:
    - name: "Mostrar variáveis de entrada"
      debug:
        msg:
          - "execution_file_name = {{ execution_file_name }}"
          - "deployment_ref     = {{ deployment_ref }}"
          - "repo_root          = {{ repo_root }}"
          - "status_dir         = {{ status_dir }}"

    - name: "Criar diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir }}"
        state: directory
        mode: "0755"

    - name: "Carregar arquivo de execução"
      ansible.builtin.include_vars:
        file: "{{ repo_root }}/{{ execution_file_name }}"
        name: execution_cfg

    - name: "Falhar se não tiver máquinas no arquivo de execução"
      ansible.builtin.fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: >
        execution_cfg.machines is not defined or
        execution_cfg.machines | length == 0

    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name

# ansible/predeploy_per_machine.yml
- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
    local_log_file: "{{ status_dir }}/predeploy-{{ machine_name }}.log"

    pipeline_base: "/opt/SoftwareExpress/sitef-pipeline/deploy"
    pipeline_pkg_root: "/opt/SoftwareExpress/sitef-pipeline/deploy"
    pipeline_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    pipeline_scripts_pkg_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

- name: "Definir usuário alvo padrão para {{ machine_name }}"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default('ec2-user') }}"

- name: "Definir ssh_common_args padrão da máquina (se existir)"
  ansible.builtin.set_fact:
    target_ssh_common_args: "{{ machine_cfg.ssh_common_args | default('') }}"

- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    target_ssh_common_args: "-o ProxyJump={{ bastion_user | default('ec2-user') }}@{{ bastion_host }}"
  when:
    - (target_ssh_common_args | length) == 0
    - bastion_host is defined

- name: "Registrar host dinâmico para {{ machine_name }}"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"

- name: "Criar status inicial (queued) para {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-predeploy.log"
      }

- name: "Garantir base do pipeline no host"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ pipeline_base }}"
    - "{{ pipeline_scripts_dir }}"
    - "{{ pipeline_scripts_pkg_dir }}"
    - "{{ pipeline_pkg_root }}/package/linux"
  delegate_to: "{{ machine_name }}"

- name: "Limpar pasta de scripts do predeploy"
  become: true
  ansible.builtin.shell: |
    rm -rf "{{ pipeline_scripts_dir }}"/*
    mkdir -p "{{ pipeline_scripts_dir }}"
    mkdir -p "{{ pipeline_scripts_pkg_dir }}"
  delegate_to: "{{ machine_name }}"

- name: "Copiar package.yml para scripts/package/"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    dest: "{{ pipeline_scripts_pkg_dir }}/{{ machine_cfg.package }}.yml"
    mode: "0644"
  delegate_to: "{{ machine_name }}"

- name: "Baixar componentes do Nexus para a área do pipeline"
  become: true
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ pipeline_pkg_root }}/{{ item }}"
    mode: "0644"
    url_username: "{{ nexus_user }}"
    url_password: "{{ nexus_password }}"
    force: true
  loop: "{{ package_cfg.components }}"
  delegate_to: "{{ machine_name }}"
  no_log: true

- name: "Copiar scripts do package para a área do pipeline"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ package_cfg.script }}/"
    dest: "{{ pipeline_scripts_dir }}/"
    mode: "0755"
  delegate_to: "{{ machine_name }}"

- name: "Montar env_vars efetivas (package + machine + pipeline base)"
  ansible.builtin.set_fact:
    merged_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
        | combine({'SITEF_PIPELINE_BASE': pipeline_base}, recursive=True)
      }}

- name: "Executar init_parallel.sh no host"
  become: true
  ansible.builtin.shell: |
    cd "{{ pipeline_scripts_dir }}"
    /bin/bash -x ./init_parallel.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ merged_env }}"
  register: predeploy_result
  ignore_errors: true

- name: "Ler parallel.txt do host alvo"
  become: true
  ansible.builtin.shell: |
    cat "{{ pipeline_scripts_dir }}/parallel.txt" 2>/dev/null || echo "parallel.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: parallel_log
  changed_when: false

# Materializa LOG local no workspace
- name: "Salvar log local do predeploy no workspace"
  ansible.builtin.copy:
    dest: "{{ local_log_file }}"
    content: "{{ parallel_log.stdout | default('') }}"
  when: parallel_log is defined

- name: "Atualizar status local predeploy success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "tags": [
          "{{ deployment_ref | lower }}:pre-deploy:ok",
          "{{ deployment_ref | lower }}:deploy:pending"
        ],
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-predeploy.log",
        "stdout": {{ predeploy_result.stdout | default('') | to_json }},
        "stderr": {{ predeploy_result.stderr | default('') | to_json }}
      }
  when: predeploy_result.rc == 0

- name: "Atualizar status local predeploy failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "tags": [
          "{{ deployment_ref | lower }}:pre-deploy:error"
        ],
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-predeploy.log",
        "stdout": {{ predeploy_result.stdout | default('') | to_json }},
        "stderr": {{ predeploy_result.stderr | default('') | to_json }}
      }
  when: predeploy_result.rc != 0

- name: "Falhar pipeline se init_parallel.sh retornou erro"
  ansible.builtin.fail:
    msg: "Predeploy falhou em {{ machine_name }} (host {{ machine_cfg.host }})."
  when: predeploy_result.rc != 0

#ansible/deploy_from_status.yml
- name: "Deploy a partir do status do predeploy"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

  tasks:
    - name: "Garantir que o diretório de status exista"
      ansible.builtin.stat:
        path: "{{ status_dir }}"
      register: status_dir_stat

    - name: "Falhar se status_dir não existir"
      ansible.builtin.fail:
        msg: "Diretório de status não encontrado: {{ status_dir }}. Rode o predeploy primeiro."
      when: not status_dir_stat.stat.exists

    - name: "Listar arquivos de predeploy da TAG"
      ansible.builtin.find:
        paths: "{{ status_dir }}"
        patterns: "predeploy-*.json"
        file_type: file
      register: predeploy_files

    - name: "Falhar se não houver predeploy-*.json"
      ansible.builtin.fail:
        msg: "Nenhum arquivo predeploy-*.json encontrado em {{ status_dir }}"
      when: predeploy_files.matched | int == 0

    - name: "Carregar JSONs de predeploy"
      ansible.builtin.slurp:
        src: "{{ item.path }}"
      loop: "{{ predeploy_files.files }}"
      register: predeploy_slurped

    - name: "Montar lista de predeploys parseados"
      ansible.builtin.set_fact:
        predeploy_jsons: "{{ predeploy_jsons | default([]) + [ (item.content | b64decode | from_json) ] }}"
      loop: "{{ predeploy_slurped.results }}"

    - name: "Montar lista de máquinas com predeploy success"
      ansible.builtin.set_fact:
        machines_to_deploy: "{{ (machines_to_deploy | default([])) + [ item.machine ] }}"
      loop: "{{ predeploy_jsons }}"
      when:
        - item.status is defined
        - item.status == "success"

    - name: "Falhar se nenhuma máquina estiver apta para deploy"
      ansible.builtin.fail:
        msg: "Nenhuma máquina com status=success no predeploy para a TAG {{ deployment_ref }}"
      when: machines_to_deploy | default([]) | length == 0

    - name: "Executar deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      loop: "{{ machines_to_deploy }}"
      loop_control:
        loop_var: target_machine

# ansible/deploy_per_machine.yml
- name: "Normalizar variável de máquina"
  ansible.builtin.set_fact:
    machine_name: "{{ target_machine | default(machine_name) }}"

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/deploy-{{ machine_name }}.json"
    local_log_file: "{{ status_dir }}/deploy-{{ machine_name }}.log"

    pipeline_base: "/opt/SoftwareExpress/sitef-pipeline/deploy"
    pipeline_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

- name: "Registrar host dinâmico"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ machine_cfg.user | default('ec2-user') }}"
    ansible_connection: ssh

- name: "Criar status inicial (queued)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-deploy.log"
      }

- name: "Montar env_vars efetivas"
  ansible.builtin.set_fact:
    merged_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
        | combine({'SITEF_PIPELINE_BASE': pipeline_base}, recursive=True)
      }}

- name: "Executar init_deploy.sh no host"
  become: true
  ansible.builtin.shell: |
    cd "{{ pipeline_scripts_dir }}"
    /bin/bash -x ./init_deploy.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ merged_env }}"
  register: deploy_result
  ignore_errors: true

- name: "Ler deploy.txt do host alvo"
  become: true
  ansible.builtin.shell: |
    cat "{{ pipeline_scripts_dir }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: deploy_log
  changed_when: false

- name: "Salvar log local do deploy no workspace"
  ansible.builtin.copy:
    dest: "{{ local_log_file }}"
    content: "{{ deploy_log.stdout | default('') }}"

- name: "Atualizar status deploy success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "tags": [
          "{{ deployment_ref | lower }}:pre-deploy:ok",
          "{{ deployment_ref | lower }}:deploy:ok"
        ],
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-deploy.log",
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }}
      }
  when: deploy_result.rc == 0

- name: "Atualizar status deploy failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "tags": [
          "{{ deployment_ref | lower }}:pre-deploy:ok",
          "{{ deployment_ref | lower }}:deploy:error"
        ],
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-deploy.log",
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }}
      }
  when: deploy_result.rc != 0

- name: "Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "Deploy falhou em {{ machine_name }} (host {{ machine_cfg.host }})."
  when: deploy_result.rc != 0

# ansible/rollback_from_status.yml
- name: "Rollback a partir do status do deploy"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

  tasks:
    - name: "Listar arquivos de deploy da TAG"
      ansible.builtin.find:
        paths: "{{ status_dir }}"
        patterns: "deploy-*.json"
        file_type: file
      register: deploy_files

    - name: "Falhar se não houver deploy-*.json"
      ansible.builtin.fail:
        msg: "Nenhum arquivo deploy-*.json encontrado em {{ status_dir }}"
      when: deploy_files.matched | int == 0

    - name: "Carregar JSONs de deploy"
      ansible.builtin.slurp:
        src: "{{ item.path }}"
      loop: "{{ deploy_files.files }}"
      register: deploy_slurped

    - name: "Montar lista de deploys parseados"
      ansible.builtin.set_fact:
        deploy_jsons: "{{ deploy_jsons | default([]) + [ (item.content | b64decode | from_json) ] }}"
      loop: "{{ deploy_slurped.results }}"

    - name: "Montar lista de máquinas com deploy failed"
      ansible.builtin.set_fact:
        machines_to_rollback: "{{ (machines_to_rollback | default([])) + [ item.machine ] }}"
      loop: "{{ deploy_jsons }}"
      when:
        - item.status is defined
        - item.status == "failed"

    - name: "Falhar se nenhuma máquina estiver apta para rollback"
      ansible.builtin.fail:
        msg: "Nenhuma máquina com status=failed no deploy para a TAG {{ deployment_ref }}"
      when: machines_to_rollback | default([]) | length == 0

    - name: "Executar rollback por máquina"
      ansible.builtin.include_tasks: rollback_per_machine.yml
      loop: "{{ machines_to_rollback }}"
      loop_control:
        loop_var: target_machine

#ansible/rollback_per_machine.yml
- name: "Normalizar variável de máquina"
  ansible.builtin.set_fact:
    machine_name: "{{ target_machine | default(machine_name) }}"

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    rollback_pkg_file: "{{ repo_root }}/package/{{ (lookup('file', machine_file) | from_yaml).rollback }}.yml"
    status_file: "{{ status_dir }}/rollback-{{ machine_name }}.json"
    local_log_file: "{{ status_dir }}/rollback-{{ machine_name }}.log"

    pipeline_base: "/opt/SoftwareExpress/sitef-pipeline/rollback"
    pipeline_pkg_root: "/opt/SoftwareExpress/sitef-pipeline/rollback"
    pipeline_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts"
    pipeline_scripts_pkg_dir: "/opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote rollback {{ machine_cfg.rollback }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.rollback }}.yml"
    name: package_cfg

- name: "Registrar host dinâmico"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ machine_cfg.user | default('ec2-user') }}"
    ansible_connection: ssh

- name: "Criar status inicial (queued)"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-rollback.log"
      }

- name: "Garantir base do pipeline no host"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ pipeline_base }}"
    - "{{ pipeline_scripts_dir }}"
    - "{{ pipeline_scripts_pkg_dir }}"
    - "{{ pipeline_pkg_root }}/package/linux"
  delegate_to: "{{ machine_name }}"

- name: "Limpar pasta de scripts do rollback"
  become: true
  ansible.builtin.shell: |
    rm -rf "{{ pipeline_scripts_dir }}"/*
    mkdir -p "{{ pipeline_scripts_dir }}"
    mkdir -p "{{ pipeline_scripts_pkg_dir }}"
  delegate_to: "{{ machine_name }}"

- name: "Copiar package.yml de rollback para scripts/package/"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/package/{{ machine_cfg.rollback }}.yml"
    dest: "{{ pipeline_scripts_pkg_dir }}/{{ machine_cfg.rollback }}.yml"
    mode: "0644"
  delegate_to: "{{ machine_name }}"

- name: "Baixar componentes do Nexus para a área do rollback"
  become: true
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ pipeline_pkg_root }}/{{ item }}"
    mode: "0644"
    url_username: "{{ nexus_user }}"
    url_password: "{{ nexus_password }}"
    force: true
  loop: "{{ package_cfg.components }}"
  delegate_to: "{{ machine_name }}"
  no_log: true

- name: "Copiar scripts do package rollback para a área do pipeline"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ package_cfg.script }}/"
    dest: "{{ pipeline_scripts_dir }}/"
    mode: "0755"
  delegate_to: "{{ machine_name }}"

- name: "Montar env_vars efetivas"
  ansible.builtin.set_fact:
    merged_env: >-
      {{
        (package_cfg.env_vars | default({}))
        | combine(machine_cfg.env_vars | default({}), recursive=True)
        | combine({'SITEF_PIPELINE_BASE': pipeline_base}, recursive=True)
      }}

- name: "Executar init_rollback.sh no host"
  become: true
  ansible.builtin.shell: |
    cd "{{ pipeline_scripts_dir }}"
    /bin/bash -x ./init_rollback.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ merged_env }}"
  register: rollback_result
  ignore_errors: true

- name: "Ler rollback.txt do host alvo"
  become: true
  ansible.builtin.shell: |
    cat "{{ pipeline_scripts_dir }}/rollback.txt" 2>/dev/null || echo "rollback.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: rollback_log
  changed_when: false

- name: "Salvar log local do rollback no workspace"
  ansible.builtin.copy:
    dest: "{{ local_log_file }}"
    content: "{{ rollback_log.stdout | default('') }}"

- name: "Atualizar status rollback success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "tags": [
          "{{ deployment_ref | lower }}:rollback:ok"
        ],
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-rollback.log",
        "stdout": {{ rollback_result.stdout | default('') | to_json }},
        "stderr": {{ rollback_result.stderr | default('') | to_json }}
      }
  when: rollback_result.rc == 0

- name: "Atualizar status rollback failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "tags": [
          "{{ deployment_ref | lower }}:rollback:error"
        ],
        "log_path": "dev/{{ deployment_ref }}/{{ deployment_ref | lower }}-{{ machine_name }}-rollback.log",
        "stdout": {{ rollback_result.stdout | default('') | to_json }},
        "stderr": {{ rollback_result.stderr | default('') | to_json }}
      }
  when: rollback_result.rc != 0

- name: "Falhar pipeline se init_rollback.sh retornou erro"
  ansible.builtin.fail:
    msg: "Rollback falhou em {{ machine_name }} (host {{ machine_cfg.host }})."
  when: rollback_result.rc != 0

# harness/pre-deploy-pipeline-script.sh
#!/bin/bash
set -euo pipefail

BRANCH="${BRANCH:-develop}"
GIT_TAG="${GIT_TAG:?GIT_TAG obrigatório}"
EXECUTION_FILE_NAME="${EXECUTION_FILE_NAME:-execution/machine_list_dev.yml}"

REPO_URL="${REPO_URL:?REPO_URL obrigatório (use secret)}"

# Nexus via env (secret)
export NEXUS_USER="${NEXUS_USER:-}"
export NEXUS_PASSWORD="${NEXUS_PASSWORD:-}"
export NEXUS_BASE_URL="${NEXUS_BASE_URL:-}"

WORKDIR="$(mktemp -d)"
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

echo "== PIPELINE PREDEPLOY =="
echo "BRANCH: ${BRANCH}"
echo "TAG   : ${GIT_TAG}"
echo "EXEC  : ${EXECUTION_FILE_NAME}"

cd ansible

ansible-playbook predeploy_from_execution.yml \
  -e "execution_file_name=${EXECUTION_FILE_NAME}" \
  -e "deployment_ref=${GIT_TAG}" \
  -e "nexus_base_url=${NEXUS_BASE_URL}" \
  -e "nexus_user=${NEXUS_USER}" \
  -e "nexus_password=${NEXUS_PASSWORD}" \
  --forks 10

cd ..

STATUS_DIR="status/${GIT_TAG}"
test -d "${STATUS_DIR}"

# Gera arquivos nomeados pro File Store
# Renomeação leve para ficar no padrão solicitado
for f in "${STATUS_DIR}"/predeploy-*.json; do
  m="$(basename "$f" | sed 's/^predeploy-//' | sed 's/\.json$//')"
  cp "$f" "${STATUS_DIR}/${GIT_TAG,,}-${m}-predeploy.json"
done

for f in "${STATUS_DIR}"/predeploy-*.log; do
  m="$(basename "$f" | sed 's/^predeploy-//' | sed 's/\.log$//')"
  cp "$f" "${STATUS_DIR}/${GIT_TAG,,}-${m}-predeploy.log"
done

tar -czf "predeploy-${GIT_TAG}.tar.gz" "${STATUS_DIR}"

echo
echo "== ARTEFATOS PARA UPLOAD NO FILE STORE =="
echo "1) predeploy-${GIT_TAG}.tar.gz"
echo "2) ${STATUS_DIR}/${GIT_TAG,,}-<machine>-predeploy.json"
echo "3) ${STATUS_DIR}/${GIT_TAG,,}-<machine>-predeploy.log"
echo
echo "Destino sugerido:"
echo "dev/${GIT_TAG}/"

# harness/deploy-pipeline-script.sh
#!/usr/bin/env bash
set -euo pipefail

BRANCH="${BRANCH:-develop}"
GIT_TAG="${GIT_TAG:?GIT_TAG obrigatório}"
MACHINES="${MACHINES:-${MACHINE:-}}"
STRATEGY="${STRATEGY:-deploy}"

REPO_URL="${REPO_URL:?REPO_URL obrigatório (use secret)}"

export NEXUS_USER="${NEXUS_USER:-}"
export NEXUS_PASSWORD="${NEXUS_PASSWORD:-}"
export NEXUS_BASE_URL="${NEXUS_BASE_URL:-}"

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: MACHINES/MACHINE vazio"
  exit 1
fi

MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  MACHINES_CLEAN+="${m} "
done

WORKDIR="$(mktemp -d)"
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

echo "== PIPELINE DEPLOY =="
echo "BRANCH   : ${BRANCH}"
echo "STRATEGY : ${STRATEGY}"
echo "TAG      : ${GIT_TAG}"
echo "MACHINES : ${MACHINES_CLEAN}"

for m in ${MACHINES_CLEAN}; do
  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "deployment_ref=${GIT_TAG}" \
    -e "machine_name=${m}" \
    -e "nexus_base_url=${NEXUS_BASE_URL}" \
    -e "nexus_user=${NEXUS_USER}" \
    -e "nexus_password=${NEXUS_PASSWORD}"
done

STATUS_DIR="status/${GIT_TAG}"
test -d "${STATUS_DIR}"

for f in "${STATUS_DIR}"/deploy-*.json; do
  m="$(basename "$f" | sed 's/^deploy-//' | sed 's/\.json$//')"
  cp "$f" "${STATUS_DIR}/${GIT_TAG,,}-${m}-deploy.json"
done

for f in "${STATUS_DIR}"/deploy-*.log; do
  m="$(basename "$f" | sed 's/^deploy-//' | sed 's/\.log$//')"
  cp "$f" "${STATUS_DIR}/${GIT_TAG,,}-${m}-deploy.log"
done

tar -czf "deploy-${GIT_TAG}.tar.gz" "${STATUS_DIR}"

echo
echo "== ARTEFATOS PARA UPLOAD NO FILE STORE =="
echo "1) deploy-${GIT_TAG}.tar.gz"
echo "2) ${STATUS_DIR}/${GIT_TAG,,}-<machine>-deploy.json"
echo "3) ${STATUS_DIR}/${GIT_TAG,,}-<machine>-deploy.log"
echo
echo "Destino sugerido:"
echo "dev/${GIT_TAG}/"

# harness/rollback-pipeline-script.sh
#!/usr/bin/env bash
set -euo pipefail

BRANCH="${BRANCH:-develop}"
GIT_TAG="${GIT_TAG:?GIT_TAG obrigatório}"

REPO_URL="${REPO_URL:?REPO_URL obrigatório (use secret)}"

export NEXUS_USER="${NEXUS_USER:-}"
export NEXUS_PASSWORD="${NEXUS_PASSWORD:-}"
export NEXUS_BASE_URL="${NEXUS_BASE_URL:-}"

WORKDIR="$(mktemp -d)"
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

echo "== PIPELINE ROLLBACK =="
echo "BRANCH: ${BRANCH}"
echo "TAG   : ${GIT_TAG}"

cd ansible

ansible-playbook rollback_from_status.yml \
  -e "deployment_ref=${GIT_TAG}" \
  -e "nexus_base_url=${NEXUS_BASE_URL}" \
  -e "nexus_user=${NEXUS_USER}" \
  -e "nexus_password=${NEXUS_PASSWORD}"

cd ..

STATUS_DIR="status/${GIT_TAG}"
test -d "${STATUS_DIR}"

for f in "${STATUS_DIR}"/rollback-*.json; do
  m="$(basename "$f" | sed 's/^rollback-//' | sed 's/\.json$//')"
  cp "$f" "${STATUS_DIR}/${GIT_TAG,,}-${m}-rollback.json"
done

for f in "${STATUS_DIR}"/rollback-*.log; do
  m="$(basename "$f" | sed 's/^rollback-//' | sed 's/\.log$//')"
  cp "$f" "${STATUS_DIR}/${GIT_TAG,,}-${m}-rollback.log"
done

tar -czf "rollback-${GIT_TAG}.tar.gz" "${STATUS_DIR}"

echo
echo "== ARTEFATOS PARA UPLOAD NO FILE STORE =="
echo "1) rollback-${GIT_TAG}.tar.gz"
echo "2) ${STATUS_DIR}/${GIT_TAG,,}-<machine>-rollback.json"
echo "3) ${STATUS_DIR}/${GIT_TAG,,}-<machine>-rollback.log"
echo
echo "Destino sugerido:"
echo "dev/${GIT_TAG}/"
