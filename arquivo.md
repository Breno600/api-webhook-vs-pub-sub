# ansible/predeploy_from_execution.yml
---
# =====================================================================
# PLAY 1 - CONTROLLER (localhost)
# Lê o arquivo de execution, resolve machines e groups e registra hosts
# dinâmicos no grupo dynamic_execution.
# =====================================================================
- name: "[CONTROLLER] Preparar hosts a partir do execution"
  hosts: localhost
  gather_facts: false

  vars:
    # raiz do repo (playbook está na pasta ansible/)
    repo_root: "{{ playbook_dir | dirname }}"

    # Caminho relativo do arquivo de execução vindo via -e execution_file=...
    execution_file: "{{ execution_file | default('execution/machine_list_dev.yml') }}"
    execution_path: "{{ repo_root }}/{{ execution_file }}"

  tasks:
    - name: "Carregar arquivo de execution"
      ansible.builtin.include_vars:
        file: "{{ execution_path }}"
        name: execution_cfg

    - name: "Definir listas de machines e groups (se existirem)"
      ansible.builtin.set_fact:
        exec_machines: "{{ execution_cfg.machines | default([]) }}"
        exec_groups: "{{ execution_cfg.groups  | default([]) }}"

    # ---------- MACHINES DIRETAS DO EXECUTION ----------
    - name: "Registrar machines declaradas diretamente no execution"
      vars:
        mname: "{{ item }}"
        machine_file_path: "{{ repo_root }}/machine/{{ mname }}.yaml"
      block:
        - name: "Carregar YAML da máquina {{ mname }}"
          ansible.builtin.include_vars:
            file: "{{ machine_file_path }}"
            name: machine_cfg

        - name: "Carregar YAML do package da máquina {{ mname }}"
          ansible.builtin.include_vars:
            file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yaml"
            name: package_cfg

        - name: "Registrar host dinâmico para {{ mname }}"
          ansible.builtin.add_host:
            name: "{{ mname }}"
            ansible_host: "{{ machine_cfg.host }}"
            ansible_user: ansible
            ansible_ssh_private_key_file: "~/.ssh/id_rsa"
            groups: dynamic_execution

            machine_name: "{{ mname }}"
            package_name: "{{ machine_cfg.package }}"
            rollback_name: "{{ machine_cfg.rollback | default('') }}"
            env_vars_merged: "{{ machine_cfg.env_vars | default({}) }}"
            machine_tags: "{{ machine_cfg.tags | default([]) }}"
            package_script: "{{ package_cfg.script }}"
            package_components: "{{ package_cfg.components | default([]) }}"
      loop: "{{ exec_machines }}"
      loop_control:
        label: "{{ item }}"

    # ---------- GROUPS DO EXECUTION ----------
    - name: "Registrar machines de cada group declarado no execution"
      vars:
        gname: "{{ item }}"
        group_file_path: "{{ repo_root }}/group-machine/{{ gname }}.yaml"
      block:
        - name: "Carregar YAML do group-machine {{ gname }}"
          ansible.builtin.include_vars:
            file: "{{ group_file_path }}"
            name: group_cfg

        - name: "Carregar YAML do package do group {{ gname }}"
          ansible.builtin.include_vars:
            file: "{{ repo_root }}/package/{{ group_cfg.package }}.yaml"
            name: package_cfg

        - name: "Definir env_vars do group {{ gname }}"
          ansible.builtin.set_fact:
            group_env_vars: "{{ group_cfg.env_vars | default({}) }}"
            group_package_name: "{{ group_cfg.package }}"
            group_rollback_name: "{{ group_cfg.rollback | default('') }}"

        - name: "Registrar hosts das máquinas do group {{ gname }}"
          vars:
            mname: "{{ item2 }}"
            machine_file_path: "{{ repo_root }}/machine/{{ mname }}.yaml"
          block:
            - name: "Carregar YAML da máquina {{ mname }}"
              ansible.builtin.include_vars:
                file: "{{ machine_file_path }}"
                name: machine_cfg

            - name: "Mesclar env_vars (group + machine)"
              ansible.builtin.set_fact:
                merged_env_vars: >-
                  {{
                    group_env_vars
                    | combine(machine_cfg.env_vars | default({}), recursive=True)
                  }}

            - name: "Registrar host dinâmico para {{ mname }} (via group {{ gname }})"
              ansible.builtin.add_host:
                name: "{{ mname }}"
                ansible_host: "{{ machine_cfg.host }}"
                ansible_user: ansible
                ansible_ssh_private_key_file: "~/.ssh/id_rsa"
                groups: dynamic_execution

                machine_name: "{{ mname }}"
                package_name: "{{ group_package_name }}"
                rollback_name: "{{ group_rollback_name or machine_cfg.rollback | default('') }}"
                env_vars_merged: "{{ merged_env_vars }}"
                machine_tags: "{{ machine_cfg.tags | default([]) }}"
                package_script: "{{ package_cfg.script }}"
                package_components: "{{ package_cfg.components | default([]) }}"
          loop: "{{ group_cfg.machines }}"
          loop_control:
            loop_var: item2
            label: "{{ item2 }}"
      loop: "{{ exec_groups }}"
      loop_control:
        label: "{{ item }}"

