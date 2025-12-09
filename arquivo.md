#machine/sitef-01.yml
host: "100.99.25.45"

package: "sitef-core-0.0.2-0"
rollback: "sitef-core-0.0.2-0-rollback"

env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"

tags:
  - name: sitef-2
  - environment: aws-dev
  - aws-account: 942632789850

# package\sitef-core-0.0.2-0.yml
script: deploy-sitef-0.0.2

components:
  - packages/linux/sitef-core-0.0.2-0.x86_64.rpm

env_vars:
  PACKAGE_VERSION: "sitef-core-0.0.2-0"

# package\sitef-core-0.0.2-0-rollback.yml
script: rollback-sitef-0.0.2

components:
  - packages/linux/sitef-core-0.0.1-0.x86_64.rpm

env_vars:
  PACKAGE_VERSION: "sitef-core-0.0.2-0"
  
# scripts\deploy-sitef-0.0.2\init_parallel.sh
#!/bin/bash
set -euo pipefail

SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
LOG_FILE="${SCRIPT_DIR}/parallel.txt"

# Tudo que o script imprimir vai para o console e para o arquivo
exec > >(tee -a "$LOG_FILE") 2>&1

echo "== Início init_parallel.sh =="
date

# Mostra comandos executados
set -x

echo "Instalacao de dependencias do sistema..."
dnf install -y --nodocs python3 python3-pip

python3 -m venv /opt/SoftwareExpress/sitef-setup/venv
source /opt/SoftwareExpress/sitef-setup/venv/bin/activate

echo "Instalacao de dependencias do python..."

# -u ajuda a não "bufferizar" logs do python
python3 -u script_prepare.py

echo "script_prepare.py finalizado com rc=$?"

deactivate

set +x
echo "== Fim init_parallel.sh =="
date

# scripts\deploy-sitef-0.0.2\init_deploy.sh

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

