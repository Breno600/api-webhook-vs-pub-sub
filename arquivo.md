#!/bin/bash
set -euo pipefail

# =========================
# INPUTS / DEFAULTS
# =========================

# ⚠️ RECOMENDADO: não deixe tokens hardcoded aqui.
# Deixe o Harness injetar via Secrets/Env Vars.
GIT_TOKEN="${GIT_TOKEN:-xXAiJyF1Hx4noamrBSdV}"

GIT_TAG="${GIT_TAG:-}"
EXECUTION_FILE_NAME="${EXECUTION_FILE_NAME:-execution/machine_list_dev.yml}"
GIT_BRANCH="${GIT_BRANCH:-develop}"

GIT_USER_NAME="${GIT_USER_NAME:-harness-bot}"
GIT_USER_EMAIL="${GIT_USER_EMAIL:-harness-bot@fiserv.com}"

NEXUS_BASE_URL="${NEXUS_BASE_URL:-https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev}"
NEXUS_USER="${NEXUS_USER:-AS4hZF20}"
NEXUS_PASSWORD="${NEXUS_PASSWORD:-l7WwGfJd_Grmh-5Kn7B__U8nqgdNWh1XhrYtVQQ5I_6k}"

# Token do Harness (PAT)
HARNESS_X_API_KEY="${HARNESS_X_API_KEY:-pat.fgDto6qoTT6ctfZS9eWbEw.693f147c43bfca2e849b46f4.WtMpaUZG5pxwDZcIkzl0}"

# Defaults Harness
HARNESS_ENDPOINT="${HARNESS_ENDPOINT:-https://harness.onefiserv.net}"
HARNESS_ACCOUNT_ID="${HARNESS_ACCOUNT_ID:-fgDto6qoTT6ctfZS9eWbEw}"
HARNESS_ORG_ID="${HARNESS_ORG_ID:-Fiserv}"
HARNESS_PROJECT_ID="${HARNESS_PROJECT_ID:-sitef}"

FILESTORE_ENV="${FILESTORE_ENV:-dev}"

export ANSIBLE_HOST_KEY_CHECKING=False

# =========================
# VALIDATIONS
# =========================
if [[ -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TAG está vazio. Ex: DEV000011"
  exit 1
fi

if [[ -z "${GIT_TOKEN}" ]]; then
  echo "ERRO: GIT_TOKEN está vazio (injete via Secret/Env no Harness)."
  exit 1
fi

if [[ -z "${NEXUS_USER}" || -z "${NEXUS_PASSWORD}" ]]; then
  echo "ERRO: NEXUS_USER/NEXUS_PASSWORD estão vazios (injete via Secret/Env)."
  exit 1
fi

if [[ -z "${HARNESS_X_API_KEY}" ]]; then
  echo "ERRO: HARNESS_X_API_KEY está vazio (injete via Secret/Env)."
  exit 1
fi

if [[ -z "${HARNESS_ACCOUNT_ID}" || -z "${HARNESS_ORG_ID}" || -z "${HARNESS_PROJECT_ID}" ]]; then
  echo "ERRO: HARNESS_ACCOUNT_ID/HARNESS_ORG_ID/HARNESS_PROJECT_ID estão vazios (injete via Env)."
  exit 1
fi

# =========================
# REPO
# =========================
REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

WORKDIR="$(mktemp -d)"
trap 'rm -rf "$WORKDIR"' EXIT

echo "Clonando repo em ${WORKDIR}..."
git clone --branch "${GIT_BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"
git checkout $GIT_TAG

# Tags / refs
git fetch --tags --force

# (Opcional) identity pro git se algum step precisar commitar/tag
git config user.name  "${GIT_USER_NAME}"
git config user.email "${GIT_USER_EMAIL}"

echo
echo "== PIPELINE PREDEPLOY =="
echo "TAG   : ${GIT_TAG}"
echo "EXEC  : ${EXECUTION_FILE_NAME}"
echo "BRANCH: ${GIT_BRANCH}"
echo

cd ansible

# =========================
# EXPORTS para o Ansible (env)
# =========================
export NEXUS_BASE_URL NEXUS_USER NEXUS_PASSWORD FILESTORE_ENV

# Padrão "HARNESS_*" (alguns playbooks usam isso)
export HARNESS_ENDPOINT HARNESS_ACCOUNT_ID HARNESS_ORG_ID HARNESS_PROJECT_ID

# Mapeia o token pro nome esperado por alguns fluxos
export HARNESS_API_KEY="${HARNESS_API_KEY:-$HARNESS_X_API_KEY}"

# ✅ CRÍTICO: mapeamento para o padrão que o seu harness_filestore_upload.yml está cobrando no log:
# "Garanta HARNESS_ENDPOINT/ACC/ORG/PROJ/TOKEN no ambiente."
export ACC="${ACC:-$HARNESS_ACCOUNT_ID}"
export ORG="${ORG:-$HARNESS_ORG_ID}"
export PROJ="${PROJ:-$HARNESS_PROJECT_ID}"
export TOKEN="${TOKEN:-$HARNESS_API_KEY}"

# (Opcional) se seu upload usa parentIdentifier
export PARENT_IDENTIFIER="${PARENT_IDENTIFIER:-Root}"

# =========================
# RUN
# =========================
ansible-playbook -i "localhost," -c local predeploy_from_execution.yml \
  -e "execution_file_name=${EXECUTION_FILE_NAME}" \
  -e "deployment_ref=${GIT_TAG}" \
  -e "nexus_base_url=${NEXUS_BASE_URL}" \
  -e "nexus_user=${NEXUS_USER}" \
  -e "nexus_password=${NEXUS_PASSWORD}" \
  -e "filestore_env=${FILESTORE_ENV}" \
  -e "stage_name=predeploy" \
  --forks 10
