#!/bin/bash
set -euo pipefail

# === Variáveis vindas do Harness ===
GIT_TOKEN="${GIT_TOKEN:?GIT_TOKEN nao definido}"
GIT_TAG="${GIT_TAG:?GIT_TAG nao definido}"                             # ex: DEV000000002
EXECUTION_FILE_NAME="${EXECUTION_FILE_NAME:?EXECUTION_FILE_NAME nao definido}"  # ex: machine_list_dev.yml
GIT_BRANCH="${GIT_BRANCH:-main}"                                       # branch onde vai o commit

GIT_USER_NAME="${GIT_USER_NAME:-harness-bot}"
GIT_USER_EMAIL="${GIT_USER_EMAIL:-harness-bot@example.local}"

echo "== 1) Clonando repositorio (branch ${GIT_BRANCH}) =="

REPO_URL="https://${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

WORKDIR="/tmp/elastic-compute-cloud-sitef"   # <= MESMO caminho que aparece no seu print
rm -rf "$WORKDIR" || true
git clone --branch "$GIT_BRANCH" "$REPO_URL" "$WORKDIR"

cd "$WORKDIR"
git config --global --add safe.directory "$WORKDIR"
git fetch --tags

echo "Repositorio em $(pwd)"
echo "HEAD em:"
git rev-parse --abbrev-ref HEAD
git rev-parse HEAD

echo
echo "== 2) Rodando predeploy via Ansible =="
echo "   TAG (deployment_ref): ${GIT_TAG}"
echo "   execution file      : ${EXECUTION_FILE_NAME}"

cd ansible

ansible-playbook predeploy_from_execution.yml \
  -e "execution_file_name=${EXECUTION_FILE_NAME}" \
  -e "deployment_ref=${GIT_TAG}" \
  --forks 10

cd ..

echo
echo "== 3) Verificando se houve alteracoes em status/${GIT_TAG} =="

if [ ! -d "status/${GIT_TAG}" ]; then
  echo "ERRO: status/${GIT_TAG} nao foi criado pelo playbook."
  echo "Verifique se o predeploy_from_execution.yml criou a pasta status/"
  exit 1
fi

git status status/"${GIT_TAG}"
CHANGES=$(git status --porcelain status/"${GIT_TAG}" || true)

if [ -z "$CHANGES" ]; then
  echo "Nenhuma mudanca em status/${GIT_TAG}. Nada para commitar."
  exit 0
fi

echo
echo "== 4) Fazendo commit e push das mudancas de status =="
git config user.name  "${GIT_USER_NAME}"
git config user.email "${GIT_USER_EMAIL}"

git add "status/${GIT_TAG}"
git commit -m "Predeploy status for ${GIT_TAG} (execution ${EXECUTION_FILE_NAME})"
git push origin "${GIT_BRANCH}"

echo "Commit de status enviado com sucesso para ${GIT_BRANCH}."