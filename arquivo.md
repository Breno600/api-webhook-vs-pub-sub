Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
Clonando repo em /tmp/tmp.b3sHY6GLwd...
Cloning into '/tmp/tmp.b3sHY6GLwd/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 19, done.
remote: Counting objects:   5% (1/19)
remote: Counting objects:  10% (2/19)
remote: Counting objects:  15% (3/19)
remote: Counting objects:  21% (4/19)
remote: Counting objects:  26% (5/19)
remote: Counting objects:  31% (6/19)
remote: Counting objects:  36% (7/19)
remote: Counting objects:  42% (8/19)
remote: Counting objects:  47% (9/19)
remote: Counting objects:  52% (10/19)
remote: Counting objects:  57% (11/19)
remote: Counting objects:  63% (12/19)
remote: Counting objects:  68% (13/19)
remote: Counting objects:  73% (14/19)
remote: Counting objects:  78% (15/19)
remote: Counting objects:  84% (16/19)
remote: Counting objects:  89% (17/19)
remote: Counting objects:  94% (18/19)
remote: Counting objects: 100% (19/19)
remote: Counting objects: 100% (19/19), done.
remote: Compressing objects:   8% (1/12)
remote: Compressing objects:  16% (2/12)
remote: Compressing objects:  25% (3/12)
remote: Compressing objects:  33% (4/12)
remote: Compressing objects:  41% (5/12)
remote: Compressing objects:  50% (6/12)
remote: Compressing objects:  58% (7/12)
remote: Compressing objects:  66% (8/12)
remote: Compressing objects:  75% (9/12)
remote: Compressing objects:  83% (10/12)
remote: Compressing objects:  91% (11/12)
remote: Compressing objects: 100% (12/12)
remote: Compressing objects: 100% (12/12), done.
remote: Total 19 (delta 3), reused 0 (delta 0), pack-reused 0
Receiving objects:   5% (1/19)
Receiving objects:  10% (2/19)
Receiving objects:  15% (3/19)
Receiving objects:  21% (4/19)
Receiving objects:  26% (5/19)
Receiving objects:  31% (6/19)
Receiving objects:  36% (7/19)
Receiving objects:  42% (8/19)
Receiving objects:  47% (9/19)
Receiving objects:  52% (10/19)
Receiving objects:  57% (11/19)
Receiving objects:  63% (12/19)
Receiving objects:  68% (13/19)
Receiving objects:  73% (14/19)
Receiving objects:  78% (15/19)
Receiving objects:  84% (16/19)
Receiving objects:  89% (17/19)
Receiving objects:  94% (18/19)
Receiving objects: 100% (19/19)
Receiving objects: 100% (19/19), 5.56 KiB | 5.56 MiB/s, done.
Resolving deltas:   0% (0/3)
Resolving deltas:  33% (1/3)
Resolving deltas:  66% (2/3)
Resolving deltas: 100% (3/3)
Resolving deltas: 100% (3/3), done.
bash: line 28: cd: /tmp/tmp.b3sHY6GLwd/elastic-compute-cloud-sitef-configurations: No such file or directory
Command finished with status FAILURE


segue script no harness ele parece que baixou errado
#!/usr/bin/env bash
set -euo pipefail

GIT_TOKEN="${GIT_TOKEN:-6qywZxWVBUMuSQwwuGSk}"
REPO_URL="${REPO_URL:-https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef-configurations.git}"
BRANCH="${BRANCH:-main}"

WORKDIR="$(mktemp -d)"
trap 'rm -rf "$WORKDIR"' EXIT

echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef-configurations"

ls -la 
pwd

cd ansible
ansible-playbook playbooks/deploy_from_execution.yml
