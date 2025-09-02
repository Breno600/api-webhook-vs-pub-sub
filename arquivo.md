#!/usr/bin/env bash
set -euo pipefail

: "${GITLAB_URL:?Defina GITLAB_URL (ex: https://gitlab.seudominio.com)}"
: "${GITLAB_TOKEN:?Defina GITLAB_TOKEN (PAT com read_api)}"
: "${GROUP_ID:?Defina GROUP_ID (id numérico ou path do grupo raiz)}"

VERBOSE="${VERBOSE:-1}"            # 1 = logs ligados, 0 = silencioso
CURL_FLAGS="${CURL_FLAGS:---retry 3 --connect-timeout 10 --max-time 60}"

API="$GITLAB_URL/api/v4"
HDR_AUTH=("PRIVATE-TOKEN: $GITLAB_TOKEN")

PROJECTS_CSV="projects_report.csv"
APM_SUMMARY_CSV="apm_ci_summary.csv"
OVERALL_SUMMARY_CSV="overall_ci_summary.csv"

log() { [[ "$VERBOSE" == "1" ]] && printf "[%(%F %T)T] %s\n" -1 "$*" >&2 || true; }
trap 'echo "[ERRO] Linha $LINENO: comando '\''$BASH_COMMAND'\'' saiu com $?" >&2' ERR

# -------- util --------
urlenc() { jq -rn --arg x "$1" '$x|@uri'; }

is_known_status() {
  case "$1" in
    success|failed|running|canceled|skipped|manual|pending|created|scheduled) return 0;;
    *) return 1;;
  esac
}

# -------- armazenamentos --------
declare -A GROUP_NAME       # [gid]=name
declare -A GROUP_FULLPATH   # [gid]=full_path
declare -A GROUP_WEBURL     # [gid]=web_url
declare -A GROUP_PARENT     # [gid]=parent_id (ou vazio)
declare -A GROUP_APM        # [gid]=apm_id (herdado do ancestral "APM_ID - Nome" mais próximo)

# Agregadores por APM
declare -A APM_TOTAL        # [apm]=count projetos
declare -A APM_WITH_CI      # [apm]=count has_ci=yes
declare -A APM_WITHOUT_CI   # [apm]=count has_ci=no
declare -A APM_STATUS       # [apm|status]=count (status do último pipeline)

# Agregado geral
ALL="__ALL__"
APM_TOTAL[$ALL]=0
APM_WITH_CI[$ALL]=0
APM_WITHOUT_CI[$ALL]=0

# -------- API helpers --------
api_get() {
  local path="$1"; local query="${2:-}"
  local url="$API$path"; [[ -n "$query" ]] && url="$url?$query"
  log "GET $url"
  curl -sS $CURL_FLAGS -H "${HDR_AUTH[@]}" "$url"
}

api_get_paginated() {
  local path="$1"; local query="${2:-}"; local page="1"; local all="[]"
  while :; do
    local url="$API$path?per_page=100&page=$page"
    [[ -n "$query" ]] && url="$url&$query"
    log "GET $url"
    local tmp_headers; tmp_headers="$(mktemp)"
    local body; body="$(curl -sS $CURL_FLAGS -D "$tmp_headers" -H "${HDR_AUTH[@]}" "$url")"
    all="$(jq -cs 'add' <(echo "$all") <(echo "$body"))"
    local next; next="$(awk -F': ' 'tolower($1)=="x-next-page"{gsub("\r","",$2);print $2}' "$tmp_headers")"
    rm -f "$tmp_headers"
    [[ -z "$next" ]] && { echo "$all"; return; }
    page="$next"
  done
}

extract_apm_id_from_name() {
  local name="$1"
  if [[ "$name" =~ ^[[:space:]]*([0-9]+)[[:space:]]*-[[:space:]]*.+$ ]]; then
    echo "${BASHREMATCH[1]}"
  else
    echo ""
  fi
}

# Caminha ancestrais até achar um grupo cujo nome siga "APM_ID - Nome"
find_apm_for_group() {
  local gid="$1"
  [[ -n "${GROUP_APM[$gid]:-}" ]] && { echo "${GROUP_APM[$gid]}"; return; }

  local cur="$gid"
  while [[ -n "$cur" ]]; do
    local gname="${GROUP_NAME[$cur]:-}"
    local apm="$(extract_apm_id_from_name "$gname")"
    if [[ -n "$apm" ]]; then
      GROUP_APM["$gid"]="$apm"
      echo "$apm"
      return
    fi
    cur="${GROUP_PARENT[$cur]:-}"
  done
  GROUP_APM["$gid"]=""
  echo ""
}

