---
# =====================================================================
# PLAY 1 - CONTROLLER (localhost)
# Lê o JSON de status + machine/<nome>.yml e registra 1 host dinâmico
# =====================================================================
- name: "[CONTROLLER] Preparar deploy/rollback de uma máquina"
  hosts: localhost
  gather_facts: false

  vars:
    repo_root: "{{ playbook_dir | dirname }}"

    # Variáveis esperadas via pipeline (-e):
    #  - machine_name (ex: sitef-01)
    #  - deployment_ref (ex: DEV000000001)
    #  - action (deploy|rollback)
    machine_name: "{{ machine_name }}"
    deployment_ref: "{{ deployment_ref }}"
    action: "{{ action | default('deploy') }}"

    status_file: "{{ repo_root }}/status/{{ deployment_ref }}/predeploy-{{ machine_name }}.json"
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"

  tasks:
    - name: "Validar se arquivo de status existe"
      ansible.builtin.stat:
        path: "{{ status_file }}"
      register: st_status

    - name: "Falhar se status da máquina não existe para essa TAG"
      ansible.builtin.fail:
        msg: "Status nao encontrado para machine={{ machine_name }}, tag={{ deployment_ref }} em {{ status_file }}"
      when: not st_status.stat.exists

    - name: "Carregar JSON de status"
      ansible.builtin.set_fact:
        status_data: "{{ lookup('file', status_file) | from_json }}"

    - name: "Falhar se predeploy nao foi sucesso"
      ansible.builtin.fail:
        msg: "Predeploy da machine={{ machine_name }} esta com status='{{ status_data.status }}', nao eh permitido prosseguir."
      when: status_data.status != 'success'

    - name: "Carregar YAML da máquina"
      ansible.builtin.include_vars:
        file: "{{ machine_file }}"
        name: machine_cfg

    # ----------------------------------------------------------
    # Definir usuário e chave padrão (igual ao predeploy)
    # ----------------------------------------------------------
    - name: "Definir usuário alvo e chave padrão"
      ansible.builtin.set_fact:
        target_user: "{{ machine_cfg.user | default('ec2-user') }}"
        target_key: "{{ machine_cfg.private_key_file | default('/home/ec2-user/.ssh/id_rsa') }}"
        raw_ssh_args: "{{ machine_cfg.ssh_common_args | default('') }}"

    - name: "Montar ssh_common_args efetivo"
      ansible.builtin.set_fact:
        effective_ssh_args: >-
          {{ raw_ssh_args }}
          -o IdentitiesOnly=yes
          -i {{ target_key }}

    # ----------------------------------------------------------
    # Definir package conforme ACTION
    # ----------------------------------------------------------
    - name: "Definir package a ser usado conforme ACTION"
      ansible.builtin.set_fact:
        selected_package: >-
          {{
            machine_cfg.package
            if action == 'deploy'
            else machine_cfg.rollback | default('')
          }}

    - name: "Falhar se package nao definido para deploy"
      ansible.builtin.fail:
        msg: "Package nao definido para machine={{ machine_name }} em machine/{{ machine_name }}.yml"
      when:
        - action == "deploy"
        - (selected_package is undefined or selected_package == '')

    - name: "Falhar se rollback nao definido"
      ansible.builtin.fail:
        msg: "Rollback nao definido para machine={{ machine_name }} em machine/{{ machine_name }}.yml"
      when:
        - action == "rollback"
        - (selected_package is undefined or selected_package == '')

    - name: "Carregar YAML do package selecionado"
      ansible.builtin.include_vars:
        file: "{{ repo_root }}/package/{{ selected_package }}.yml"
        name: package_cfg

    - name: "Definir script e env_vars"
      ansible.builtin.set_fact:
        package_script: "{{ package_cfg.script }}"
        package_components: "{{ package_cfg.components | default([]) }}"
        machine_env_vars: "{{ machine_cfg.env_vars | default({}) }}"

    # ----------------------------------------------------------
    # Registrar host dinâmico
    # status_data.host vem do predeploy (host real)
    # ----------------------------------------------------------
    - name: "Registrar host alvo dinamico para deploy/rollback"
      ansible.builtin.add_host:
        name: dynamic_deploy_target
        ansible_host: "{{ status_data.host }}"
        ansible_user: "{{ target_user }}"
        ansible_ssh_private_key_file: "{{ target_key }}"
        ansible_ssh_common_args: "{{ effective_ssh_args }}"
        groups: dynamic_deploy

        # Metadados disponíveis no PLAY 2
        machine_name: "{{ machine_name }}"
        action: "{{ action }}"
        package_name: "{{ selected_package }}"
        package_script: "{{ package_script }}"
        package_components: "{{ package_components }}"
        env_vars: "{{ machine_env_vars }}"

