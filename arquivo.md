#!/usr/bin/env bash
set -euo pipefail

# =========================================================
# INPUTS / DEFAULTS (Harness Variables)
# =========================================================
BRANCH="${BRANCH:-develop-testes}"
STRATEGY="${STRATEGY:-deploy}"              # deploy | rollback
GIT_TAG="${GIT_TAG:-}"                      # ex: DEV000001
MACHINES="${MACHINES:-${MACHINE:-}}"        # "sitef-01,sitef-02" ou "sitef-01 sitef-02"

FILESTORE_ENV="${FILESTORE_ENV:-dev}"
FILESTORE_BASE_DIR="${FILESTORE_BASE_DIR:-${FILESTORE_ENV}/${GIT_TAG}}"

# Repo (injete GIT_TOKEN no Harness)
GIT_TOKEN="${GIT_TOKEN:-}"
REPO_URL="${REPO_URL:-https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git}"

# Harness token (injete HARNESS_X_API_KEY no Harness)
HARNESS_X_API_KEY="${HARNESS_X_API_KEY:-}"

# Defaults Harness (opcionais: o upload tem default, mas é bom setar)
HARNESS_ACCOUNT_ID="${HARNESS_ACCOUNT_ID:-fgDto6qoTT6ctfZS9eWbEw}"
HARNESS_ORG_ID="${HARNESS_ORG_ID:-Fiserv}"
HARNESS_PROJECT_ID="${HARNESS_PROJECT_ID:-sitef}"
HARNESS_ENDPOINT="${HARNESS_ENDPOINT:-https://harness.onefiserv.net}"

# Nexus (se seu playbook resolver Nexus via env)
NEXUS_BASE_URL="${NEXUS_BASE_URL:-}"
NEXUS_USER="${NEXUS_USER:-}"
NEXUS_PASSWORD="${NEXUS_PASSWORD:-}"

export ANSIBLE_HOST_KEY_CHECKING=False

# =========================================================
# HEADER
# =========================================================
echo "== DEPLOY/ROLLBACK PIPELINE =="
echo "BRANCH            : ${BRANCH}"
echo "STRATEGY          : ${STRATEGY}"
echo "GIT_TAG           : ${GIT_TAG}"
echo "MACHINES          : ${MACHINES}"
echo "FILESTORE_ENV     : ${FILESTORE_ENV}"
echo "FILESTORE_BASEDIR : ${FILESTORE_BASE_DIR}"
echo

# =========================================================
# VALIDATIONS
# =========================================================
if [[ -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TAG nao informado (ex: DEV000001)"
  exit 1
fi

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: MACHINES/MACHINE vazio (ex: sitef-02)"
  exit 1
fi

if [[ -z "${GIT_TOKEN}" ]]; then
  echo "ERRO: GIT_TOKEN vazio (injete via Secret/Env no Harness)."
  exit 1
fi

if [[ -z "${HARNESS_X_API_KEY}" ]]; then
  echo "ERRO: HARNESS_X_API_KEY vazio (injete via Secret/Env no Harness)."
  exit 1
fi

if [[ "${STRATEGY}" != "deploy" && "${STRATEGY}" != "rollback" ]]; then
  echo "ERRO: STRATEGY invalida. Use deploy ou rollback."
  exit 1
fi

# =========================================================
# EXPORTS pro Ansible / Upload
# =========================================================
# ✅ CRÍTICO: seu harness_filestore_upload.yml procura HARNESS_API_KEY/HARNESS_PAT/HARNESS_TOKEN
export HARNESS_API_KEY="${HARNESS_API_KEY:-$HARNESS_X_API_KEY}"

# (Recomendado) setar padrão Harness no ambiente
export HARNESS_ENDPOINT HARNESS_ACCOUNT_ID HARNESS_ORG_ID HARNESS_PROJECT_ID

# Se seus playbooks usam Nexus via env
if [[ -n "${NEXUS_BASE_URL}" ]]; then export NEXUS_BASE_URL; fi
if [[ -n "${NEXUS_USER}" ]]; then export NEXUS_USER; fi
if [[ -n "${NEXUS_PASSWORD}" ]]; then export NEXUS_PASSWORD; fi

# =========================================================
# NORMALIZA LISTA DE MÁQUINAS
# - aceita .yml/.yaml
# - aceita espaço ou vírgula
# =========================================================
MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  m="$(echo "${m}" | xargs)"   # trim
  [[ -z "${m}" ]] && continue
  MACHINES_CLEAN+="${m} "
done

echo "MACHINES_NORMALIZADAS: ${MACHINES_CLEAN}"
echo

# =========================================================
# CLONE DO REPO
# =========================================================
WORKDIR="$(mktemp -d)"
trap 'rm -rf "$WORKDIR"' EXIT

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

  # DICA: se você quiser forçar stage_name no topo (pra evitar variáveis vazias):
  # -e "stage_name=${STRATEGY}"

  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "machine_name=${m}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "deploy_action=${STRATEGY}" \
    -e "filestore_env=${FILESTORE_ENV}" \
    -e "filestore_base_dir=${FILESTORE_BASE_DIR}" \
    -e "harness_api_key_override=${HARNESS_API_KEY}"
done

echo
echo "== Pipeline finalizada com sucesso =="
