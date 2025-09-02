#!/usr/bin/env bash
# gitlab_group_audit_json.sh 03
# Percorre o grupo raiz (GROUP_ID), varre subgrupos e projetos diretos,
# coleta último pipeline e monta um JSON consolidado.
# Saída: imprime no STDOUT e salva em $OUT_JSON (default: gitlab_group_audit.json)

set -Eeuo pipefail

# ===== Config obrigatória =====
: "${GITLAB_URL:?Defina GITLAB_URL (ex: https://gitlab.seudominio.com)}"
: "${GITLAB_TOKEN:?Defina GITLAB_TOKEN (PAT com read_api)}"
: "${GROUP_ID:?Defina GROUP_ID (id numérico ou full_path do grupo raiz)}"

# ===== Flags/variáveis =====
VERBOSE="${VERBOSE:-1}"             # 1 = logs; 0 = silencioso
CURL_FLAGS="${CURL_FLAGS:---retry 3 --connect-timeout 10 --max-time 60}"
OUT_JSON="${OUT_JSON:-gitlab_group_audit.json}"

API="$GITLAB_URL/api/v4"
HDR_AUTH=("PRIVATE-TOKEN: $GITLAB_TOKEN")

TMP_DIR="$(mktemp -d)"
PROJECTS_ND="$TMP_DIR/projects.ndjson"
GROUPS_ND="$TMP_DIR/groups.ndjson"

# ===== Debug opcional =====
[[ "${TRACE:-0}" == "1" ]] && set -x

# ===== Utilitários =====
log(){ [[ "$VERBOSE" == "1" ]] && printf "[%(%F %T)T] %s\n" -1 "$*" >&2 || true; }
cleanup(){ rm -rf "$TMP_DIR"; }

# Trap de erro (propaga com -E): mostra RC, linha e comando
trap 'rc=$?; echo "[ERRO] rc=$rc | linha ${BASH_LINENO[0]} | cmd: ${BASH_COMMAND}" >&2' ERR
trap 'cleanup' EXIT

need(){ command -v "$1" >/dev/null 2>&1 || { echo "ERRO: '$1' não encontrado." >&2; exit 1; }; }
need curl
need jq

urlenc(){ jq -rn --arg x "$1" '$x|@uri'; }

# GET simples (sem paginação)
api_get(){
  local path="$1"; local query="${2:-}"
  local url="$API$path"; [[ -n "$query" ]] && url="$url?$query"
  log "GET $url"
  curl -sS $CURL_FLAGS -H "${HDR_AUTH[@]}" "$url"
}

# GET paginado (robusto): se resposta não for array, trata como []
api_get_paginated(){
  local path="$1"; local query="${2:-}"; local page=1; local all="[]"
  while :; do
    local url="$API$path?per_page=100&page=$page"
    [[ -n "$query" ]] && url="$url&$query"
    log "GET $url"
    local hdr; hdr="$(mktemp)"
    # Não usar -f aqui: queremos capturar body mesmo em 4xx/5xx e normalizar para [].
    local body; body="$(curl -sS $CURL_FLAGS -D "$hdr" -H "${HDR_AUTH[@]}" "$url")"

    # Soma arrays; se body não for array, vira [] para não quebrar
    all="$(jq -cs 'add' \
          <(echo "$all") \
          <(echo "$body" | jq -c 'if type=="array" then . else [] end' 2>/dev/null || echo '[]'))"

    # Descobre próxima página pelo cabeçalho
    local next; next="$(awk -F': ' 'tolower($1)=="x-next-page"{gsub("\r","",$2);print $2}' "$hdr")"
    rm -f "$hdr"
    [[ -z "$next" ]] && { echo "$all"; return; }
    page="$next"
  done
}

# Extrai APM do nome do grupo: "12345 - Nome"
extract_apm_id(){
  local name="$1"
  if [[ "$name" =~ ^[[:space:]]*([0-9]+)[[:space:]]*-[[:space:]]*.+$ ]]; then
    echo "${BASH_REMATCH[1]}"
  else
    echo ""
  fi
}

# ===== Mapas de grupos =====
declare -A G_NAME G_PATH G_URL G_PARENT G_APM

# ===== Grupo raiz =====
log "Carregando grupo raiz: $GROUP_ID"
root="$(api_get "/groups/$(urlenc "$GROUP_ID")")"
root_id="$(echo "$root" | jq -r '.id')"
G_NAME["$root_id"]="$(echo "$root" | jq -r '.name')"
G_PATH["$root_id"]="$(echo "$root" | jq -r '.full_path')"
G_URL["$root_id"]="$(echo "$root" | jq -r '.web_url')"
G_PARENT["$root_id"]=""

