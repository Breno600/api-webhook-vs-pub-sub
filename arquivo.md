#!/usr/bin/env bash
# gitlab_fulltree_audit_json.sh — percorre a árvore completa a partir de GROUP_ID (ex.: 13223)
set -euo pipefail

: "${GITLAB_URL:?Defina GITLAB_URL (ex: https://gitlab.seudominio.com)}"
: "${GITLAB_TOKEN:?Defina GITLAB_TOKEN (PAT com read_api)}"
: "${GROUP_ID:?Defina GROUP_ID (id numérico ou path do grupo raiz, ex.: 13223)}"

VERBOSE="${VERBOSE:-1}"  # 1=logs
CURL_FLAGS="${CURL_FLAGS:---retry 3 --connect-timeout 10 --max-time 60 --location}"
API_BASE="${API_BASE:-$GITLAB_URL/api/v4}"     # ajuste para subpath se necessário
HDR_AUTH=("PRIVATE-TOKEN: $GITLAB_TOKEN")

OUT_JSON="${OUT_JSON:-gitlab_group_audit.json}"
TMP_DIR="$(mktemp -d)"
GROUPS_ND="$TMP_DIR/groups.ndjson"
PROJECTS_ND="$TMP_DIR/projects.ndjson"

log(){ [[ "$VERBOSE" == "1" ]] && printf "[%(%F %T)T] %s\n" -1 "$*" >&2 || true; }
warn(){ printf "[%(%F %T)T] WARN: %s\n" -1 "$*" >&2; }
err (){ printf "[%(%F %T)T] ERRO: %s\n" -1 "$*" >&2; }
SUCCESS=0
cleanup(){ [[ $SUCCESS -eq 1 ]] && rm -rf "$TMP_DIR" 2>/dev/null || warn "Mantendo tmp: $TMP_DIR"; }
trap 'err "Linha $LINENO falhou ($?)"' ERR
trap 'cleanup' EXIT

need(){ command -v "$1" >/dev/null 2>&1 || { err "'$1' não encontrado"; exit 1; }; }
need curl
need jq

urlenc(){ jq -rn --arg x "$1" '$x|@uri'; }

_http_raw(){
  local url="$1"; log "GET $url"
  curl -sS $CURL_FLAGS -H "${HDR_AUTH[@]}" -w "\n---HTTP_CODE:%{http_code}---" "$url"
}
_http_split(){
  awk 'BEGIN{body="";code=""}
       /---HTTP_CODE:/ {code=$0; sub(/.*---HTTP_CODE:/,"",code); sub(/---.*/,"",code); print body > "/dev/stderr"; print code; next}
       {body=body $0 ORS}'
}
api_get_or_empty(){
  local path="$1"; local query="${2:-}"; local empty="${3:-[]}"
  local url="$API_BASE$path"; [[ -n "$query" ]] && url="$url?$query"
  local raw; raw="$(_http_raw "$url")"
  local code body
  body="$(printf "%s" "$raw" | _http_split 2> >(cat))"
  code="$(printf "%s" "$raw" | tail -n1)"
  log "HTTP $code $path"
  if [[ ! "$code" =~ ^[0-9]{3}$ ]]; then warn "HTTP inválido em $url"; echo "$empty"; return; fi
  if (( code < 200 || code >= 300 )); then warn "HTTP $code em $url — usando vazio"; echo "$empty"; return; fi
  if ! echo "$body" | jq -e . >/dev/null 2>&1; then warn "Resposta não-JSON em $url — usando vazio"; echo "$empty"; return; fi
  echo "$body"
}
api_get_obj(){ local q="${2-}"; api_get_or_empty "$1" "$q" "{}"; }
api_get_arr(){ local q="${2-}"; api_get_or_empty "$1" "$q" "[]"; }

api_get_paginated_arr(){
  local path="$1"; local query="${2:-}"; local page=1; local acc="[]"
  while :; do
    local url="$API_BASE$path?per_page=100&page=$page"; [[ -n "$query" ]] && url="$url&$query"
    local raw; raw="$(_http_raw "$url")"
    local code body
    body="$(printf "%s" "$raw" | _http_split 2> >(cat))"
    code="$(printf "%s" "$raw" | tail -n1)"
    log "HTTP $code $path?page=$page"
    if [[ ! "$code" =~ ^[0-9]{3}$ || $code -lt 200 || $code -ge 300 ]]; then
      warn "Página $page HTTP $code — fim da paginação"; break
    fi
    if ! echo "$body" | jq -e . >/dev/null 2>&1; then warn "Página $page não-JSON — fim"; break; fi
    acc="$(jq -cs 'add' <(echo "$acc") <(echo "$body"))"
    local len; len="$(echo "$body" | jq 'length')"
    (( len < 100 )) && break
    ((page++))
  done
  echo "$acc"
}