has_ci_file() {
  local pid="$1"; local dbranch="$2"
  [[ -z "$dbranch" || "$dbranch" == "null" ]] && { echo "no"; return; }
  local encb; encb="$(urlenc "$dbranch")"
  # Tenta .gitlab-ci.yml e .gitlab-ci.yaml
  for fname in ".gitlab-ci.yml" ".gitlab-ci.yaml"; do
    local code
    code="$(curl -sS -o /dev/null -w "%{http_code}" $CURL_FLAGS \
      -H "${HDR_AUTH[@]}" \
      "$API/projects/${pid}/repository/files/$(urlenc "$fname")?ref=$encb")"
    [[ "$code" == "200" ]] && { echo "yes"; return; }
  done
  echo "no"
}

latest_pipeline_status() {
  local pid="$1"
  local js; js="$(api_get "/projects/${pid}/pipelines" "per_page=1&order_by=updated_at&sort=desc")"
  local len; len="$(echo "$js" | jq 'length')"
  if (( len > 0 )); then
    echo "$js" | jq -r '.[0].status'
  else
    echo "none"
  fi
}

latest_pipeline_url() {
  local pid="$1"; local web="$2"
  local js; js="$(api_get "/projects/${pid}/pipelines" "per_page=1&order_by=updated_at&sort=desc")"
  local len; len="$(echo "$js" | jq 'length')"
  if (( len > 0 )); then
    local plid; plid="$(echo "$js" | jq -r '.[0].id')"
    echo "$web/-/pipelines/$plid"
  else
    echo ""
  fi
}

inc() {
  # inc "ARRNAME" "KEY"
  local arr="$1" key="$2"
  local cur=0
  eval "cur=\${$arr[\$key]:-0}"
  ((cur++))
  eval "$arr[\"$key\"]=\$cur"
}

# -------- CSV headers --------
echo "apm_id,group_id,group_full_path,group_web_url,project_id,project_path_with_namespace,project_web_url,default_branch,has_ci_file,latest_pipeline_status,latest_pipeline_web_url" > "$PROJECTS_CSV"
echo "apm_id,total_projects,with_ci,without_ci,latest_success,latest_failed,latest_running,latest_canceled,latest_skipped,latest_manual,latest_pending,latest_created,latest_scheduled,latest_none,latest_other" > "$APM_SUMMARY_CSV"
echo "scope,total_projects,with_ci,without_ci,latest_success,latest_failed,latest_running,latest_canceled,latest_skipped,latest_manual,latest_pending,latest_created,latest_scheduled,latest_none,latest_other" > "$OVERALL_SUMMARY_CSV"

# -------- Carrega grupo raiz --------
log "Carregando grupo raiz: $GROUP_ID"
root="$(api_get "/groups/$(urlenc "$GROUP_ID")")"
root_id="$(echo "$root" | jq -r '.id')"
GROUP_NAME["$root_id"]="$(echo "$root" | jq -r '.name')"
GROUP_FULLPATH["$root_id"]="$(echo "$root" | jq -r '.full_path')"
GROUP_WEBURL["$root_id"]="$(echo "$root" | jq -r '.web_url')"
GROUP_PARENT["$root_id"]=""
log "Root id=$root_id full_path=${GROUP_FULLPATH[$root_id]}"

# -------- BFS de subgrupos --------
declare -A VISITED
queue=("$root_id")
all_groups=()

