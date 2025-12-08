## sitef_packages.py

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

## script_deploy.py
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


