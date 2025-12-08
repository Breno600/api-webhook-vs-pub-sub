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