# =====================================================================
# PLAY 2 - TARGET (dynamic_deploy)
# Copia scripts e executa init.sh do package (deploy ou rollback)
# =====================================================================
- name: "[TARGET] Executar {{ hostvars['dynamic_deploy_target'].action | default('deploy') }} na máquina"
  hosts: dynamic_deploy
  become: true
  gather_facts: false

  vars:
    repo_root: "{{ playbook_dir | dirname }}"

    # Alinhado com o predeploy
    remote_base_dir: "/opt/SoftwareExpress/sitef"
    remote_pkg_dir: "{{ remote_base_dir }}/package/linux"
    remote_scripts_root: "{{ remote_base_dir }}/scripts"
    remote_scripts_dir: "{{ remote_scripts_root }}/{{ package_script }}"

  tasks:
    - name: "Criar diretórios base no target (se ainda não existem)"
      ansible.builtin.file:
        path: "{{ item }}"
        state: directory
        mode: "0755"
        owner: root
        group: root
      loop:
        - "{{ remote_base_dir }}"
        - "{{ remote_pkg_dir }}"
        - "{{ remote_scripts_root }}"
        - "{{ remote_scripts_dir }}"

    - name: "Copiar scripts do package para o target"
      ansible.builtin.copy:
        src: "{{ repo_root }}/scripts/{{ package_script }}/"
        dest: "{{ remote_scripts_dir }}/"
        mode: "0755"

    - name: "Verificar se existe init.sh"
      ansible.builtin.stat:
        path: "{{ remote_scripts_dir }}/init.sh"
      register: st_init

    - name: "Falhar se init.sh nao existir"
      ansible.builtin.fail:
        msg: "init.sh nao encontrado em {{ remote_scripts_dir }}"
      when: not st_init.stat.exists

    # Opcional: evidência rápida do RPM esperado no host
    - name: "Validar que ao menos 1 componente existe no diretório de package"
      ansible.builtin.stat:
        path: "{{ remote_pkg_dir }}/{{ (package_components[0] | default('') | basename) }}"
      register: st_any_rpm
      when: (package_components | length) > 0

    - name: "Falhar se componente esperado não existir"
      ansible.builtin.fail:
        msg: "Componente esperado nao encontrado em {{ remote_pkg_dir }} para o package {{ package_name }}"
      when:
        - (package_components | length) > 0
        - not st_any_rpm.stat.exists

    - name: "Executar init.sh do package ({{ action }})"
      ansible.builtin.shell: |
        cd "{{ remote_base_dir }}"
        bash "scripts/{{ package_script }}/init.sh"
      environment: "{{ env_vars | default({}) }}"
      register: deploy_result
      ignore_errors: true

    - name: "Exibir stdout do init.sh"
      ansible.builtin.debug:
        var: deploy_result.stdout_lines

    - name: "Falhar se init.sh retornou erro"
      ansible.builtin.fail:
        msg: |
          Falha ao executar init.sh do package {{ package_name }} em {{ inventory_hostname }}.
          STDERR:
          {{ deploy_result.stderr }}
      when: deploy_result.rc != 0




----

#!/usr/bin/env bash
set -euo pipefail

# =========================
# Parâmetros da pipeline
# =========================
BRANCH="${BRANCH:-develop}"
ACTION="${ACTION:-deploy}"  # deploy | rollback
DEPLOYMENT_REF="${DEPLOYMENT_REF:?Você precisa definir DEPLOYMENT_REF}"
EXECUTION_FILE="${EXECUTION_FILE:-execution/machine_list_dev.yml}"

# Repo
REPO_URL="${REPO_URL:-<COLE_AQUI_O_URL_DO_SEU_REPO_GIT>}"

echo "== DEPLOY PIPELINE =="
echo "BRANCH         : ${BRANCH}"
echo "ACTION         : ${ACTION}"
echo "DEPLOYMENT_REF : ${DEPLOYMENT_REF}"
echo "EXECUTION_FILE : ${EXECUTION_FILE}"
echo

# =========================
# Garantir chave no bastion/ansible host
# (você disse que a chave usada é id_rsa)
# =========================
if [[ ! -f "${HOME}/.ssh/id_rsa" ]]; then
  echo "ERRO: chave privada não encontrada em ${HOME}/.ssh/id_rsa"
  exit 1
fi
chmod 600 "${HOME}/.ssh/id_rsa"

# =========================
# Clonar repo
# =========================
WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

echo "Repositorio em $(pwd)"
git rev-parse --abbrev-ref HEAD
git rev-parse HEAD
echo

# =========================
# Extrair lista de máquinas do execution file
# Formato esperado:
# machines:
#   - sitef-01
#   - sitef-02
# =========================
if [[ ! -f "${EXECUTION_FILE}" ]]; then
  echo "ERRO: execution file não encontrado: ${EXECUTION_FILE}"
  exit 1
fi

MACHINES="$(grep -E '^\s*-\s*' "${EXECUTION_FILE}" | sed -E 's/^\s*-\s*//')"

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: nenhuma máquina encontrada em ${EXECUTION_FILE}"
  exit 1
fi

echo "Maquinas encontradas:"
echo "${MACHINES}"
echo

# =========================
# Rodar deploy por máquina
# =========================
for MACHINE in ${MACHINES}; do
  echo "===================================================="
  echo "== ${ACTION^^} -> ${MACHINE}"
  echo "===================================================="

  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "machine_name=${MACHINE}" \
    -e "deployment_ref=${DEPLOYMENT_REF}" \
    -e "action=${ACTION}"
done

echo
echo "== Pipeline finalizada com sucesso =="