# =====================================================================
# PLAY 2 - TARGETS (dynamic_execution)
# Copia scripts, executa pré-deploy (init_parallel.sh) + download Nexus
# e grava status/<deployment_ref>/predeploy-<machine>.json
# =====================================================================
- name: "[TARGET] Pré-deploy para machines do execution"
  hosts: dynamic_execution
  become: yes
  gather_facts: false

  vars:
    repo_root: "{{ playbook_dir | dirname }}"

    remote_base_dir: "/opt/sitef"
    remote_scripts_dir: "{{ remote_base_dir }}/scripts/{{ package_script }}"
    remote_packages_dir: "{{ remote_base_dir }}/packages"

    # Ajustar para o seu Nexus real
    nexus_base_url: "https://nexus-ci.onefiserv.net/repository/raw-apm00045468-dev"
    nexus_user: "{{ lookup('env','NEXUS_USER') | default('', true) }}"
    nexus_password: "{{ lookup('env','NEXUS_PASSWORD') | default('', true) }}"

  tasks:
    - name: "Criar diretórios base no target"
      ansible.builtin.file:
        path: "{{ item }}"
        state: directory
        mode: "0755"
      loop:
        - "{{ remote_base_dir }}"
        - "{{ remote_scripts_dir }}"
        - "{{ remote_packages_dir }}"
        - "{{ remote_packages_dir }}/Linux"

    - name: "Copiar scripts do package para o target"
      ansible.builtin.copy:
        src: "{{ repo_root }}/scripts/{{ package_script }}/"
        dest: "{{ remote_scripts_dir }}/"
        mode: "0755"

    # -------- PREDEPLOY (backup / preparação) --------
    - name: "Verificar se existe init_parallel.sh (pré-deploy)"
      ansible.builtin.stat:
        path: "{{ remote_scripts_dir }}/init_parallel.sh"
      register: st_init_parallel

    - name: "Executar pré-deploy (init_parallel.sh)"
      ansible.builtin.shell: "bash {{ remote_scripts_dir }}/init_parallel.sh"
      environment: "{{ env_vars_merged | default({}) }}"
      when: st_init_parallel.stat.exists
      register: predeploy_parallel
      failed_when: false

    # -------- DOWNLOAD COMPONENTES DO NEXUS --------
    - name: "Baixar componentes do Nexus"
      ansible.builtin.get_url:
        url: "{{ nexus_base_url }}/{{ item }}"
        dest: "{{ remote_base_dir }}/{{ item }}"
        mode: "0644"
        url_username: "{{ nexus_user }}"
        url_password: "{{ nexus_password }}"
        force_basic_auth: true
      loop: "{{ package_components }}"
      loop_control:
        label: "{{ item }}"
      register: download_result
      failed_when: false

    - name: "Verificar se houve erro em algum download"
      ansible.builtin.set_fact:
        download_failed: >-
          {{
            (download_result is defined and
             download_result.results is defined and
             (download_result.results
              | selectattr('failed','defined')
              | selectattr('failed')
              | list
              | length) > 0)
            | default(false)
          }}

    - name: "Definir status final do pré-deploy"
      ansible.builtin.set_fact:
        predeploy_status: >-
          {{
            'success'
            if ( (st_init_parallel.stat.exists == false
                  or predeploy_parallel.rc|default(0) == 0)
                 and not download_failed )
            else 'failed'
          }}

    # -------- GRAVAR JSON DE STATUS NO CONTROLLER --------
    - name: "Registrar status em arquivo JSON (controller)"
      delegate_to: localhost
      run_once: false
      vars:
        # deployment_ref vem da pipeline (-e "deployment_ref=TAG")
        status_dir: "{{ repo_root }}/status/{{ deployment_ref | default('no-ref') }}"
        status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
        now_iso: "{{ lookup('pipe', 'date -u +%Y-%m-%dT%H:%M:%SZ') }}"
        status_payload:
          machine: "{{ machine_name }}"
          host: "{{ ansible_host }}"
          package: "{{ package_name }}"
          rollback: "{{ rollback_name }}"
          script: "{{ package_script }}"
          components: "{{ package_components }}"
          tags: "{{ machine_tags }}"
          status: "{{ predeploy_status }}"
          updated_at: "{{ now_iso }}"
          deployment_ref: "{{ deployment_ref | default('') }}"
      block:
        - name: "Garantir diretório de status existe"
          ansible.builtin.file:
            path: "{{ status_dir }}"
            state: directory
            mode: "0755"

        - name: "Gravar JSON de status"
          ansible.builtin.copy:
            dest: "{{ status_file }}"
            mode: "0644"
            content: "{{ status_payload | to_nice_json }}"



