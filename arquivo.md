#!/usr/bin/env bash
set -euo pipefail

: "${GITLAB_URL:?Defina GITLAB_URL (ex: https://gitlab.seudominio.com)}"
: "${GITLAB_TOKEN:?Defina GITLAB_TOKEN (PAT com read_api)}"
: "${GROUP_ID:?Defina GROUP_ID (id numérico ou path do grupo raiz)}"

VERBOSE="${VERBOSE:-1}"            # 1 = logs; 0 = silencioso
CURL_FLAGS="${CURL_FLAGS:---retry 3 --connect-timeout 10 --max-time 60}"
API="$GITLAB_URL/api/v4"
HDR_AUTH=("PRIVATE-TOKEN: $GITLAB_TOKEN")

OUT_CSV="projects_report.csv"
OUT_APM="apm_summary.csv"
OUT_XLSX="gitlab_group_audit.xlsx"

log(){ [[ "$VERBOSE" == "1" ]] && printf "[%(%F %T)T] %s\n" -1 "$*" >&2 || true; }
trap 'echo "[ERRO] Linha $LINENO: comando '\''$BASH_COMMAND'\'' ($?)" >&2' ERR

need(){ command -v "$1" >/dev/null 2>&1 || { echo "ERRO: '$1' não encontrado."; exit 1; }; }
need curl
need jq
need python3

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
    local hdr tmp; hdr="$(mktemp)"
    local body; body="$(curl -sS $CURL_FLAGS -D "$hdr" -H "${HDR_AUTH[@]}" "$url")"
    all="$(jq -cs 'add' <(echo "$all") <(echo "$body"))"
    local next; next="$(awk -F': ' 'tolower($1)=="x-next-page"{gsub("\r","",$2);print $2}' "$hdr")"
    rm -f "$hdr"
    [[ -z "$next" ]] && { echo "$all"; return; }
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

# -------- estruturas --------
declare -A G_NAME G_PATH G_URL G_PARENT G_APM
declare -A APM_TOTAL APM_WITH_PIPE APM_WITH_APPVARS
declare -A APM_STATUS  # chave "APM|status" => count

echo "apm_id,group_id,group_full_path,group_web_url,project_id,project_path_with_namespace,project_web_url,default_branch,has_general_pipeline,latest_pipeline_id,latest_pipeline_status,latest_pipeline_ref,latest_pipeline_user,latest_pipeline_created_at,latest_pipeline_updated_at,latest_pipeline_web_url,has_appname_or_appversion_var,app_name_value,app_version_value" > "$OUT_CSV"

# -------- carrega grupo raiz --------
log "Carregando grupo raiz: $GROUP_ID"
root="$(api_get "/groups/$(urlenc "$GROUP_ID")")"
root_id="$(echo "$root" | jq -r '.id')"
G_NAME["$root_id"]="$(echo "$root" | jq -r '.name')"
G_PATH["$root_id"]="$(echo "$root" | jq -r '.full_path')"
G_URL["$root_id"]="$(echo "$root" | jq -r '.web_url')"
G_PARENT["$root_id"]=""

# -------- BFS de subgrupos --------
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

# -------- calcula APM herdado --------
for gid in "${all_groups[@]}"; do
  # sobe a árvore até achar "APM_ID - Nome"
  cur="$gid"; found=""
  while [[ -n "$cur" ]]; do
    try="$(extract_apm_id "${G_NAME[$cur]}")"
    if [[ -n "$try" ]]; then found="$try"; break; fi
    cur="${G_PARENT[$cur]:-}"
  done
  G_APM["$gid"]="$found"
done

inc(){ # inc <assoc> <key>
  local arr="$1" key="$2" cur=0
  eval "cur=\${$arr[\$key]:-0}"
  ((cur++))
  eval "$arr[\"$key\"]=\$cur"
}

