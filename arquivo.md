#init.sh

#!/bin/bash
echo "Instalacao de dependencias do sistema..."

# Colocar aqui dependencias de sistema que ser�o necess�rias para a aplica��o e instala��o
dnf install -y --nodocs python3 python3-pip

echo "Instalacao de dependencias finalizada."

python3 -m venv /opt/SoftwareExpress/sitef-setup/venv

source /opt/SoftwareExpress/sitef-setup/venv/bin/activate

echo "Instalacao de dependencias do python..."

# Colocar aqui dependencias do python que ser�o necess�rias para a aplicacao e instalacao
# pip3 install <dependencia>

echo "Instalacao de dependencias finalizada."

python3 script_deploy.py

if [ $? -ne 0 ]; then
  echo "Script finalizou com erro."
  exit 1
fi

deactivate


#script_deploy.py
import backup
import sitef
import sitef_packages

sitef = sitef.sitef()
sitef_packages = sitef_packages.sitef_packages()

print('Verificando presenca do servico sitef...')
if sitef.service_exists():
    print('Parando servico sitef...')
    sitef.service_stop()

# Executa a instalacao dos pacotes baixados.
if not sitef_packages.install():
    print('Falha na operacao de instalacao.')
    exit(1)

# Faz tratamentos especificos condicionados a instalacao de um pacote.
for pack in sitef_packages.get_installed():
    if pack.startswith('aplicpatchprods'):
        print('Executando atualizacao do prods...')
        sitef.run_aplicpatchprods()

if sitef.service_exists():
    print('Iniciando servico sitef...')
    sitef.service_start()

#sitef.py
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

        with open(self._metadata_path, 'w', encoding='utf-8') as f:
            json.dump(metadata, f, ensure_ascii=False, indent=2)

        return self._metadata

    def service_exists(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(['systemctl', 'list-unit-files', 'sitef.service', '--quiet'], capture_output=True, check=False)
                return result.returncode == 0
            except Exception as e:
                print(f'Falha verificacao de servico SiTef: {e}')
                return False
        return False

    def service_start(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(['systemctl', 'start', 'sitef.service', '--quiet'], capture_output=True, check=False)
                return result.returncode == 0
            except Exception as e:
                print(f'Falha start de servico SiTef: {e}')
                return False
        return False

    def service_stop(self):
        if platform in ["linux", "linux2"]:
            try:
                result = subprocess.run(['systemctl', 'stop', 'sitef.service', '--quiet'], capture_output=True, check=False)
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

#sitef_packages.py

from sys import platform
import subprocess
import os

class sitef_packages:

    def __init__(self):
        # Diretorio padrao onde é esperado que estejam os pacotes copiados do repositorio (Nexus)
        if platform in ["linux", "linux2"]:
            self._package_paths = 'packages/linux'
        else:
            self._package_paths = 'packages/windows'

        # Lista de pacotes instalados, usado para permitir tratamentos adicionais condicionados
        self._installed = []

    def install(self, rollback=False):
        if platform in ["linux", "linux2"]:
            for name in os.listdir(self._package_paths):
                path = os.path.join(self._package_paths, name)
                # Faz a instalação de um rpm, opção pelo uso do dnf por tratar eventuais dependencias
                if os.path.isfile(path) and name.endswith('.rpm'):
                    print(f'Instalando: {path}')
                    result = subprocess.run(['dnf', 'install', '-y', path], check=False)
                    if result.returncode != 0:
                        return False
                    # Acrescenta na lista de pacotes instalados
                    self._installed.append(name)
            return True
        return False

    def get_installed(self):
        return self._installed

#deploy_from_status.py
# ansible/predeploy_from_execution.yml
#
# PIPELINE 1 - PREDEPLOY (PARTE LEVE)
# - Lê execution/<arquivo>.yml (lista de máquinas)
# - Para cada máquina chama predeploy_per_machine.yml (em loop)
# - Cada máquina ganha um JSON em status/<TAG>/predeploy-<machine>.json

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true   # preciso de ansible_date_time p/ timestamp

  vars:
    # Vêm da pipeline via -e
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

    # Nexus
    nexus_base_url: "https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"
    nexus_user: "{{ nexus_user | default('AS4hZF20') }}"
    nexus_password: "{{ nexus_password | default('l7WwGfJd_Grmh-5Kn7B__U8nqgdNWh1XhrYtVQQ5I_6k') }}"

  tasks:

    - name: "Mostrar variáveis de entrada"
      ansible.builtin.debug:
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

    # Carrega o arquivo de execução
    # execution/machine_list_dev.yml
    # machines:
    #   - sitef-01
    #   - sitef-02
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

    # ------------------------------------------------------------------
    # Loop chamando o arquivo de tarefas por máquina
    # ------------------------------------------------------------------
    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name
      vars:
        nexus_base_url: "{{ nexus_base_url }}"
        nexus_user: "{{ nexus_user }}"
        nexus_password: "{{ nexus_password }}"
