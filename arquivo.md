#!/bin/bash
set -euo pipefail

echo "== 1) Clonando repositório =="
REPO_URL="https://harness:SEU_TOKEN_AQUI@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

WORKDIR="/tmp/elastic-compute-cloud-sitef"
rm -rf "$WORKDIR" || true
git clone "$REPO_URL" "$WORKDIR"

cd "$WORKDIR"

# Garante que temos pelo menos 2 commits pra fazer diff
COMMITS_QTD=$(git rev-list --count HEAD || echo 0)
if [ "$COMMITS_QTD" -lt 2 ]; then
  echo "Repo tem menos de 2 commits, nada pra comparar. Saindo."
  exit 0
fi

echo "== 2) Descobrindo arquivos alterados no último commit =="
CHANGED_FILES=$(git diff --name-only HEAD~1 HEAD || true)

if [ -z "$CHANGED_FILES" ]; then
  echo "Nenhum arquivo alterado nesse commit. Nada a fazer."
  exit 0
fi

echo "Arquivos alterados:"
echo "$CHANGED_FILES"
echo

cd ansible

# Função pra extrair IP a partir do nome do arquivo ip-xxx.yaml
get_ip_from_machine_file() {
  local fname="$1"
  # tira prefixo ip- e sufixo .yaml/.yml
  echo "$fname" \
    | sed -E 's/^ip-//' \
    | sed -E 's/\.ya?ml$//'
}

echo "== 3) Processando arquivos alterados =="

for file in $CHANGED_FILES; do
  echo ">> Analisando $file"

  # 3.1) Caso 1: arquivo de máquina (machine/ip-*.yaml)
  if [[ "$file" == machine/ip-*.yaml ]]; then
    machine_file=$(basename "$file")
    target_ip=$(get_ip_from_machine_file "$machine_file")

    echo "   -> Detectado arquivo de máquina: $machine_file"
    echo "   -> IP de destino: $target_ip"
    echo "   -> Executando deploy_from_machine.yml"

    ansible-playbook deploy_from_machine.yml \
      -e "machine_file_name=$machine_file" \
      -e "target_host=$target_ip"

  # 3.2) Caso 2: arquivo de grupo (group-machine/*.yaml)
  elif [[ "$file" == group-machine/*.yaml ]]; then
    group_file=$(basename "$file")

    echo "   -> Detectado arquivo de grupo: $group_file"
    echo "   -> Executando deploy_from_group.yml"

    ansible-playbook deploy_from_group.yml \
      -e "group_file_name=$group_file"
  else
    echo "   -> Não é machine/ nem group-machine, ignorando."
  fi

  echo
done

echo "== 4) Execução concluída =="