# -------- coleta projetos --------
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

    # Pipelines (último)
    pls="$(api_get "/projects/$pid/pipelines" "per_page=1&order_by=updated_at&sort=desc")"
    plen="$(echo "$pls" | jq 'length')"
    has_general="no"
    pl_id=""; pl_status="none"; pl_ref=""; pl_user=""; pl_created=""; pl_updated=""; pl_url=""
    appflag="no"; app_name_val=""; app_version_val=""

    if (( plen > 0 )); then
      has_general="yes"
      pl_id="$(echo "$pls" | jq -r '.[0].id')"
      pl_status="$(echo "$pls" | jq -r '.[0].status')"
      pl_ref="$(echo "$pls" | jq -r '.[0].ref')"
      pl_created="$(echo "$pls" | jq -r '.[0].created_at')"
      pl_updated="$(echo "$pls" | jq -r '.[0].updated_at')"
      # pega usuário
      pl_user="$(echo "$pls" | jq -r '.[0].user.username // .[0].user.name // ""')"
      pl_url="$pweb/-/pipelines/$pl_id"

      # variáveis do pipeline mais recente
      vars="$(api_get "/projects/$pid/pipelines/$pl_id/variables")" || vars="[]"
      app_name_val="$(echo "$vars" | jq -r '.[]|select(.key=="APP_NAME")|.value' | head -n1 || true)"
      app_version_val="$(echo "$vars" | jq -r '.[]|select(.key=="APP_VERSION")|.value' | head -n1 || true)"
      [[ -n "$app_name_val" || -n "$app_version_val" ]] && appflag="yes"
    fi

    # CSV
    echo "$gapm,$gid,$gpath,$gurl,$pid,$ppath,$pweb,$dbranch,$has_general,$pl_id,$pl_status,$pl_ref,$pl_user,$pl_created,$pl_updated,$pl_url,$appflag,$app_name_val,$app_version_val" >> "$OUT_CSV"

    # agregados por APM
    inc APM_TOTAL "$gapm"
    [[ "$has_general" == "yes" ]] && inc APM_WITH_PIPE "$gapm"
    [[ "$appflag" == "yes" ]] && inc APM_WITH_APPVARS "$gapm"
    inc APM_STATUS "$gapm|$pl_status"
    log "  - $ppath | pipeline=$has_general status=$pl_status APP_NAME=${app_name_val:--} APP_VERSION=${app_version_val:--}"
  done
done

# -------- resumo por APM CSV --------
echo "apm_id,total_projects,with_general_pipeline,with_app_vars(APP_NAME_or_APP_VERSION),status_success,status_failed,status_running,status_canceled,status_skipped,status_manual,status_pending,status_created,status_scheduled,status_none,status_other" > "$OUT_APM"
# ordena as chaves
mapfile -t apms < <(printf "%s\n" "${!APM_TOTAL[@]}" | sort -V)
for apm in "${apms[@]}"; do
  t="${APM_TOTAL[$apm]:-0}"
  wg="${APM_WITH_PIPE[$apm]:-0}"
  wa="${APM_WITH_APPVARS[$apm]:-0}"
  s_success="${APM_STATUS[$apm|success]:-0}"
  s_failed="${APM_STATUS[$apm|failed]:-0}"
  s_running="${APM_STATUS[$apm|running]:-0}"
  s_canceled="${APM_STATUS[$apm|canceled]:-0}"
  s_skipped="${APM_STATUS[$apm|skipped]:-0}"
  s_manual="${APM_STATUS[$apm|manual]:-0}"
  s_pending="${APM_STATUS[$apm|pending]:-0}"
  s_created="${APM_STATUS[$apm|created]:-0}"
  s_scheduled="${APM_STATUS[$apm|scheduled]:-0}"
  s_none="${APM_STATUS[$apm|none]:-0}"
  s_other="${APM_STATUS[$apm|other]:-0}"
  echo "$apm,$t,$wg,$wa,$s_success,$s_failed,$s_running,$s_canceled,$s_skipped,$s_manual,$s_pending,$s_created,$s_scheduled,$s_none,$s_other" >> "$OUT_APM"
done

# -------- XLSX bonito (usa xlsxwriter) --------
log "Gerando Excel estilizado: $OUT_XLSX"
python3 - <<'PY'
import csv, os, xlsxwriter
from datetime import datetime

CSV_PROJECTS = "projects_report.csv"
CSV_APM = "apm_summary.csv"
XLSX = "gitlab_group_audit.xlsx"

wb = xlsxwriter.Workbook(XLSX)

# formatos
fmt_header = wb.add_format({"bold": True, "bg_color": "#F2F2F2", "border":1})
fmt_link   = wb.add_format({"font_color": "blue", "underline": 1})
fmt_ok     = wb.add_format({"font_color":"#2E7D32"})   # verde
fmt_fail   = wb.add_format({"font_color":"#C62828"})   # vermelho
fmt_warn   = wb.add_format({"font_color":"#EF6C00"})   # laranja
fmt_text   = wb.add_format({"text_wrap": True})
fmt_int    = wb.add_format({"num_format": "0"})
fmt_center = wb.add_format({"align":"center"})
fmt_date   = wb.add_format({"num_format":"yyyy-mm-dd hh:mm"})

# --- aba Projetos ---
ws = wb.add_worksheet("Projetos")
with open(CSV_PROJECTS, newline='', encoding='utf-8') as f:
    reader = csv.reader(f)
    rows = list(reader)

# cabeçalho
for j, h in enumerate(rows[0]):
    ws.write(0, j, h, fmt_header)

# índices de colunas (para links/format)
headers = rows[0]
col_idx = {name:i for i,name in enumerate(headers)}

def w(row, col, val, fmt=None):
    ws.write(row, col, val, fmt)

