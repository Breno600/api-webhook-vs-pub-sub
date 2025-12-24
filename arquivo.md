#!/bin/bash
set -euo pipefail

# =========================
# INPUTS / DEFAULTS
# =========================
GIT_TOKEN="${GIT_TOKEN:-xXAiJyF1Hx4noamrBSdV}"

GIT_TAG="${GIT_TAG:-}"
EXECUTION_FILE_NAME="${EXECUTION_FILE_NAME:-execution/machine_list_dev.yml}"
GIT_BRANCH="${GIT_BRANCH:-develop-testes}"

GIT_USER_NAME="${GIT_USER_NAME:-harness-bot}"
GIT_USER_EMAIL="${GIT_USER_EMAIL:-harness-bot@fiserv.com}"

NEXUS_BASE_URL="${NEXUS_BASE_URL:-https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev}"
NEXUS_USER="${NEXUS_USER:-AS4hZF20}"
NEXUS_PASSWORD="${NEXUS_PASSWORD:-l7WwGfJd_Grmh-5Kn7B__U8nqgdNWh1XhrYtVQQ5I_6k}"

# Você já usava HARNESS_X_API_KEY no pipeline.
# O Ansible está esperando HARNESS_API_KEY (env) ou harness_api_key (var).
HARNESS_X_API_KEY="${HARNESS_X_API_KEY:-pat.fgDto6qoTT6ctfZS9eWbEw.693f147c43bfca2e849b46f4.WtMpaUZG5pxwDZcIkzl0}"

# Defaults Harness (ajuste se quiser deixar tudo via variáveis do Harness)
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
  echo "ERRO: GIT_TAG está vazio. Ex: dev000006"
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

# Tags / refs
git fetch --tags --force

# (Opcional) garante identity pro git se algum step precisar commitar/tag
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

# IMPORTANTÍSSIMO:
# O include do Harness upload usa lookup('env','HARNESS_*').
# Então exporta no padrão correto:
export HARNESS_ENDPOINT HARNESS_ACCOUNT_ID HARNESS_ORG_ID HARNESS_PROJECT_ID

# Mapeia o que você já tinha (HARNESS_X_API_KEY) para o nome esperado:
export HARNESS_API_KEY="${HARNESS_API_KEY:-$HARNESS_X_API_KEY}"

# =========================
# RUN
# =========================
ansible-playbook predeploy_from_execution.yml \
  -e "execution_file_name=${EXECUTION_FILE_NAME}" \
  -e "deployment_ref=${GIT_TAG}" \
  -e "nexus_base_url=${NEXUS_BASE_URL}" \
  -e "nexus_user=${NEXUS_USER}" \
  -e "nexus_password=${NEXUS_PASSWORD}" \
  -e "filestore_env=${FILESTORE_ENV}" \
  -e "stage_name=predeploy" \
  --forks 10
