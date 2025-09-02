#!/usr/bin/env bash
set -euo pipefail

: "${GITLAB_URL:?Defina GITLAB_URL (ex: https://gitlab.seudominio.com)}"
: "${GITLAB_TOKEN:?Defina GITLAB_TOKEN (Personal Access Token com permissões de leitura)}"
: "${GROUP_ID:?Defina GROUP_ID (ID numérico ou path do grupo raiz)}"

# Dependências
for bin in curl jq; do
  command -v "$bin" >/dev/null 2>&1 || { echo "ERRO: '$bin' não encontrado."; exit 1; }
done

API="$GITLAB_URL/api/v4"
HDR_AUTH=("PRIVATE-TOKEN: $GITLAB_TOKEN")

# Arquivos de saída
PROJECTS_CSV="projects_report.csv"
APM_SUMMARY_CSV="apm_summary.csv"

# CSV header
echo "group_id,group_full_path,group_web_url,project_id,project_path_with_namespace,default_branch,has_ci_file,latest_pipeline_status,latest_pipeline_web_url" > "$PROJECTS_CSV"

declare -A GROUP_WEBURL          # [group_id] = web_url
declare -A GROUP_FULLPATH       # [group_id] = full_path
declare -A APM_COUNTS           # [apm_id]  = count_projects

#############################################
# Funções utilitárias
#############################################

api_get_paginated() {
  # Uso: api_get_paginated "/rota" "query=..." 
  local path="$1"
  local query="${2:-}"
  local page=1
  local out tmp headers next

  out="[]"
  while :; do
    read -r tmp headers < <(
      curl -sS -i \
        -H "${HDR_AUTH[@]}" \
        "${API}${path}?per_page=100&page=${page}${query:+&}${query}" | awk 'BEGIN{h=1} 
          /^[[:space:]]*$/ && h==1 {h=0; print; next} 
          { if(h){print > "/dev/stderr"} else {print} }'
    )

    # 'tmp' é a parte JSON (stdout), 'headers' veio por stderr; reorganizamos acima
    # Para simplificar, re-executamos com -D - para obter headers limpos:
    response="$(curl -sS -D - -H "${HDR_AUTH[@]}" "${API}${path}?per_page=100&page=${page}${query:+&}${query}")"
    headers="$(printf "%s" "$response" | sed -n '1,/^\r$/p')"
    json="$(printf "%s" "$response" | sed '1,/^\r$/d')"

    # Agrega JSON (array) ao 'out'
    out="$(jq -c --slurp 'add' <(echo "$out") <(echo "$json"))"
    next="$(printf "%s" "$headers" | awk -F': ' 'tolower($1)=="x-next-page"{gsub("\r","",$2);print $2}')"

    if [[ -z "$next" ]]; then
      echo "$out"
      return
    fi
    page=$((page+1))
  done
}

api_get() {
  # Sem paginação
  local path="$1"
  local query="${2:-}"
  curl -sS -H "${HDR_AUTH[@]}" "${API}${path}${query:+?}${query}"
}

urlenc() {
  # URL encode simples p/ branch etc.
  python3 - <<'PY' "$1"
import sys, urllib.parse
print(urllib.parse.quote(sys.argv[1], safe=''))
PY
}

extract_apm_id() {
  # Extrai APM_ID do nome do grupo se padrão "12345 - Nome" (flexível em espaços)
  local group_name="$1"
  if [[ "$group_name" =~ ^[[:space:]]*([0-9]+)[[:space:]]*-[[:space:]]*.+$ ]]; then
    echo "${BASH_REMATCH[1]}"
  else
    echo ""
  fi
}

#############################################
# Coletar informações do grupo raiz
#############################################
root_group="$(api_get "/groups/$(urlenc "$GROUP_ID")")"
root_id="$(echo "$root_group" | jq -r '.id')"
root_full_path="$(echo "$root_group" | jq -r '.full_path')"
root_web_url="$(echo "$root_group" | jq -r '.web_url')"

GROUP_WEBURL["$root_id"]="$root_web_url"
GROUP_FULLPATH["$root_id"]="$root_full_path"

#############################################
# Percorrer subgroups (recursivo BFS)
#############################################
declare -A VISITED
queue=("$root_id")
all_groups=()