-----------

#!/bin/bash
set -euo pipefail

echo "== 1) Clonando repositório =="
# Ajusta URL com o seu usuário/projeto e usa o token do GitLab
GIT_TOKEN="${GIT_TOKEN:?GIT_TOKEN nao definido}"
REPO_URL="https://${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

WORKDIR="/tmp/elastic-compute-cloud-sitef"
rm -rf "$WORKDIR" || true
git clone "$REPO_URL" "$WORKDIR"
cd "$WORKDIR"

git config --global --add safe.directory "$WORKDIR"

GIT_TAG="${GIT_TAG:?GIT_TAG nao definido}"
STATUS_BRANCH="${STATUS_BRANCH:-main}"
EXECUTION_FILE="${EXECUTION_FILE:-execution/machine_list_dev.yml}"

echo "== 2) Checkout da TAG =="
git fetch --all --tags
git checkout "$GIT_TAG"

echo "== 3) Rodando predeploy via Ansible (TAG: $GIT_TAG) =="
cd ansible

ansible-playbook predeploy_from_execution.yml \
  -e "execution_file=${EXECUTION_FILE}" \
  -e "deployment_ref=${GIT_TAG}" \
  --forks 20

cd "$WORKDIR"

echo "== 4) Preparando commit de status =="
if [ ! -d "status" ]; then
  echo "Diretorio status/ nao encontrado, nada para commitar."
  exit 0
fi

TMP_STATUS="/tmp/status-${GIT_TAG}-$(date +%s)"
mkdir -p "$TMP_STATUS"
cp -r status/* "$TMP_STATUS/" || true

echo "== 5) Checkout do branch de status: $STATUS_BRANCH =="
git checkout "$STATUS_BRANCH"
git pull origin "$STATUS_BRANCH"

mkdir -p status
cp -r "$TMP_STATUS"/* status/ || true
rm -rf "$TMP_STATUS"

if [ -z "$(git status --porcelain status)" ]; then
  echo "Nenhuma alteracao em status/, nada para commitar."
  exit 0
fi

echo "== 6) Commitando status no repo =="
git config user.name  "${GIT_USER_NAME:-harness-bot}"
git config user.email "${GIT_USER_EMAIL:-harness-bot@example.com}"

git add status
git commit -m "chore: predeploy status for ${GIT_TAG}"
git push origin "$STATUS_BRANCH"

echo "== 7) Fim da pipe 1 (status commitado em status/${GIT_TAG}) =="



-----------