# dados
for i, r in enumerate(rows[1:], start=1):
    # links clicáveis
    g_url = r[col_idx["group_web_url"]]
    p_web = r[col_idx["project_web_url"]]
    pl_url = r[col_idx["latest_pipeline_web_url"]]

    for j, val in enumerate(r):
        fmt = None
        if headers[j] in ("latest_pipeline_created_at","latest_pipeline_updated_at") and val:
            try:
                dt = datetime.fromisoformat(val.replace("Z","+00:00")).astimezone()
                ws.write_datetime(i, j, dt, fmt_date); continue
            except Exception:
                pass

        if headers[j] == "group_web_url" and g_url:
            ws.write_url(i, j, g_url, fmt_link, g_url); continue
        if headers[j] == "project_web_url" and p_web:
            ws.write_url(i, j, p_web, fmt_link, p_web); continue
        if headers[j] == "latest_pipeline_web_url" and pl_url:
            ws.write_url(i, j, pl_url, fmt_link, pl_url); continue

        w(i, j, val, fmt_text)

# largura auto-ajustada (simples)
for j in range(len(headers)):
    length = max([len(str(r[j])) for r in rows[:200]]+[len(headers[j])])
    ws.set_column(j, j, min(max(12, length+2), 60))

# filtros + congelar cabeçalho
ws.autofilter(0, 0, len(rows)-1, len(headers)-1)
ws.freeze_panes(1, 0)

# formatação condicional por status
status_col = col_idx.get("latest_pipeline_status")
if status_col is not None:
    ws.conditional_format(1, status_col, len(rows)-1, status_col,
                          {"type":"text", "criteria":"containing", "value":"success", "format":fmt_ok})
    ws.conditional_format(1, status_col, len(rows)-1, status_col,
                          {"type":"text", "criteria":"containing", "value":"failed", "format":fmt_fail})
    ws.conditional_format(1, status_col, len(rows)-1, status_col,
                          {"type":"text", "criteria":"containing", "value":"running", "format":fmt_warn})

# --- aba Resumo por APM ---
ws2 = wb.add_worksheet("Resumo por APM")
with open(CSV_APM, newline='', encoding='utf-8') as f:
    reader = csv.reader(f)
    rows2 = list(reader)

for j, h in enumerate(rows2[0]): ws2.write(0, j, h, fmt_header)
for i, r in enumerate(rows2[1:], start=1):
    for j, v in enumerate(r):
        if j == 0:
            ws2.write(i, j, v)  # apm_id
        else:
            try:
                ws2.write_number(i, j, int(v), fmt_int)
            except:
                ws2.write(i, j, v)

ws2.autofilter(0, 0, len(rows2)-1, len(rows2[0])-1)
ws2.freeze_panes(1, 0)
for j in range(len(rows2[0])):
    length = max([len(str(r[j])) for r in rows2[:200]]+[len(rows2[0][j])])
    ws2.set_column(j, j, min(max(12, length+2), 45))

# --- aba Resumo Geral (a partir dos dados de projetos) ---
ws3 = wb.add_worksheet("Resumo geral")
# Contagens globais
headers3 = ["metric","value"]
for j,h in enumerate(headers3): ws3.write(0, j, h, fmt_header)

# total projetos
total_projects = max(0, len(rows)-1)
ws3.write(1,0,"total_projects"); ws3.write_number(1,1,total_projects, fmt_int)

# por status
from collections import Counter
status_idx = col_idx["latest_pipeline_status"]
has_gen_idx = col_idx["has_general_pipeline"]
appflag_idx = col_idx["has_appname_or_appversion_var"]

status_counts = Counter(r[status_idx] for r in rows[1:])
row3 = 2
for k,v in sorted(status_counts.items()):
    ws3.write(row3,0,f"status:{k}")
    try: ws3.write_number(row3,1,int(v), fmt_int)
    except: ws3.write(row3,1,str(v))
    row3+=1

# com pipeline geral
with_gen = sum(1 for r in rows[1:] if r[has_gen_idx]=="yes")
ws3.write(row3,0,"with_general_pipeline"); ws3.write_number(row3,1,with_gen, fmt_int); row3+=1
# com APP_NAME/APP_VERSION
with_appvars = sum(1 for r in rows[1:] if r[appflag_idx]=="yes")
ws3.write(row3,0,"with_APP_NAME_or_APP_VERSION"); ws3.write_number(row3,1,with_appvars, fmt_int); row3+=1

ws3.set_column(0,0,40); ws3.set_column(1,1,18)

wb.close()
PY

echo
echo "✅ Concluído!"
echo "• CSV detalhado: $OUT_CSV"
echo "• Resumo por APM: $OUT_APM"
echo "• Excel bonito:   $OUT_XLSX"
