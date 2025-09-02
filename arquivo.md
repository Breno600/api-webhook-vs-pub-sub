#!/usr/bin/env bash
set -euo pipefail

# ========= CONFIG =========
# Ex.: https://gitlab.com/api/v4  ou  https://gitlab.seu-dominio/api/v4
: "${GITLAB_BASE_URL:?Defina GITLAB_BASE_URL (ex.: https://gitlab.com/api/v4)}"
# Token de acesso (PAT) com permissões de leitura em grupos e projetos
: "${GITLAB_TOKEN:?Defina GITLAB_TOKEN (Personal Access Token)}"
# ID numérico do grupo "raiz" onde estão seus subgrupos APM_ID - Nome
: "${GROUP_ID:?Defina GROUP_ID (id numérico do grupo)}"

# Opcional: pausa curta entre chamadas para evitar rate limit
SLEEP_BETWEEN_CALLS="${SLEEP_BETWEEN_CALLS:-0.15}"

# Opcional: caminho para CSV de saída (deixe vazio para não gerar)
CSV_OUT="${CSV_OUT:-gitlab_apm_audit.csv}"
# ==========================

hdr_auth=(-H "PRIVATE-TOKEN: ${GITLAB_TOKEN}")

api_get() {
  local url="$1"
  curl -sS --retry 3 --retry-delay 1 "${hdr_auth[@]}" "$url"
}

# Coleta todos os projetos (paginando) do grupo, incluindo subgrupos
fetch_all_projects_json() {
  local page=1 per_page=100
  local acc="[]"
  while :; do
    local url="${GITLAB_BASE_URL}/groups/${GROUP_ID}/projects?include_subgroups=true&simple=true&per_page=${per_page}&page=${page}"
    local body
    body="$(api_get "$url")"
    local count
    count="$(jq 'length' <<<"$body")"
    acc="$(jq -s '.[0] + .[1]' <(echo "$acc") <(echo "$body"))"
    if [[ "$count" -lt "$per_page" ]]; then
      break
    fi
    page=$((page+1))
    sleep "$SLEEP_BETWEEN_CALLS"
  done
  echo "$acc"
}

# Extrai o APM ID a partir do path/name do namespace
# Regra: pega o primeiro segmento do caminho cujo início combine ^[0-9]+(\s*)-
extract_apm_id() {
  local full_path="$1"   # exemplo: "parent/123 - Equipe X/proj"
  IFS='/' read -r -a parts <<< "$full_path"
  for seg in "${parts[@]}"; do
    if [[ "$seg" =~ ^([0-9]+)[[:space:]]*-[[:space:]]*.*$ ]]; then
      echo "${BASH_REMATCH[1]}"
      return 0
    fi
  done
  echo "UNKNOWN"
}

# Busca status da pipeline mais recente do projeto
latest_pipeline_status() {
  local project_id="$1"
  local url="${GITLAB_BASE_URL}/projects/${project_id}/pipelines?per_page=1&order_by=updated_at&sort=desc"
  local body
  body="$(api_get "$url" || echo "[]")"
  local len
  len="$(jq 'length' <<<"$body")"
  if [[ "$len" -eq 0 ]]; then
    echo "no_pipeline"
    return 0
  fi
  local status
  status="$(jq -r '.[0].status // "unknown"' <<<"$body")"
  # status comum: success, failed, canceled, running, pending, skipped, manual, created
  echo "$status"
}

# -------- MAIN --------
echo "Carregando projetos do grupo ${GROUP_ID}..."
projects_json="$(fetch_all_projects_json)"
total_projects="$(jq 'length' <<<"$projects_json")"
echo "Projetos encontrados: ${total_projects}"

# CSV header (opcional)
if [[ -n "${CSV_OUT:-}" ]]; then
  echo "apm_id,project_id,project_name,path_with_namespace,default_branch,latest_pipeline_status" > "$CSV_OUT"
fi

# Mapas associativos por APM
declare -A apm_repo_count
declare -A apm_status_count   # chave composta: "<APM>::<STATUS>"
declare -A apm_with_pipeline

# Guarda detalhes por projeto
printf "\n=== Detalhes por projeto ===\n"
printf "%-10s | %-10s | %-40s | %-12s | %s\n" "APM_ID" "ProjID" "Path" "DefaultBr" "LastPipeline"

# Itera projetos
for row in $(jq -c '.[]' <<<"$projects_json"); do
  project_id="$(jq -r '.id' <<<"$row")"
  path_with_ns="$(jq -r '.path_with_namespace' <<<"$row")"
  default_branch="$(jq -r '.default_branch // "main"' <<<"$row")"

  # Em alguns payloads, "namespace.full_path" está presente; senão, use path_with_namespace
  ns_full_path="$(jq -r '.namespace.full_path // empty' <<<"$row")"
  [[ -z "$ns_full_path" ]] && ns_full_path="$path_with_ns"

  apm_id="$(extract_apm_id "$ns_full_path")"
  (( apm_repo_count["$apm_id"]++ )) || true

  # Status da última pipeline
  status="$(latest_pipeline_status "$project_id" || echo "unknown")"
  (( apm_status_count["${apm_id}::${status}"]++ )) || true
  if [[ "$status" != "no_pipeline" ]]; then
    (( apm_with_pipeline["$apm_id"]++ )) || true
  fi

  printf "%-10s | %-10s | %-40s | %-12s | %s\n" "$apm_id" "$project_id" "$(printf '%.40s' "$path_with_ns")" "$default_branch" "$status"

  # CSV opcional
  if [[ -n "${CSV_OUT:-}" ]]; then
    # Escapar vírgulas no path com aspas
    printf '%s,"%s","%s","%s","%s","%s"\n' \
      "$apm_id" "$project_id" "$(
        jq -rn --arg s "$row" '$s' >/dev/null 2>&1; echo "$path_with_ns"
      )" "$path_with_ns" "$default_branch" "$status" >> "$CSV_OUT"
  fi
  sleep "$SLEEP_BETWEEN_CALLS"
done

# -------- Resumo por APM --------
printf "\n=== Resumo por APM ID ===\n"
printf "%-10s | %6s | %8s | %s\n" "APM_ID" "Repos" "c/CI" "Status breakdown"

# Coletar APM IDs únicos
mapfile -t apm_ids < <(printf "%s\n" "${!apm_repo_count[@]}" | sort -V)
for apm in "${apm_ids[@]}"; do
  repos="${apm_repo_count[$apm]}"
  with_ci="${apm_with_pipeline[$apm]:-0}"

  # Monta breakdown por status
  # Vamos varrer chaves que começam com "${apm}::"
  statuses=()
  while IFS= read -r k; do
    if [[ "$k" == "${apm}::"* ]]; then
      st="${k#${apm}::}"
      cnt="${apm_status_count[$k]}"
      statuses+=("${st}=${cnt}")
    fi
  done < <(printf "%s\n" "${!apm_status_count[@]}")

  IFS=', ' read -r -a statuses_sorted < <(printf "%s\n" "${statuses[@]}" | sort)
  breakdown="$(IFS=', '; echo "${statuses_sorted[*]}")"

  printf "%-10s | %6s | %8s | %s\n" "$apm" "$repos" "$with_ci" "$breakdown"
done

if [[ -n "${CSV_OUT:-}" ]]; then
  echo -e "\nCSV gerado em: ${CSV_OUT}"
fi
