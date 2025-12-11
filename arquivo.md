# PIPELINE 2 - DEPLOY
# - Recebe machine_name (ou lista externa via loop no script)
# - Reusa área /deploy/scripts preparada no predeploy
# - Executa init_deploy.sh
# - Atualiza status
# - Upload JSON + LOG no File Store

- name: "Deploy por máquina"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    machine_name: "{{ machine_name | default('') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"
    filestore_env: "{{ filestore_env | default('dev') }}"
    filestore_base_dir: "{{ filestore_base_dir | default(filestore_env ~ '/' ~ deployment_ref) }}"

  tasks:
    - name: "Validar machine_name"
      ansible.builtin.assert:
        that:
          - machine_name is defined
          - machine_name | length > 0
        fail_msg: "machine_name não informado"

    - name: "Incluir deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      vars:
        deployment_ref: "{{ deployment_ref }}"
        machine_name: "{{ machine_name }}"
        repo_root: "{{ repo_root }}"
        status_dir: "{{ status_dir }}"
        filestore_env: "{{ filestore_env }}"
        filestore_base_dir: "{{ filestore_base_dir }}"


# Tarefas por máquina - DEPLOY

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/deploy-{{ machine_name }}.json"

    host_deploy_scripts: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"

- name: "Carregar config da máquina"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

- name: "Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default('ec2-user') }}"

- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    target_ssh_common_args: >-
      {{
        machine_cfg.ssh_common_args
          | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null')
      }}

- name: "Registrar host dinâmico"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"

- name: "Montar env final"
  ansible.builtin.set_fact:
    effective_env: "{{ (package_cfg.env_vars | default({})) | combine(machine_cfg.env_vars | default({}), recursive=True) }}"

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
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }

- name: "Verificar init_deploy.sh"
  become: true
  ansible.builtin.stat:
    path: "{{ host_deploy_scripts }}/init_deploy.sh"
  delegate_to: "{{ machine_name }}"
  register: deploy_script_stat

- name: "Falhar se init_deploy.sh não existir"
  ansible.builtin.fail:
    msg: "init_deploy.sh não encontrado em {{ host_deploy_scripts }}. Rode o predeploy antes."
  when: not deploy_script_stat.stat.exists

- name: "Executar init_deploy.sh"
  become: true
  ansible.builtin.shell: |
    set -e
    cd "{{ host_deploy_scripts }}"
    /bin/bash -x ./init_deploy.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ effective_env }}"
  register: deploy_result
  ignore_errors: true

- name: "Ler deploy.txt do host"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_deploy_scripts }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: deploy_log
  changed_when: false

- name: "Atualizar status para success"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ deploy_result.rc | default(0) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "script_log": {{ deploy_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: deploy_result.rc == 0

- name: "Atualizar status para failed"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "stage": "deploy",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "rc": {{ deploy_result.rc | default(1) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "script_log": {{ deploy_log.stdout | default('') | to_json }},
        "log_path": "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log"
      }
  when: deploy_result.rc != 0

- name: "Upload status JSON para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "{{ status_file }}" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.json"
  changed_when: false
  failed_when: false

- name: "Upload log do deploy para Harness File Store"
  ansible.builtin.shell: |
    "{{ repo_root }}/harness/upload_filestore.sh" \
      "-" \
      "{{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ machine_name }}-{{ filestore_env }}.log" \
      <<'EOF'
    ---- pipeline deploy ----
    {{ deploy_result.stdout | default('') }}
    {{ deploy_result.stderr | default('') }}
    ---- deploy.txt ----
    {{ deploy_log.stdout | default('') }}
    ---- pipeline deploy ----
    EOF
  args:
    executable: /bin/bash
  changed_when: false
  failed_when: false

- name: "Falhar pipeline se init_deploy.sh retornou erro"
  ansible.builtin.fail:
    msg: "DEPLOY falhou em {{ machine_name }} (host {{ machine_cfg.host }})"
  when: deploy_result.rc != 0

# harness scripts
#!/usr/bin/env bash
set -euo pipefail

BRANCH="${BRANCH:-develop}"
STRATEGY="${STRATEGY:-deploy}"      # deploy | rollback
GIT_TAG="${GIT_TAG:-}"
MACHINES="${MACHINES:-${MACHINE:-}}"
GIT_TOKEN="xXAiJyF1Hx4noamrBSdV"
REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"


echo "== DEPLOY PIPELINE =="
echo "BRANCH   : ${BRANCH}"
echo "STRATEGY : ${STRATEGY}"
echo "GIT_TAG  : ${GIT_TAG}"
echo "MACHINES : ${MACHINES}"
echo

if [[ -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TAG nao informado"
  exit 1
fi

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: MACHINES/MACHINE vazio"
  exit 1
fi

# Normaliza lista:
# - aceita "sitef-01.yml", "sitef-01.yaml"
# - aceita separado por espaço ou vírgula
MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  MACHINES_CLEAN+="${m} "
done

echo "MACHINES_NORMALIZADAS: ${MACHINES_CLEAN}"
echo

WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

for m in ${MACHINES_CLEAN}; do
  echo "===================================================="
  echo "== ${STRATEGY^^} -> ${m}"
  echo "===================================================="

  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "machine_name=${m}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "deploy_action=${STRATEGY}"
done

echo
echo "== Pipeline finalizada com sucesso =="


# fluxo:

deploy:
- exportar as variáveis em tempo de execução
-----
package.yml
env_vars:
  PACKAGE_VERSION: 'sitef-0.0.20'
-----
machine.yml
env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"
-----
- entrar na pasta do scripts e chamar o init_deploy.sh  /opt/SoftwareExpress/sitef-pipeline/deploy/scripts
