#init.sh

#!/bin/bash
set -euo pipefail

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
LOG_FILE="${SCRIPT_DIR}/deploy.txt"

# Tudo que sair em stdout/stderr vai pro console e pro arquivo
exec > >(tee -a "$LOG_FILE") 2>&1

echo "== Início init.sh =="
date

set -x

echo "Instalacao de dependencias do sistema..."
dnf install -y --nodocs python3 python3-pip
echo "Instalacao de dependencias do sistema finalizada."

# Garante diretório de setup
mkdir -p /opt/SoftwareExpress/sitef-setup

# Venv padrão do fluxo
python3 -m venv /opt/SoftwareExpress/sitef-setup/venv
source /opt/SoftwareExpress/sitef-setup/venv/bin/activate

echo "Instalacao de dependencias do python..."
# Se um dia precisar:
# pip3 install <dependencia>
echo "Instalacao de dependencias do python finalizada."

# Execução do deploy real
python3 -u script_deploy.py
RC=$?

echo "script_deploy.py finalizado com rc=${RC}"

deactivate

set +x

if [ $RC -ne 0 ]; then
  echo "Script finalizou com erro."
  exit 1
fi

echo "== Fim init.sh =="
date


# scripts/deploy-sitef-0.0.2/script_deploy.py
import sys
import backup
import sitef as sitef_module
import sitef_packages as sitef_packages_module

sitef = sitef_module.sitef()
sitef_packages = sitef_packages_module.sitef_packages()

print("== Início script_deploy.py ==")
print(f"Root path  : {sitef.root_path()}")
print(f"Setup path : {sitef.setup_path()}")
print(f"Config path: {sitef.config_path()}")

print("Verificando presenca do servico sitef...")
if sitef.service_exists():
    print("Serviço sitef existe.")
    print("Parando servico sitef...")
    if not sitef.service_stop():
        print("Falha ao parar servico sitef.")
        sys.exit(1)
else:
    print("Serviço sitef não encontrado. Seguindo instalação mesmo assim.")

# (Opcional) caso você queira garantir backup também no deploy:
# print("Executando backup pre-install...")
# if not backup.backup().prepare():
#     print("Falha na operacao de backup.")
#     sys.exit(1)
# print("Backup finalizado.")

print("Executando instalacao de pacotes...")
if not sitef_packages.install():
    print("Falha na operacao de instalacao.")
    sys.exit(1)

installed = sitef_packages.get_installed()
print(f"Pacotes instalados ({len(installed)}): {installed}")

for pack in installed:
    if pack.startswith("aplicpatchprods"):
        print("Executando atualizacao do prods...")
        if not sitef.run_aplicpatchprods():
            print("Falha na execucao do aplicpatchprods.")
            sys.exit(1)

print("Validando serviço pós-instalação...")
if sitef.service_exists():
    print("Iniciando servico sitef...")
    if not sitef.service_start():
        print("Falha ao iniciar servico sitef.")
        sys.exit(1)
else:
    print("Serviço sitef ainda não existe após instalação (ok dependendo do pacote).")

print("== Fim script_deploy.py ==")

## scripts/deploy-sitef-0.0.2/sitef.py
from sys import platform
import subprocess
import json
import os

