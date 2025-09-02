#!/usr/bin/env bash
# gitlab_group_audit_json.sh 05
# Varre grupo raiz (GROUP_ID), subgrupos e projetos diretos, coleta último pipeline
# e monta JSON. Saída no STDOUT e em $OUT_JSON.
set -Eeuo pipefail

: "${GITLAB_URL:?Defina GITLAB_URL (ex: https://gitlab.seudominio.com)}"
: "${GITLAB_TOKEN:?Defina GITLAB_TOKEN (PAT com read_api)}"
: "${GROUP_ID:?Defina GROUP_ID (id numérico ou full_path do grupo raiz)}"

VERBOSE="${VERBOSE:-1}"             # 1 = logs; 0 = silencioso
CURL_FLAGS="${CURL_FLAGS:---retry 3 --connect-timeout 10 --max-time 60 --compressed}"
OUT_JSON="${OUT_JSON:-gitlab_group_audit.json}"

API="$GITLAB_URL/api/v4"
HDR_AUTH=("PRIVATE-TOKEN: $GITLAB_TOKEN")

TMP_DIR="$(mktemp -d)"
PROJECTS_ND="$TMP_DIR/projects.ndjson"
GROUPS_ND="$TMP_DIR/groups.ndjson"

[[ "${TRACE:-0}" == "1" ]] && set -x

log(){ [[ "$VERBOSE" == "1" ]] && printf "[%(%F %T)T] %s\n" -1 "$*" >&2 || true; }
cleanup(){ rm -rf "$TMP_DIR"; }
trap 'rc=$?; echo "[ERRO] rc=$rc | linha ${BASH_LINENO[0]} | cmd: ${BASH_COMMAND}" >&2' ERR
trap 'cleanup' EXIT

need(){ command -v "$1" >/dev/null 2>&1 || { echo "ERRO: '$1' não encontrado." >&2; exit 1; }; }
need curl
need jq

urlenc(){ jq -rn --arg x "$1" '$x|@uri'; }

api_get(){
  local path="$1"; local query="${2:-}"
  local url="$API$path"; [[ -n "$query" ]] && url="$url?$query"
  log "GET $url"
  curl -sS $CURL_FLAGS -H "${HDR_AUTH[@]}" "$url"
}

api_get_paginated(){
  local path="$1"; local query="${2:-}"; local page=1; local all="[]"
  while :; do
    local url="$API$path?per_page=100&page=$page"
    [[ -n "$query" ]] && url="$url&$query"
    log "GET $url"
    local hdr body
    hdr="$(mktemp)"
    body="$(curl -sS $CURL_FLAGS -D "$hdr" -H "${HDR_AUTH[@]}" "$url")" || body='[]'
    # Normaliza para array e agrega sem passar JSON gigante por argv
    all="$(jq -cs 'add' \
          <(printf '%s' "$all") \
          <(printf '%s' "$body" | jq -c 'if type=="array" then . else [] end' 2>/dev/null || printf '[]'))"
    local next
    next="$(awk -F': ' 'tolower($1)=="x-next-page"{gsub("\r","",$2);print $2}' "$hdr" || true)"
    rm -f "$hdr"
    [[ -z "${next:-}" ]] && { printf '%s' "$all"; return; }
    page="$next"
  done
}

extract_apm_id(){
  local name="$1"
  if [[ "$name" =~ ^[[:space:]]*([0-9]+)[[:space:]]*-[[:space:]]*.+$ ]]; then
    echo "${BASH_REMATCH[1]}"
  else
    echo ""
  fi
}

# mapas
declare -A G_NAME G_PATH G_URL G_PARENT G_APM

# grupo raiz
log "Carregando grupo raiz: $GROUP_ID"
root="$(api_get "/groups/$(urlenc "$GROUP_ID")")"
root_id="$(printf '%s' "$root" | jq -r '.id')"
G_NAME["$root_id"]="$(printf '%s' "$root" | jq -r '.name')"
G_PATH["$root_id"]="$(printf '%s' "$root" | jq -r '.full_path')"
G_URL["$root_id"]="$(printf '%s' "$root" | jq -r '.web_url')"
G_PARENT["$root_id"]=""