extract_apm_id(){ local n="$1"; [[ "$n" =~ ^[[:space:]]*([0-9]+)[[:space:]]*-[[:space:]]*.+$ ]] && echo "${BASH_REMATCH[1]}" || echo ""; }

# ---------- 1) Grupo raiz ----------
log "Carregando grupo raiz: $GROUP_ID"
root="$(api_get_obj "/groups/$(urlenc "$GROUP_ID")")"
root_id="$(echo "$root" | jq -r '.id')"
[[ -z "$root_id" || "$root_id" == "null" ]] && { err "Não achei o grupo raiz ($GROUP_ID). Verifique API_BASE=$API_BASE e o PAT (read_api)."; exit 1; }

root_full_path="$(echo "$root" | jq -r '.full_path')"
root_web_url="$(echo "$root" | jq -r '.web_url')"

# ---------- 2) Subgrupos (BFS) — apenas para listar links e extrair APM ----------
declare -A G_NAME G_PATH G_URL G_PARENT G_APM
declare -A VISITED
queue=("$root_id")
all_groups=()

G_NAME["$root_id"]="$(echo "$root" | jq -r '.name')"
G_PATH["$root_id"]="$root_full_path"
G_URL["$root_id"]="$root_web_url"
G_PARENT["$root_id"]=""

