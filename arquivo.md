#!/usr/bin/env bash
set -euo pipefail

GIT_TOKEN="${GIT_TOKEN:-6qywZxWVBUMuSQwwuGSk}"
REPO_URL="${REPO_URL:-https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef-configurations.git}"
BRANCH="${BRANCH:-main}"

WORKDIR="$(mktemp -d)"
trap 'rm -rf "$WORKDIR"' EXIT

REPO_DIR="${WORKDIR}/repo"

echo "Clonando repo em ${REPO_DIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${REPO_DIR}"

echo "Conteúdo do repo:"
ls -la "${REPO_DIR}"
echo "PWD do repo:"
cd "${REPO_DIR}"
pwd

cd ansible
ansible-playbook playbooks/deploy_from_execution.yml