class sitef:
    """
    Classe para acesso a atributos e tratamentos relacionados ao SiTef.
    """

    def __init__(self):
        if platform in ["linux", "linux2"]:
            self._root_path = '/opt/SoftwareExpress/sitef'
            self._setup_path = '/opt/SoftwareExpress/sitef-setup'
        else:
            self._root_path = '/sitef'
            self._setup_path = '/sitef-setup'

        self._metadata = None
        self._metadata_path = f'{self._setup_path}/metadata.json'

    def root_path(self):
        return self._root_path

    def config_path(self):
        return f'{self._root_path}/config'

    def bin_path(self):
        if platform in ["linux", "linux2"]:
            return f'{self._root_path}/bin'
        else:
            return f'{self._root_path}/aplic.win'

    def setup_path(self):
        return self._setup_path

    def load_metadata(self):
        self._metadata = {}

        if os.path.exists(self._metadata_path):
            with open(self._metadata_path, 'r', encoding='utf-8') as f:
                self._metadata = json.load(f)

        return self._metadata

    def write_metadata(self, metadata):
        self._metadata = metadata

        os.makedirs(self._setup_path, exist_ok=True)

        with open(self._metadata_path, 'w', encoding='utf-8') as f:
            json.dump(metadata, f, ensure_ascii=False, indent=2)

        return self._metadata

    def service_exists(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(
                    ['systemctl', 'list-unit-files', 'sitef.service', '--quiet'],
                    capture_output=True,
                    check=False
                )
                return result.returncode == 0
            except Exception as e:
                print(f'Falha verificacao de servico SiTef: {e}')
                return False
        return False

    def service_start(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(
                    ['systemctl', 'start', 'sitef.service', '--quiet'],
                    capture_output=True,
                    check=False
                )
                return result.returncode == 0
            except Exception as e:
                print(f'Falha start de servico SiTef: {e}')
                return False
        return False

    def service_stop(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(
                    ['systemctl', 'stop', 'sitef.service', '--quiet'],
                    capture_output=True,
                    check=False
                )
                return result.returncode == 0
            except Exception as e:
                print(f'Falha stop de servico SiTef: {e}')
                return False
        return False

    def run_aplicpatchprods(self, create=True):
        if platform in ["linux", "linux2"]:
            try:
                binary_path = f'{self.bin_path()}/aplicpatchprods'
                if create:
                    result = subprocess.run(['sudo', '-u', 'sitefd', binary_path, 'create=1'], check=False)
                else:
                    result = subprocess.run(['sudo', '-u', 'sitefd', binary_path], check=False)
                return result.returncode == 0
            except Exception as e:
                print(f'Falha na execucao do aplicpatchprods: {e}')
                return False
        return False

## scripts/deploy-sitef-0.0.2/sitef_packages.py

from sys import platform
import subprocess
import os

class sitef_packages:

    def __init__(self):
        # Diretório real no host onde o predeploy baixou/copou os RPMs
        if platform in ["linux", "linux2"]:
            self._package_dir = "/opt/SoftwareExpress/sitef/package/linux"
        else:
            self._package_dir = "packages/windows"

        self._installed = []

    def install(self, rollback=False):
        if platform in ["linux", "linux2"]:
            if not os.path.isdir(self._package_dir):
                print(f"Diretório de pacotes não existe: {self._package_dir}")
                return False

            for name in os.listdir(self._package_dir):
                path = os.path.join(self._package_dir, name)

                if os.path.isfile(path) and name.endswith('.rpm'):
                    print(f"Instalando: {path}")
                    result = subprocess.run(['dnf', 'install', '-y', path], check=False)
                    if result.returncode != 0:
                        print(f"Falha instalando RPM: {name}")
                        return False
                    self._installed.append(name)

            return True

        return False

    def get_installed(self):
        return self._installed


# ansible/deploy_from_status.yml
# ansible/deploy_from_status.yml
#
# PIPELINE 2 - DEPLOY (PARTE PESADA)
# - Lê execution/<arquivo>.yml (lista de máquinas)
# - Para cada máquina chama deploy_per_machine.yml
# - Cada máquina ganha um JSON em status/<TAG>/deploy-<machine>.json

- name: "Deploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

  tasks:

    - name: "Mostrar variáveis de entrada"
      ansible.builtin.debug:
        msg:
          - "execution_file_name = {{ execution_file_name }}"
          - "deployment_ref     = {{ deployment_ref }}"
          - "repo_root          = {{ repo_root }}"
          - "status_dir         = {{ status_dir }}"

    - name: "Criar diretório de status da TAG (se não existir)"
      ansible.builtin.file:
        path: "{{ status_dir }}"
        state: directory
        mode: "0755"

    - name: "Carregar arquivo de execução"
      ansible.builtin.include_vars:
        file: "{{ repo_root }}/{{ execution_file_name }}"
        name: execution_cfg

    - name: "Exibir lista de máquinas"
      ansible.builtin.debug:
        var: execution_cfg.machines

    - name: "Falhar se não tiver máquinas no arquivo de execução"
      ansible.builtin.fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: >
        execution_cfg.machines is not defined or
        execution_cfg.machines | length == 0

    - name: "Executar deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name


# ansible/deploy_per_machine.yml
# ansible/deploy_per_machine.yml
#
# Tarefas executadas para CADA máquina no deploy.
#
# Reaproveita o mesmo padrão do predeploy:
# - carrega machine/<nome>.yml
# - carrega package/<package>.yml
# - add_host dinâmico
# - executa init.sh NO HOST ALVO
# - lê deploy.txt
# - grava status JSON

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/deploy-{{ machine_name }}.json"
    host_base_dir: "/opt/SoftwareExpress/sitef"
    host_pkg_dir: "/opt/SoftwareExpress/sitef/package/linux"
    host_scripts_dir: "/opt/SoftwareExpress/sitef/scripts"

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
        "timestamp": "{{ ansible_date_time.iso8601 }}"
      }

# Opcional: validar se o predeploy ocorreu antes
- name: "Verificar se status de predeploy existe"
  ansible.builtin.stat:
    path: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
  register: predeploy_status_stat

- name: "Avisar se predeploy não foi encontrado (não bloqueante)"
  ansible.builtin.debug:
    msg: "ATENÇÃO: predeploy-{{ machine_name }}.json não encontrado. Deploy continuará mesmo assim."
  when: not predeploy_status_stat.stat.exists

# Garantir diretório scripts do pacote no host alvo
- name: "Garantir diretórios do deploy no host {{ machine_cfg.host }}"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
    owner: root
    group: root
  loop:
    - "{{ host_base_dir }}"
    - "{{ host_pkg_dir }}"
    - "{{ host_scripts_dir }}"
    - "{{ host_scripts_dir }}/{{ package_cfg.script }}"
  delegate_to: "{{ machine_name }}"

# Executar init.sh no host alvo
- name: "Executar script de deploy (init.sh) em {{ machine_name }}"
  become: true
  ansible.builtin.shell: |
    cd "{{ host_scripts_dir }}/{{ package_cfg.script }}"
    ./init.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ machine_cfg.env_vars | default({}) }}"
  register: deploy_result
  ignore_errors: true