# ===== BFS subgrupos =====
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
  mapfile -t ids < <(echo "$subs" | jq -r '.[].id')
  for sgid in "${ids[@]}"; do
    G_NAME["$sgid"]="$(echo "$subs" | jq -r ".[]|select(.id==$sgid)|.name")"
    G_PATH["$sgid"]="$(echo "$subs" | jq -r ".[]|select(.id==$sgid)|.full_path")"
    G_URL["$sgid"]="$(echo "$subs" | jq -r ".[]|select(.id==$sgid)|.web_url")"
    G_PARENT["$sgid"]="$(echo "$subs" | jq -r ".[]|select(.id==$sgid)|.parent_id // \"\"")"
    queue+=("$sgid")
    log "  + ${G_PATH[$sgid]} (id=$sgid)"
  done
done
log "Total de grupos (inclui raiz): ${#all_groups[@]}"

# ===== APM herdado + salvar groups NDJSON =====
for gid in "${all_groups[@]}"; do
  cur="$gid"; found=""
  while [[ -n "$cur" ]]; do
    try="$(extract_apm_id "${G_NAME[$cur]}")"
    if [[ -n "$try" ]]; then found="$try"; break; fi
    cur="${G_PARENT[$cur]:-}"
  done
  G_APM["$gid"]="$found"

  parent_id_str="${G_PARENT[$gid]:-}"
  apm_id_str="$found"

  jq -n \
    --arg id_str "$gid" \
    --arg name "${G_NAME[$gid]}" \
    --arg full_path "${G_PATH[$gid]}" \
    --arg web_url "${G_URL[$gid]}" \
    --arg parent_id_str "$parent_id_str" \
    --arg apm_id_str "$apm_id_str" '
    def null_if_empty: if . == "" then null else . end;
    {
      id:        $id_str,
      name:      $name,
      full_path: $full_path,
      web_url:   $web_url,
      parent_id: ($parent_id_str | null_if_empty),
      apm_id:    ($apm_id_str    | null_if_empty)
    }' >> "$GROUPS_ND"
done

# ===== Coletores de resumo =====
declare -A APM_TOTAL APM_WITH_PIPE APM_WITH_APPVARS
declare -A APM_STATUS   # chave: "APM|status" => count
inc(){ local arr="$1" key="$2" cur=0; eval "cur=\${$arr[\$key]:-0}"; ((cur++)); eval "$arr[\"$key\"]=\$cur"; }

# ===== Projetos diretos por grupo =====
for gid in "${all_groups[@]}"; do
  gpath="${G_PATH[$gid]}"; gurl="${G_URL[$gid]}"; gapm="${G_APM[$gid]}"
  [[ -z "$gapm" ]] && gapm="(sem_apm)"

  projs="$(api_get_paginated "/groups/$gid/projects" "with_shared=false&include_subgroups=false")"
  pcount="$(echo "$projs" | jq 'length')"
  log "Grupo ${gpath} (APM=$gapm): $pcount projeto(s) diretos"
  (( pcount == 0 )) && continue

  for ((i=0;i<pcount;i++)); do
    p="$(echo "$projs" | jq ".[$i]")"
    pid="$(echo "$p" | jq -r '.id')"
    ppath="$(echo "$p" | jq -r '.path_with_namespace')"
    pweb="$(echo "$p" | jq -r '.web_url')"
    dbranch="$(echo "$p" | jq -r '.default_branch // empty')"

    # Último pipeline (pela atualização)
    pls="$(api_get "/projects/$pid/pipelines" "per_page=1&order_by=updated_at&sort=desc")"
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
      pl_url="$pweb/-/pipelines/$pl_id"

      # Variáveis do pipeline (robustas; se der erro, vira [])
      vars="$(api_get "/projects/$pid/pipelines/$pl_id/variables" || true)"
      vars="$(echo "${vars:-[]}" | jq -c 'if type=="array" then . else [] end' 2>/dev/null || echo '[]')"
      app_name_val="$(echo "$vars" | jq -r '.[]|select(.key=="APP_NAME")|.value' | head -n1 || true)"
      app_version_val="$(echo "$vars" | jq -r '.[]|select(.key=="APP_VERSION")|.value' | head -n1 || true)"
      if [[ -n "$app_name_val" || -n "$app_version_val" ]]; then
        appflag="true"
      fi
    fi

    # NDJSON por projeto
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
      def to_bool:
        if . == "true" then true
        elif . == "false" then false
        else false end;

      {
        apm_id: $apm_id,
        group: {
          id:        $group_id_str,
          full_path: $group_full_path,
          web_url:   $group_web_url
        },
        project: {
          id:                  $project_id_str,
          path_with_namespace: $project_pwn,
          web_url:             $project_web_url,
          default_branch:      ($default_branch | null_if_empty)
        },
        pipelines: {
          has_general_pipeline: ($has_general_pipeline_str | to_bool),
          latest: (
            ($latest_pipeline_id_str | null_if_empty) as $pid
            | if $pid == null or $latest_pipeline_status == "none"
              then null
              else {
                id:        $latest_pipeline_id_str,
                status:    $latest_pipeline_status,
                ref:       ($latest_pipeline_ref       | null_if_empty),
                user:      ($latest_pipeline_user      | null_if_empty),
                created_at:($latest_pipeline_created_at| null_if_empty),
                updated_at:($latest_pipeline_updated_at| null_if_empty),
                web_url:   ($latest_pipeline_web_url   | null_if_empty),
                variables: {
                  APP_NAME:    ($app_name_value    | null_if_empty),
                  APP_VERSION: ($app_version_value | null_if_empty)
                }
              }
            end
          ),
          has_APP_NAME_or_APP_VERSION: ($has_app_vars_str | to_bool)
        }
      }' >> "$PROJECTS_ND"

    # Agregadores
    inc APM_TOTAL "$gapm"
    [[ "$has_general" == "true" ]] && inc APM_WITH_PIPE "$gapm"
    [[ "$appflag" == "true" ]] && inc APM_WITH_APPVARS "$gapm"
    inc APM_STATUS "$gapm|$pl_status"

    log "  - $ppath | general_pipeline=$has_general status=$pl_status APP_NAME=${app_name_val:-} APP_VERSION=${app_version_val:-}"
  done