while ((${#queue[@]})); do
  gid="${queue[0]}"; queue=("${queue[@]:1}")
  [[ -n "${VISITED[$gid]:-}" ]] && continue
  VISITED["$gid"]=1; all_groups+=("$gid")

  subs="$(api_get_paginated_arr "/groups/$gid/subgroups")"
  mapfile -t ids < <(echo "$subs" | jq -r '.[].id')
  for sgid in "${ids[@]}"; do
    G_NAME["$sgid"]="$(echo "$subs" | jq -r ".[]|select(.id==$sgid)|.name")"
    G_PATH["$sgid"]="$(echo "$subs" | jq -r ".[]|select(.id==$sgid)|.full_path")"
    G_URL["$sgid"]="$(echo "$subs" | jq -r ".[]|select(.id==$sgid)|.web_url")"
    G_PARENT["$sgid"]="$gid"
    queue+=("$sgid")
    log "  subgroup: ${G_PATH[$sgid]} (id=$sgid)"
  done
done
log "Total de grupos (inclui raiz): ${#all_groups[@]}"

# salva groups NDJSON (com APM herdado)
for gid in "${all_groups[@]}"; do
  cur="$gid"; found=""
  while [[ -n "$cur" ]]; do
    try="$(extract_apm_id "${G_NAME[$cur]}")"; [[ -n "$try" ]] && { found="$try"; break; }
    cur="${G_PARENT[$cur]:-}"
  done
  G_APM["$gid"]="$found"
  jq -n \
    --arg id_str "$gid" \
    --arg name "${G_NAME[$gid]}" \
    --arg full_path "${G_PATH[$gid]}" \
    --arg web_url "${G_URL[$gid]}" \
    --arg parent_id_str "${G_PARENT[$gid]:-}" \
    --arg apm_id_str "$found" \
    'def n: if .=="" then null else . end;
     {id:$id_str, name:$name, full_path:$full_path, web_url:$web_url,
      parent_id:($parent_id_str|n), apm_id:($apm_id_str|n)}' >> "$GROUPS_ND"
done

# ---------- 3) PROJETOS DE TODA A ÁRVORE (UM ÚNICO LIST) ----------
# >>> AQUI QUE MUDAMOS: pega tudo com include_subgroups=true (não filtra por subgrupo)
log "Listando TODOS os projetos debaixo do grupo $root_id…"
all_projects="$(api_get_paginated_arr "/groups/$root_id/projects" "with_shared=false&include_subgroups=true")"
total_p="$(echo "$all_projects" | jq 'length')"
log "Projetos total sob $root_id: $total_p"

# ---------- 4) Para cada projeto: pega último pipeline e variáveis APP_* ----------
for ((i=0;i<total_p;i++)); do
  p="$(echo "$all_projects" | jq ".[$i]")"
  pid="$(echo "$p" | jq -r '.id')"
  p_pwn="$(echo "$p" | jq -r '.path_with_namespace')"
  p_web="$(echo "$p" | jq -r '.web_url')"
  p_def="$(echo "$p" | jq -r '.default_branch // empty')"

  # namespace do projeto (grupo a que pertence)
  ns_full="$(echo "$p" | jq -r '.namespace.full_path // empty')"
  ns_web="$(echo "$p" | jq -r '.namespace.web_url // empty')"
  # se a API não retornou web_url do namespace, construímos
  if [[ -z "$ns_web" && -n "$ns_full" ]]; then ns_web="$GITLAB_URL/$ns_full"; fi

  # APM herdado pelo caminho do namespace (subimos no mapa de grupos, se possível)
  # como fallback, tenta extrair APM do próprio path do namespace
  apm_id=""
  # tenta achar um grupo conhecido com mesmo full_path
  for gid in "${all_groups[@]}"; do
    if [[ "${G_PATH[$gid]}" == "$ns_full" ]]; then apm_id="${G_APM[$gid]}"; break; fi
  done
  if [[ -z "$apm_id" ]]; then apm_id="$(extract_apm_id "$ns_full")"; fi
  [[ -z "$apm_id" ]] && apm_id="(sem_apm)"

  # último pipeline do projeto
  pls="$(api_get_arr "/projects/$pid/pipelines" "per_page=1&order_by=updated_at&sort=desc")"
  plen="$(echo "$pls" | jq 'length')"

  has_general="false"
  pl_id=""; pl_status="none"; pl_ref=""; pl_user=""; pl_created=""; pl_updated=""; pl_url=""
  appflag="false"; app_name_val=""; app_version_val=""

  if (( plen > 0 )); then
    has_general="true"
    pl_id="$(echo "$pls" | jq -r '.[0].id')"
    pl_status="$(echo "$pls" | jq -r '.[0].status')"
    pl_ref="$(echo "$pls" | jq -r '.[0].ref')"
    pl_created="$(echo "$pls" | jq -r '.[0].created_at')"
    pl_updated="$(echo "$pls" | jq -r '.[0].updated_at')"
    pl_user="$(echo "$pls" | jq -r '.[0].user.username // .[0].user.name // ""')"
    pl_url="$p_web/-/pipelines/$pl_id"

    vars="$(api_get_arr "/projects/$pid/pipelines/$pl_id/variables")"
    app_name_val="$(echo "$vars" | jq -r '.[]|select(.key=="APP_NAME")|.value' | head -n1 || true)"
    app_version_val="$(echo "$vars" | jq -r '.[]|select(.key=="APP_VERSION")|.value' | head -n1 || true)"
    [[ -n "$app_name_val" || -n "$app_version_val" ]] && appflag="true"
  fi

  jq -n \
    --arg apm_id "$apm_id" \
    --arg ns_full "$ns_full" \
    --arg ns_web "$ns_web" \
    --arg project_id_str "$pid" \
    --arg project_pwn "$p_pwn" \
    --arg project_web_url "$p_web" \
    --arg default_branch "$p_def" \
    --arg has_general_pipeline_str "$has_general" \
    --arg latest_pipeline_id_str "$pl_id" \
    --arg latest_pipeline_status "$pl_status" \
    --arg latest_pipeline_ref "$pl_ref" \
    --arg latest_pipeline_user "$pl_user" \
    --arg latest_pipeline_created_at "$pl_created" \
    --arg latest_pipeline_updated_at "$pl_updated" \
    --arg latest_pipeline_web_url "$pl_url" \
    --arg has_app_vars_str "$appflag" \
    --arg app_name_value "$app_name_val" \
    --arg app_version_value "$app_version_val" \
    '
    def n: if .=="" then null else . end;
    def b: if .=="true" then true else false end;
    {
      apm_id: $apm_id,
      group: { full_path: ($ns_full|n), web_url: ($ns_web|n) },
      project: { id:$project_id_str, path_with_namespace:$project_pwn, web_url:$project_web_url,
                 default_branch: ($default_branch|n) },
      pipelines: {
        has_general_pipeline: ($has_general_pipeline_str|b),
        latest: (
          ($latest_pipeline_id_str|n) as $pid
          | if $pid==null or $latest_pipeline_status=="none"
            then null
            else {
              id:$latest_pipeline_id_str, status:$latest_pipeline_status,
              ref:($latest_pipeline_ref|n), user:($latest_pipeline_user|n),
              created_at:($latest_pipeline_created_at|n), updated_at:($latest_pipeline_updated_at|n),
              web_url:($latest_pipeline_web_url|n),
              variables:{ APP_NAME:($app_name_value|n), APP_VERSION:($app_version_value|n) }
            }
          end
        ),
        has_APP_NAME_or_APP_VERSION: ($has_app_vars_str|b)
      }
    }' >> "$PROJECTS_ND"

  log "  - $p_pwn | pipeline=$has_general status=$pl_status APP_NAME=${app_name_val:-} APP_VERSION=${app_version_val:-}"
done

# ---------- 5) Resumo + JSON final ----------
log "Montando JSON final: $OUT_JSON"

groups_json="$( [ -s "$GROUPS_ND" ] && jq -s '.' "$GROUPS_ND" || echo '[]' )"
projects_json="$( [ -s "$PROJECTS_ND" ] && jq -s '.' "$PROJECTS_ND" || echo '[]' )"

# agregados
declare -A APM_TOTAL APM_WITH_PIPE APM_WITH_APPVARS APM_STATUS
while IFS= read -r line; do
  apm="$(echo "$line" | jq -r '.apm_id')"
  (( APM_TOTAL["$apm"]+=1 )) 2>/dev/null || APM_TOTAL["$apm"]=1
  if [[ "$(echo "$line" | jq -r '.pipelines.has_general_pipeline')" == "true" ]]; then
    (( APM_WITH_PIPE["$apm"]+=1 )) 2>/dev/null || APM_WITH_PIPE["$apm"]=1
  fi
  if [[ "$(echo "$line" | jq -r '.pipelines.has_APP_NAME_or_APP_VERSION')" == "true" ]]; then
    (( APM_WITH_APPVARS["$apm"]+=1 )) 2>/dev/null || APM_WITH_APPVARS["$apm"]=1
  fi
  st="$(echo "$line" | jq -r '.pipelines.latest?.status // "none"')"
  key="$apm|$st"; (( APM_STATUS["$key"]+=1 )) 2>/dev/null || APM_STATUS["$key"]=1
done < <(jq -c '.[]' <<<"$projects_json")

# monta resumo por APM
apm_summary="[]"
for apm in "${!APM_TOTAL[@]}"; do
  counts_obj="{}"
  for s in success failed running canceled skipped manual pending created scheduled none other; do
    v="${APM_STATUS[$apm|$s]:-0}"
    counts_obj="$(jq -n --argjson obj "$counts_obj" --arg s "$s" --argjson v "$v" '$obj + {($s):$v}')" 
  done
  apm_summary="$(jq -n \
    --arg apm_id "$apm" \
    --argjson total "${APM_TOTAL[$apm]}" \
    --argjson with_general_pipeline "${APM_WITH_PIPE[$apm]:-0}" \
    --argjson with_APP_vars "${APM_WITH_APPVARS[$apm]:-0}" \
    --argjson status_counts "$counts_obj" \
    --argjson cur "$apm_summary" \
    '$cur + [{apm_id:$apm_id, total_projects:$total, with_general_pipeline:$with_general_pipeline, with_APP_NAME_or_APP_VERSION:$with_APP_vars, status_counts:$status_counts}]'
  )"
done

overall="$(jq -n --argjson items "$projects_json" '
  def count(f): reduce (.[]|select(f)) as $i (0;.+1);
  def status_counts: reduce (.[]|.pipelines.latest?.status // "none") as $s ({}; .+{($s):((.[$s]//0)+1)});
  { total_projects:(items|length),
    with_general_pipeline:(items|count(.pipelines.has_general_pipeline==true)),
    with_APP_NAME_or_APP_VERSION:(items|count(.pipelines.has_APP_NAME_or_APP_VERSION==true)),
    status_counts:(items|status_counts) }')"

jq -n \
  --arg generated_at "$(date -Iseconds)" \
  --arg gitlab_url "$GITLAB_URL" \
  --argjson root_group '{id:'"$root_id"',"full_path":"'"$root_full_path"'","web_url":"'"$root_web_url"'"}' \
  --argjson groups "$groups_json" \
  --argjson projects "$projects_json" \
  --argjson summary_by_apm "$apm_summary" \
  --argjson summary_overall "$overall" \
  '{ generated_at:$generated_at, gitlab_url:$gitlab_url, root_group:$root_group,
     groups:$groups, projects:$projects, summary:{by_apm:$summary_by_apm, overall:$summary_overall} }' \
  > "$OUT_JSON"

SUCCESS=1
echo "✅ Pronto: $OUT_JSON"