while ((${#queue[@]})); do
  gid="${queue[0]}"
  queue=("${queue[@]:1}")

  [[ -n "${VISITED[$gid]:-}" ]] && continue
  VISITED["$gid"]=1
  all_groups+=("$gid")

  # pega subgroups do gid
  # NOTA: o endpoint /groups/:id/subgroups retorna subgrupos diretos
  subs="$(api_get_paginated "/groups/$gid/subgroups")"
  mapfile -t ids < <(echo "$subs" | jq -r '.[].id')
  for sgid in "${ids[@]}"; do
    s_web="$(echo "$subs" | jq -r ".[] | select(.id==$sgid) | .web_url")"
    s_path="$(echo "$subs" | jq -r ".[] | select(.id==$sgid) | .full_path")"
    GROUP_WEBURL["$sgid"]="$s_web"
    GROUP_FULLPATH["$sgid"]="$s_path"
    queue+=("$sgid")
  done
done

#############################################
# Para cada grupo (incluindo subgroups), coletar projetos
#############################################
for gid in "${all_groups[@]}"; do
  group_info="$(api_get "/groups/$gid")"
  group_name="$(echo "$group_info" | jq -r '.name')"
  group_full_path="${GROUP_FULLPATH[$gid]}"
  group_web_url="${GROUP_WEBURL[$gid]}"

  # Projetos diretos do grupo (sem include_subgroups aqui, pois já percorremos recursivamente)
  projs="$(api_get_paginated "/groups/$gid/projects" "with_shared=false&include_subgroups=false")"

  proj_count=$(echo "$projs" | jq 'length')
  (( proj_count == 0 )) && continue

  # Se o nome do grupo seguir "APM_ID - Nome", extraímos p/ agregação
  apm_id="$(extract_apm_id "$group_name")"

  for ((i=0; i<proj_count; i++)); do
    p="$(echo "$projs" | jq ".[$i]")"
    pid="$(echo "$p" | jq -r '.id')"
    ppath="$(echo "$p" | jq -r '.path_with_namespace')"
    pweb="$(echo "$p" | jq -r '.web_url')"
    dbranch="$(echo "$p" | jq -r '.default_branch // empty')"

    # Verifica se .gitlab-ci.yml existe no branch padrão
    has_ci="no"
    if [[ -n "$dbranch" && "$dbranch" != "null" ]]; then
      enc_branch="$(urlenc "$dbranch")"
      # HEAD no arquivo pra evitar baixar conteúdo
      code=$(curl -sS -o /dev/null -w "%{http_code}" \
        -H "${HDR_AUTH[@]}" \
        "${API}/projects/${pid}/repository/files/$(urlenc ".gitlab-ci.yml")?ref=${enc_branch}")
      if [[ "$code" == "200" ]]; then
        has_ci="yes"
      fi
    fi

    # Último pipeline (ordenado por atualização)
    latest_status="none"
    latest_pipeline_url=""
    pipelines="$(api_get "/projects/${pid}/pipelines" "per_page=1&order_by=updated_at&sort=desc")"
    plen="$(echo "$pipelines" | jq 'length')"
    if (( plen > 0 )); then
      latest_status="$(echo "$pipelines" | jq -r '.[0].status')"
      plid="$(echo "$pipelines" | jq -r '.[0].id')"
      latest_pipeline_url="$pweb/-/pipelines/$plid"
    fi

    # Linha do CSV (projeto)
    echo "$gid,$group_full_path,$group_web_url,$pid,$ppath,$dbranch,$has_ci,$latest_status,$latest_pipeline_url" >> "$PROJECTS_CSV"

    # Agregação por APM_ID (conta repositórios sob grupos que seguem o padrão)
    if [[ -n "$apm_id" ]]; then
      cur="${APM_COUNTS[$apm_id]:-0}"
      APM_COUNTS["$apm_id"]=$((cur+1))
    fi
  done
done

#############################################
# CSV de resumo por APM
#############################################
echo "apm_id,total_repositories" > "$APM_SUMMARY_CSV"
for k in "${!APM_COUNTS[@]}"; do
  echo "$k,${APM_COUNTS[$k]}" >> "$APM_SUMMARY_CSV"
done

echo
echo "✅ Concluído!"
echo "• Detalhes por projeto: $PROJECTS_CSV"
echo "• Resumo por APM_ID:    $APM_SUMMARY_CSV"
echo
echo "Dica: abra o CSV em um editor/planilha e filtre por 'group_full_path' ou 'latest_pipeline_status'."
