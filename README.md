#!/usr/bin/env bash
set -euo pipefail

# =========================================================
# DEFAULTS / INPUTS DO HARNESS
# =========================================================
BRANCH="${BRANCH:-develop}"
STRATEGY="${STRATEGY:-deploy}"        # deploy | rollback
GIT_TAG="${GIT_TAG:-}"
MACHINES="${MACHINES:-${MACHINE:-}}"

# >>> CORREÇÃO DO ERRO (unbound variable)
FILESTORE_ENV="${FILESTORE_ENV:-dev}"
FILESTORE_BASE_DIR="${FILESTORE_BASE_DIR:-${FILESTORE_ENV}/${GIT_TAG}}"

# =========================================================
# GIT / HARNESS
# =========================================================
GIT_TOKEN="xXAiJyF1Hx4noamrBSdV"
REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

HARNESS_X_API_KEY="${HARNESS_X_API_KEY:-pat.fgDto6qoTT6ctfZS9eWbEw.693f147c43bfca2e849b46f4.WtMpaUZG5pxwDZcIkzl0}"

# =========================================================
# HEADER
# =========================================================
echo "== DEPLOY PIPELINE =="
echo "BRANCH            : ${BRANCH}"
echo "STRATEGY          : ${STRATEGY}"
echo "GIT_TAG           : ${GIT_TAG}"
echo "MACHINES          : ${MACHINES}"
echo "FILESTORE_ENV     : ${FILESTORE_ENV}"
echo "FILESTORE_BASEDIR : ${FILESTORE_BASE_DIR}"
echo

# =========================================================
# VALIDAÇÕES
# =========================================================
if [[ -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TAG nao informado"
  exit 1
fi

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: MACHINES/MACHINE vazio"
  exit 1
fi

# =========================================================
# NORMALIZA LISTA DE MÁQUINAS
# - aceita .yml/.yaml
# - aceita espaço ou vírgula
# =========================================================
MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  MACHINES_CLEAN+="${m} "
done

echo "MACHINES_NORMALIZADAS: ${MACHINES_CLEAN}"
echo

# =========================================================
# CLONE DO REPO
# =========================================================
WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

# =========================================================
# LOOP POR MÁQUINA
# =========================================================
for m in ${MACHINES_CLEAN}; do
  echo "===================================================="
  echo "== ${STRATEGY^^} -> ${m}"
  echo "===================================================="

  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "machine_name=${m}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "deploy_action=${STRATEGY}" \
    -e "filestore_env=${FILESTORE_ENV}" \
    -e "filestore_base_dir=${FILESTORE_BASE_DIR}" \
    -e "harness_x_api_key=${HARNESS_X_API_KEY}"

done

echo
echo "== Pipeline finalizada com sucesso =="
