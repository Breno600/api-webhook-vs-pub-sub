#!/bin/bash
set -euo pipefail

# Obrigatórios (vindos do Harness Secrets / Variables)
: "${GIT_TAG:?ERRO: GIT_TAG não informado}"
: "${GIT_TOKEN:?ERRO: GIT_TOKEN não informado}"
: "${NEXUS_USER:?ERRO: NEXUS_USER não informado}"
: "${NEXUS_PASSWORD:?ERRO: NEXUS_PASSWORD não informado}"

# Opcionais
EXECUTION_FILE_NAME="${EXECUTION_FILE_NAME:-execution/machine_list_dev.yml}"
GIT_BRANCH="${GIT_BRANCH:-develop}"
FILESTORE_ENV="${FILESTORE_ENV:-dev}"
NEXUS_BASE_URL="${NEXUS_BASE_URL:-https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev}"

export ANSIBLE_HOST_KEY_CHECKING=False

REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

WORKDIR="$(mktemp -d)"
trap 'rm -rf "$WORKDIR"' EXIT

echo "Clonando repo em ${WORKDIR}..."
git clone --branch "${GIT_BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"
git fetch --tags --force

echo "== PIPELINE PREDEPLOY =="
echo "TAG   : ${GIT_TAG}"
echo "EXEC  : ${EXECUTION_FILE_NAME}"
echo "BRANCH: ${GIT_BRANCH}"
echo

cd ansible

# Passa secrets via env (melhor do que -e)
export NEXUS_BASE_URL NEXUS_USER NEXUS_PASSWORD FILESTORE_ENV

ansible-playbook predeploy_from_execution.yml \
  -e "execution_file_name=${EXECUTION_FILE_NAME}" \
  -e "deployment_ref=${GIT_TAG}" \
  -e "nexus_base_url=${NEXUS_BASE_URL}" \
  -e "filestore_env=${FILESTORE_ENV}" \
  --forks 10