# BFS subgrupos (robusto a respostas não-array)
declare -A VISITED
queue=("$root_id")
all_groups=()
while ((${#queue[@]})); do
  gid="${queue[0]}"; queue=("${queue[@]:1}")
  [[ -n "${VISITED[$gid]:-}" ]] && continue
  VISITED["$gid"]=1
  all_groups+=("$gid")

  log "Subgrupos de ${G_PATH[$gid]} (id=$gid)…"
  subs="$(api_get_paginated "/groups/$gid/subgroups")"
  # Carrega metadados dos subgrupos desta página só uma vez
  mapfile -t ids < <(printf '%s' "$subs" | jq -r 'if type=="array" then .[].id else empty end')
  if ((${#ids[@]})); then
    # Indexa por id para não re-jq em loop
    for sgid in "${ids[@]}"; do
      sg="$(printf '%s' "$subs" | jq -c --argjson i "$sgid" '.[] | select(.id==$i)')"
      G_NAME["$sgid"]="$(printf '%s' "$sg" | jq -r '.name')"
      G_PATH["$sgid"]="$(printf '%s' "$sg" | jq -r '.full_path')"
      G_URL["$sgid"]="$(printf '%s' "$sg" | jq -r '.web_url')"
      G_PARENT["$sgid"]="$(printf '%s' "$sg" | jq -r '.parent_id // ""')"
      queue+=("$sgid")
      log "  + ${G_PATH[$sgid]} (id=$sgid)"
    done
  fi
done
log "Total de grupos (inclui raiz): ${#all_groups[@]}"

# salvar grupos + APM herdado
for gid in "${all_groups[@]}"; do
  cur="$gid"; found=""
  while [[ -n "$cur" ]]; do
    try="$(extract_apm_id "${G_NAME[$cur]}")"
    if [[ -n "$try" ]]; then found="$try"; break; fi
    cur="${G_PARENT[$cur]:-}"
  done
  G_APM["$gid"]="$found"

  jq -n \
    --arg id_str "$gid" \
    --arg name "${G_NAME[$gid]}" \
    --arg full_path "${G_PATH[$gid]}" \
    --arg web_url "${G_URL[$gid]}" \
    --arg parent_id_str "${G_PARENT[$gid]:-}" \
    --arg apm_id_str "$found" '
    def null_if_empty: if . == "" then null else . end;
    { id:$id_str, name:$name, full_path:$full_path, web_url:$web_url,
      parent_id: ($parent_id_str | null_if_empty),
      apm_id:    ($apm_id_str    | null_if_empty) }' >> "$GROUPS_ND"
done

# coletores (mantidos para logs; agregação final será em jq)
declare -A APM_TOTAL APM_WITH_PIPE APM_WITH_APPVARS
declare -A APM_STATUS
if (( BASH_VERSINFO[0] > 4 || ( BASH_VERSINFO[0] == 4 && BASH_VERSINFO[1] >= 3 ) )); then
  inc(){ local -n ref="$1"; local key="$2"; local cur="${ref[$key]:-0}"; cur=$((cur+1)); ref["$key"]="$cur"; }
else
  inc(){ local arr="$1" key="$2" cur; eval 'cur=${'"$arr"'[$key]:-0}'; cur=$((cur+1)); eval "$arr[\"\$key\"]=\$cur"; }
fi

# projetos diretos
for gid in "${all_groups[@]}"; do
  gpath="${G_PATH[$gid]}"; gurl="${G_URL[$gid]}"; gapm="${G_APM[$gid]}"; [[ -z "$gapm" ]] && gapm="(sem_apm)"
  projs="$(api_get_paginated "/groups/$gid/projects" "with_shared=false&include_subgroups=false")"
  # Garante array
  pcount="$(printf '%s' "$projs" | jq 'if type=="array" then length else 0 end')"
  log "Grupo ${gpath} (APM=$gapm): $pcount projeto(s) diretos"
  (( pcount == 0 )) && continue

  for ((i=0;i<pcount;i++)); do
    p="$(printf '%s' "$projs" | jq ".[$i]")"
    pid="$(printf '%s' "$p" | jq -r '.id')"
    ppath="$(printf '%s' "$p" | jq -r '.path_with_namespace')"
    pweb="$(printf '%s' "$p" | jq -r '.web_url')"
    dbranch="$(printf '%s' "$p" | jq -r '.default_branch // empty')"

    # ===== pipelines (normalização) =====
    pls_raw="$(api_get "/projects/$pid/pipelines" "per_page=1&order_by=updated_at&sort=desc" || true)"
    pls="$(printf '%s' "${pls_raw:-[]}" | jq -c 'if type=="array" then . else [] end' 2>/dev/null || printf '[]')"
    plen="$(printf '%s' "$pls" | jq 'length')"

    has_general="false"
    pl_id=""; pl_status="none"; pl_ref=""; pl_user=""; pl_created=""; pl_updated=""; pl_url=""
    appflag="false"; app_name_val=""; app_version_val=""

    if (( plen > 0 )); then
      has_general="true"
      pl_id="$(printf '%s' "$pls" | jq -r '.[0].id')"
      pl_status="$(printf '%s' "$pls" | jq -r '.[0].status')"
      pl_ref="$(printf '%s' "$pls" | jq -r '.[0].ref')"
      pl_created="$(printf '%s' "$pls" | jq -r '.[0].created_at')"
      pl_updated="$(printf '%s' "$pls" | jq -r '.[0].updated_at')"
      pl_user="$(printf '%s' "$pls" | jq -r '.[0].user.username // .[0].user.name // ""')"
      pl_url="$pweb/-/pipelines/$pl_id"

      vars="$(api_get "/projects/$pid/pipelines/$pl_id/variables" || true)"
      vars="$(printf '%s' "${vars:-[]}" | jq -c 'if type=="array" then . else [] end' 2>/dev/null || printf '[]')"
      app_name_val="$(printf '%s' "$vars" | jq -r '.[]|select(.key=="APP_NAME")|.value' | head -n1 || true)"
      app_version_val="$(printf '%s' "$vars" | jq -r '.[]|select(.key=="APP_VERSION")|.value' | head -n1 || true)"
      [[ -n "$app_name_val" || -n "$app_version_val" ]] && appflag="true"
    fi

    # NDJSON projeto
    has_general_bool=$([[ "$has_general" == "true" ]] && echo true || echo false)
    has_appvars_bool=$([[ "$appflag" == "true" ]] && echo true || echo false)

    jq -n \
      --arg apm_id "$gapm" \
      --arg group_id_str "$gid" \
      --arg group_full_path "$gpath" \
      --arg group_web_url "$gurl" \
      --arg project_id_str "$pid" \
      --arg project_pwn "$ppath" \
      --arg project_web_url "$pweb" \
      --arg default_branch "$dbranch" \
      --arg has_general_pipeline_str "$has_general_bool" \
      --arg latest_pipeline_id_str "$pl_id" \
      --arg latest_pipeline_status "$pl_status" \
      --arg latest_pipeline_ref "$pl_ref" \
      --arg latest_pipeline_user "$pl_user" \
      --arg latest_pipeline_created_at "$pl_created" \
      --arg latest_pipeline_updated_at "$pl_updated" \
      --arg latest_pipeline_web_url "$pl_url" \
      --arg has_app_vars_str "$has_appvars_bool" \
      --arg app_name_value "$app_name_val" \
      --arg app_version_value "$app_version_val" '
      def null_if_empty: if . == "" then null else . end;
      def to_bool: if . == "true" then true elif . == "false" then false else false end;
      {
        apm_id: $apm_id,
        group: { id:$group_id_str, full_path:$group_full_path, web_url:$group_web_url },
        project: {
          id:$project_id_str, path_with_namespace:$project_pwn, web_url:$project_web_url,
          default_branch: ($default_branch | null_if_empty)
        },
        pipelines: {
          has_general_pipeline: ($has_general_pipeline_str | to_bool),
          latest: (
            ($latest_pipeline_id_str | null_if_empty) as $pid
            | if $pid == null or $latest_pipeline_status == "none" then null else {
                id:$latest_pipeline_id_str, status:$latest_pipeline_status,
                ref:($latest_pipeline_ref|null_if_empty),
                user:($latest_pipeline_user|null_if_empty),
                created_at:($latest_pipeline_created_at|null_if_empty),
                updated_at:($latest_pipeline_updated_at|null_if_empty),
                web_url:($latest_pipeline_web_url|null_if_empty),
                variables:{ APP_NAME:($app_name_value|null_if_empty),
                            APP_VERSION:($app_version_value|null_if_empty) }
              } end
          ),
          has_APP_NAME_or_APP_VERSION: ($has_app_vars_str | to_bool)
        }
      }' >> "$PROJECTS_ND"

    inc APM_TOTAL "$gapm"
    [[ "$has_general" == "true" ]] && inc APM_WITH_PIPE "$gapm"
    [[ "$appflag" == "true" ]] && inc APM_WITH_APPVARS "$gapm"
    inc APM_STATUS "$gapm|$pl_status" || true

    log "  - $ppath | general_pipeline=$has_general status=$pl_status APP_NAME=${app_name_val:-} APP_VERSION=${app_version_val:-}"
  done
done

# =========================
# montar final (SEM blobs)
# =========================
log "Montando JSON final: $OUT_JSON"

# Converte NDJSON -> JSON (array) em arquivos
GROUPS_JSON="$TMP_DIR/groups.json"
PROJECTS_JSON="$TMP_DIR/projects.json"

[ -s "$GROUPS_ND" ]   && jq -s '.' "$GROUPS_ND"   > "$GROUPS_JSON"   || printf '[]' > "$GROUPS_JSON"
[ -s "$PROJECTS_ND" ] && jq -s '.' "$PROJECTS_ND" > "$PROJECTS_JSON" || printf '[]' > "$PROJECTS_JSON"

# Resumo geral direto do NDJSON
OVERALL_JSON="$TMP_DIR/overall.json"
jq -s '
  def known: ["success","failed","running","canceled","skipped","manual","pending","created","scheduled","none"];
  def empty_counts: {success:0, failed:0, running:0, canceled:0, skipped:0, manual:0, pending:0, created:0, scheduled:0, none:0, other:0};
  def norm(s): (known | index(s)) as $i | if $i==null then "other" else s end;

  . as $items
  | {
      total_projects: ($items|length),
      with_general_pipeline: ([$items[] | select(.pipelines.has_general_pipeline==true)] | length),
      with_APP_NAME_or_APP_VERSION: ([$items[] | select(.pipelines.has_APP_NAME_or_APP_VERSION==true)] | length),
      status_counts:
        (reduce ($items[] | (.pipelines.latest?.status // "none") | norm(.)) as $s
          (empty_counts; .[$s] += 1))
    }
' "$PROJECTS_ND" > "$OVERALL_JSON"

# Resumo por APM (100% em jq)
APM_SUMMARY_JSON="$TMP_DIR/summary_by_apm.json"
jq -s '
  def known: ["success","failed","running","canceled","skipped","manual","pending","created","scheduled","none"];
  def empty_counts: {success:0, failed:0, running:0, canceled:0, skipped:0, manual:0, pending:0, created:0, scheduled:0, none:0, other:0};
  def norm(s): (known | index(s)) as $i | if $i==null then "other" else s end;

  sort_by(.apm_id)
  | group_by(.apm_id)
  | map({
      apm_id: (.[0].apm_id),
      total_projects: (length),
      with_general_pipeline: ([.[] | select(.pipelines.has_general_pipeline==true)] | length),
      with_APP_NAME_or_APP_VERSION: ([.[] | select(.pipelines.has_APP_NAME_or_APP_VERSION==true)] | length),
      status_counts:
        (reduce (.[] | (.pipelines.latest?.status // "none") | norm(.)) as $s
          (empty_counts; .[$s] += 1))
    })
' "$PROJECTS_JSON" > "$APM_SUMMARY_JSON"

# Monta o JSON final usando --slurpfile (evita --argjson gigante)
final_json="$(
  jq -n \
    --arg generated_at "$(date -Iseconds)" \
    --arg gitlab_url "$GITLAB_URL" \
    --arg root_group_id "$root_id" \
    --arg root_group_full_path "${G_PATH[$root_id]}" \
    --arg root_group_web_url "${G_URL[$root_id]}" \
    --slurpfile groups "$GROUPS_JSON" \
    --slurpfile projects "$PROJECTS_JSON" \
    --slurpfile summary_by_apm "$APM_SUMMARY_JSON" \
    --slurpfile summary_overall "$OVERALL_JSON" '
    {
      generated_at: $generated_at,
      gitlab_url: $gitlab_url,
      root_group: { id:$root_group_id, full_path:$root_group_full_path, web_url:$root_group_web_url },
      groups: $groups[0],
      projects: $projects[0],
      summary: { by_apm: $summary_by_apm[0], overall: $summary_overall[0] }
    }'
)"

printf '%s\n' "$final_json" | tee "$OUT_JSON" >/dev/null
log "OK: $OUT_JSON"
echo "✅ Pronto: $OUT_JSON"
