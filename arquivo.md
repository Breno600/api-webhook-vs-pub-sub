segue meu script_deploy.py atual.

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