- name: "DEBUG - stdout do init.sh"
  ansible.builtin.debug:
    var: deploy_result.stdout_lines

- name: "DEBUG - stderr do init.sh"
  ansible.builtin.debug:
    var: deploy_result.stderr_lines

# Ler deploy.txt do host alvo
- name: "Ler deploy.txt do host alvo"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_scripts_dir }}/{{ package_cfg.script }}/deploy.txt" || echo "deploy.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: deploy_log
  changed_when: false

- name: "DEBUG - conteúdo do deploy.txt"
  ansible.builtin.debug:
    var: deploy_log.stdout_lines

# Atualizar status final
- name: "Atualizar status para success de {{ machine_name }}"
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
        "stdout": {{ deploy_result.stdout | to_json }},
        "stderr": {{ deploy_result.stderr | to_json }},
        "deploy_log": {{ deploy_log.stdout | to_json }}
      }
  when: deploy_result is succeeded

- name: "Atualizar status para failed de {{ machine_name }}"
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
        "stdout": {{ deploy_result.stdout | to_json }},
        "stderr": {{ deploy_result.stderr | to_json }},
        "deploy_log": {{ deploy_log.stdout | to_json }}
      }
  when: deploy_result is failed

# Se quiser falhar o pipeline quando der erro:
- name: "Falhar pipeline se init.sh retornou erro"
  ansible.builtin.fail:
    msg: "Deploy falhou em {{ machine_name }} (host {{ machine_cfg.host }}). Verifique deploy.txt e status JSON."
  when: deploy_result is failed