done

# ===== Montar JSON final =====
log "Montando JSON final: $OUT_JSON"

groups_json="$( [ -s "$GROUPS_ND" ] && jq -s '.' "$GROUPS_ND" || echo '[]' )"
projects_json="$( [ -s "$PROJECTS_ND" ] && jq -s '.' "$PROJECTS_ND" || echo '[]' )"

# Resumo por APM
apm_keys="$(printf "%s\n" "${!APM_TOTAL[@]}" | sort -V || true)"
apm_summary="[]"
while IFS= read -r apm; do
  [[ -z "$apm" ]] && continue
  t="${APM_TOTAL[$apm]:-0}"
  wg="${APM_WITH_PIPE[$apm]:-0}"
  wa="${APM_WITH_APPVARS[$apm]:-0}"

  statuses=(success failed running canceled skipped manual pending created scheduled none other)
  counts_obj="{}"
  for s in "${statuses[@]}"; do
    v="${APM_STATUS["$apm|$s"]:-0}"
    counts_obj="$(jq -n --argjson obj "$counts_obj" --arg s "$s" --argjson v "$v" '$obj + {($s):$v}')" 
  done

  apm_summary="$(jq -n \
    --arg apm_id "$apm" \
    --argjson total "$t" \
    --argjson with_general_pipeline "$wg" \
    --argjson with_APP_vars "$wa" \
    --argjson status_counts "$counts_obj" \
    --argjson cur "$apm_summary" \
    '$cur + [{apm_id:$apm_id, total_projects:$total, with_general_pipeline:$with_general_pipeline, with_APP_NAME_or_APP_VERSION:$with_APP_vars, status_counts:$status_counts}]'
  )"
done <<< "$apm_keys"

# Resumo geral a partir de projects_json
overall="$(jq -n --argjson items "$projects_json" '
  def count(f): reduce ( .[] | select(f) ) as $i (0; .+1);
  def status_counts:
    reduce (.[] | .pipelines.latest?.status // "none") as $s ({}; . + {($s): ( .[$s] // 0 ) + 1});
  {
    total_projects: (items|length),
    with_general_pipeline: (items|count(.pipelines.has_general_pipeline == true)),
    with_APP_NAME_or_APP_VERSION: (items|count(.pipelines.has_APP_NAME_or_APP_VERSION == true)),
    status_counts: (items|status_counts)
  }')"

# ===== Objeto final (imprime e salva) =====
final_json="$(
  jq -n \
    --arg generated_at "$(date -Iseconds)" \
    --arg gitlab_url "$GITLAB_URL" \
    --arg root_group_id "$root_id" \
    --arg root_group_full_path "${G_PATH[$root_id]}" \
    --arg root_group_web_url "${G_URL[$root_id]}" \
    --argjson groups "$groups_json" \
    --argjson projects "$projects_json" \
    --argjson summary_by_apm "$apm_summary" \
    --argjson summary_overall "$overall" \
    '{
      generated_at: $generated_at,
      gitlab_url: $gitlab_url,
      root_group: { id: $root_group_id, full_path: $root_group_full_path, web_url: $root_group_web_url },
      groups: $groups,
      projects: $projects,
      summary: { by_apm: $summary_by_apm, overall: $summary_overall }
    }'
)"

printf '%s\n' "$final_json" | tee "$OUT_JSON" >/dev/null
log "OK: $OUT_JSON"
echo "✅ Pronto: $OUT_JSON"