while ((${#queue[@]})); do
  gid="${queue[0]}"; queue=("${queue[@]:1}")
  [[ -n "${VISITED[$gid]:-}" ]] && continue
  VISITED["$gid"]=1
  all_groups+=("$gid")

  log "Buscando subgrupos de gid=$gid (${GROUP_FULLPATH[$gid]})"
  subs="$(api_get_paginated "/groups/$gid/subgroups")"
  mapfile -t ids < <(echo "$subs" | jq -r '.[].id')
  for sgid in "${ids[@]}"; do
    GROUP_NAME["$sgid"]="$(echo "$subs" | jq -r ".[] | select(.id==$sgid) | .name")"
    GROUP_FULLPATH["$sgid"]="$(echo "$subs" | jq -r ".[] | select(.id==$sgid) | .full_path")"
    GROUP_WEBURL["$sgid"]="$(echo "$subs" | jq -r ".[] | select(.id==$sgid) | .web_url")"
    GROUP_PARENT["$sgid"]="$(echo "$subs" | jq -r ".[] | select(.id==$sgid) | .parent_id // \"\"")"
    log "  + subgroup: id=$sgid full_path=${GROUP_FULLPATH[$sgid]}"
    queue+=("$sgid")
  done
done
log "Total de grupos (inclui raiz): ${#all_groups[@]}"

# Pré-calcula APM herdado para todos os grupos
for gid in "${all_groups[@]}"; do
  find_apm_for_group "$gid" >/dev/null
done

# -------- Itera grupos e coleta projetos diretos --------
for gid in "${all_groups[@]}"; do
  gpath="${GROUP_FULLPATH[$gid]}"
  gurl="${GROUP_WEBURL[$gid]}"
  gname="${GROUP_NAME[$gid]}"
  gapm="${GROUP_APM[$gid]}"

  projs="$(api_get_paginated "/groups/$gid/projects" "with_shared=false&include_subgroups=false")"
  pcount="$(echo "$projs" | jq 'length')"
  log "Processando grupo id=$gid path=$gpath (APM=${gapm:-none}) - projetos diretos: $pcount"
  (( pcount == 0 )) && continue

  for ((i=0;i<pcount;i++)); do
    p="$(echo "$projs" | jq ".[$i]")"
    pid="$(echo "$p" | jq -r '.id')"
    ppath="$(echo "$p" | jq -r '.path_with_namespace')"
    pweb="$(echo "$p" | jq -r '.web_url')"
    dbranch="$(echo "$p" | jq -r '.default_branch // empty')"

    hci="$(has_ci_file "$pid" "$dbranch")"
    status="$(latest_pipeline_status "$pid")"
    plurl="$(latest_pipeline_url "$pid" "$pweb")"

    apm="${gapm:-}"
    [[ -z "$apm" ]] && apm="(sem_apm)"

    # CSV por projeto
    echo "$apm,$gid,$gpath,$gurl,$pid,$ppath,$pweb,$dbranch,$hci,$status,$plurl" >> "$PROJECTS_CSV"

    # Agregadores por APM
    inc APM_TOTAL "$apm"
    inc APM_TOTAL "$ALL"

    if [[ "$hci" == "yes" ]]; then
      inc APM_WITH_CI "$apm"
      inc APM_WITH_CI "$ALL"
    else
      inc APM_WITHOUT_CI "$apm"
      inc APM_WITHOUT_CI "$ALL"
    fi

    if is_known_status "$status"; then
      inc APM_STATUS "$apm|$status"
      inc APM_STATUS "$ALL|$status"
    else
      inc APM_STATUS "$apm|other"
      inc APM_STATUS "$ALL|other"
    fi

    log "  - proj=$ppath branch=${dbranch:-<none>} ci=$hci status=$status"
  done
done

# -------- Emite resumo por APM --------
emit_line_apm() {
  local apm="$1"
  local t="${APM_TOTAL[$apm]:-0}"
  local wci="${APM_WITH_CI[$apm]:-0}"
  local nci="${APM_WITHOUT_CI[$apm]:-0}"
  local s_success="${APM_STATUS[$apm|success]:-0}"
  local s_failed="${APM_STATUS[$apm|failed]:-0}"
  local s_running="${APM_STATUS[$apm|running]:-0}"
  local s_canceled="${APM_STATUS[$apm|canceled]:-0}"
  local s_skipped="${APM_STATUS[$apm|skipped]:-0}"
  local s_manual="${APM_STATUS[$apm|manual]:-0}"
  local s_pending="${APM_STATUS[$apm|pending]:-0}"
  local s_created="${APM_STATUS[$apm|created]:-0}"
  local s_scheduled="${APM_STATUS[$apm|scheduled]:-0}"
  local s_none="${APM_STATUS[$apm|none]:-0}"
  local s_other="${APM_STATUS[$apm|other]:-0}"
  echo "$apm,$t,$wci,$nci,$s_success,$s_failed,$s_running,$s_canceled,$s_skipped,$s_manual,$s_pending,$s_created,$s_scheduled,$s_none,$s_other"
}

# Linhas por APM (ordena por chave)
mapfile -t apm_keys < <(printf "%s\n" "${!APM_TOTAL[@]}" | grep -v "^$ALL$" | sort -V)
for k in "${apm_keys[@]}"; do
  emit_line_apm "$k" >> "$APM_SUMMARY_CSV"
done

# Resumo geral
emit_line_apm "$ALL" | sed "s/^$ALL/overall/" >> "$OVERALL_SUMMARY_CSV"

log "Finalizado."
log "Arquivos: $PROJECTS_CSV | $APM_SUMMARY_CSV | $OVERALL_SUMMARY_CSV"
