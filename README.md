#!/bin/bash
set -euo pipefail

GIT_TOKEN="xXAiJyF1Hx4noamrBSdV"
GIT_TAG="${GIT_TAG:-}"
EXECUTION_FILE_NAME="${EXECUTION_FILE_NAME:-execution/machine_list_dev.yml}"
GIT_BRANCH="${GIT_BRANCH:-develop}"

GIT_USER_NAME="${GIT_USER_NAME:-harness-bot}"
GIT_USER_EMAIL="${GIT_USER_EMAIL:-harness-bot@fiserv.com}"

NEXUS_BASE_URL="${NEXUS_BASE_URL:-https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev}"
NEXUS_USER="${NEXUS_USER:-AS4hZF20}"
NEXUS_PASSWORD="${NEXUS_PASSWORD:-l7WwGfJd_Grmh-5Kn7B__U8nqgdNWh1XhrYtVQQ5I_6k}"

FILESTORE_ENV="${FILESTORE_ENV:-dev}"

if [[ -z "${GIT_TOKEN}" || -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TOKEN ou GIT_TAG não informado"
  exit 1
fi

export ANSIBLE_HOST_KEY_CHECKING=False

REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
rm -rf "${WORKDIR}/elastic-compute-cloud-sitef"
git clone --branch "${GIT_BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"
git fetch --tags

echo "== PIPELINE PREDEPLOY =="
echo "TAG   : ${GIT_TAG}"
echo "EXEC  : ${EXECUTION_FILE_NAME}"
echo "BRANCH: ${GIT_BRANCH}"
echo

cd ansible

ansible-playbook predeploy_from_execution.yml \
  -e "execution_file_name=${EXECUTION_FILE_NAME}" \
  -e "deployment_ref=${GIT_TAG}" \
  -e "nexus_base_url=${NEXUS_BASE_URL}" \
  -e "nexus_user=${NEXUS_USER}" \
  -e "nexus_password=${NEXUS_PASSWORD}" \
  -e "filestore_env=${FILESTORE_ENV}" \
  --forks 10
