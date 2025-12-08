#!/usr/bin/env bash
set -euo pipefail

# =========================
# Parâmetros vindos do Harness
# =========================
# Esperado que o Harness injete:
#   BRANCH, STRATEGY, GIT_TAG, MACHINES ou MACHINE
BRANCH="${BRANCH:-develop}"

# deploy | rollback
STRATEGY="${STRATEGY:-${ACTION:-deploy}}"

# Tag de execução (ex: DEV0001)
GIT_TAG="${GIT_TAG:-}"

# Lista de máquinas (preferencial) ou máquina única
MACHINES="${MACHINES:-${MACHINE:-}}"

# =========================
# Segurança: token NUNCA hardcoded
# =========================
# Ideal: usar Secret do Harness e injetar em GIT_TOKEN
GIT_TOKEN="${GIT_TOKEN:-}"

if [[ -z "${GIT_TOKEN}" ]]; then
  echo "ERRO: GIT_TOKEN não informado via secret/env do Harness."
  exit 1
fi

REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

echo "== DEPLOY PIPELINE =="
echo "BRANCH   : ${BRANCH}"
echo "STRATEGY : ${STRATEGY}"
echo "GIT_TAG  : ${GIT_TAG}"
echo "MACHINES : ${MACHINES}"
echo

# =========================
# Validações
# =========================
if [[ -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TAG vazio."
  exit 1
fi

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: nenhuma máquina informada (use MACHINES ou MACHINE)."
  exit 1
fi

# =========================
# Garantir chave no bastion/ansible host
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

echo "Maquinas encontradas:"
echo "${MACHINES}"
echo

# =========================
# Rodar deploy por máquina
# =========================
for M in ${MACHINES}; do
  echo "===================================================="
  echo "== ${STRATEGY^^} -> ${M}"
  echo "===================================================="

  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "machine_name=${M}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "deploy_action=${STRATEGY}"
done

echo
echo "== Pipeline finalizada com sucesso =="
