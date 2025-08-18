#!/usr/bin/env bash
set -euo pipefail

# ---------- UI helpers ----------
supports_color() { [[ -t 1 && "${TERM:-}" != "dumb" ]]; }
if supports_color; then
  BOLD=$(tput bold); DIM=$(tput dim); RESET=$(tput sgr0)
  BLUE=$(tput setaf 4); GREEN=$(tput setaf 2); YELLOW=$(tput setaf 3); RED=$(tput setaf 1); CYAN=$(tput setaf 6)
else
  BOLD=""; DIM=""; RESET=""; BLUE=""; GREEN=""; YELLOW=""; RED=""; CYAN=""
fi
ts() { date +"%Y-%m-%d %H:%M:%S"; }
log() {
  local level="$1"; shift
  local color="$RESET"
  case "$level" in
    INFO)  color="$BLUE";;
    OK)    color="$GREEN";;
    WARN)  color="$YELLOW";;
    ERROR) color="$RED";;
    DEBUG) color="$CYAN";;
  esac
  echo -e "${color}[$(ts)] ${level}${RESET} $*"
}
hr() { echo -e "${DIM}────────────────────────────────────────────────────────────${RESET}"; }
box() {
  local title="$1"; shift
  hr; echo -e "${BOLD}$title${RESET}"; hr
  [[ $# -gt 0 ]] && printf "%s\n" "$@"
}

# ---------- Input ----------
FILE="${1:-variables.json}"
[[ -f "$FILE" ]] || { log ERROR "Arquivo não encontrado: $FILE"; exit 1; }
log INFO "Processando arquivo: ${BOLD}$FILE${RESET}"

# ---------- Read base fields ----------
PROJECT=$(jq -r '.harnessProject // empty' "$FILE")
WEBHOOK=$(jq -r '.harnessPipelineWebhook // empty' "$FILE")

[[ -n "$PROJECT" ]] || { log ERROR "Campo .harnessProject ausente em $FILE"; exit 1; }
[[ -n "$WEBHOOK" ]] || { log ERROR "Campo .harnessPipelineWebhook ausente em $FILE"; exit 1; }

box "🔎 Metadados"
echo "Harness Project : $PROJECT"
echo "Webhook         : $WEBHOOK"

# ---------- Aggregate arrays from all .variables[] ----------
JSON_SECRET=$(jq -c '[.variables[].secretManager? // []] | add' "$FILE")
JSON_PARAMETER=$(jq -c '[.variables[].parameterManager? // []] | add' "$FILE")
SECRET_LEN=$(jq -r 'length' <<<"$JSON_SECRET")
PARAM_LEN=$(jq -r 'length' <<<"$JSON_PARAMETER")

box "📊 Conteúdo Detectado"
echo "Secrets    : $SECRET_LEN"
echo "Parameters : $PARAM_LEN"

# ---------- Build payload according to presence ----------
declare -A CASE_DESC=(
  ["both"]="TEM secret e TEM parameter"
  ["only_secret"]="TEM secret e NÃO tem parameter"
  ["only_param"]="NÃO tem secret e TEM parameter"
  ["none"]="NÃO tem secret nem parameter"
)

PAYLOAD=""
CASE="none"
if [[ "$SECRET_LEN" -gt 0 && "$PARAM_LEN" -gt 0 ]]; then
  CASE="both"
  PAYLOAD=$(jq -n --arg project "$PROJECT" \
                 --argjson json_secret "$JSON_SECRET" \
                 --argjson json_parameter_manager "$JSON_PARAMETER" \
                 '{project:$project, json_secret:$json_secret, json_parameter_manager:$json_parameter_manager}')
elif [[ "$SECRET_LEN" -gt 0 ]]; then
  CASE="only_secret"
  PAYLOAD=$(jq -n --arg project "$PROJECT" \
                 --argjson json_secret "$JSON_SECRET" \
                 '{project:$project, json_secret:$json_secret}')
elif [[ "$PARAM_LEN" -gt 0 ]]; then
  CASE="only_param"
  PAYLOAD=$(jq -n --arg project "$PROJECT" \
                 --argjson json_parameter_manager "$JSON_PARAMETER" \
                 '{project:$project, json_parameter_manager:$json_parameter_manager}')
fi

box "🧠 Decisão de Payload"
echo "Caso: ${CASE_DESC[$CASE]}"

if [[ "$CASE" == "none" ]]; then
  log WARN "Nada para enviar. Encerrando com sucesso."
  exit 0
fi

# ---------- Pretty preview (redacting values by default) ----------
REDACT="${REDACT:-1}"   # REACT=0 para não redigir
DEBUG="${DEBUG:-0}"
PREVIEW="$PAYLOAD"
if [[ "$REDACT" == "1" && "$DEBUG" != "1" ]]; then
  # tenta mascarar field 'valueIdentifyFromHarness'
  PREVIEW=$(jq 'walk(
    if type=="object" and has("valueIdentifyFromHarness") then
      .valueIdentifyFromHarness = "***"
    else . end
  )' <<<"$PAYLOAD")
fi
box "📝 Payload (preview)"
echo "$PREVIEW" | jq .

# ---------- Send ----------
log INFO "Enviando request…"
HTTP_CODE=""
RESP_BODY=""
# salvar body em arquivo temporário para não quebrar cores
TMP_BODY=$(mktemp)
HTTP_CODE=$(curl -sS -w "%{http_code}" -o "$TMP_BODY" \
  -X POST -H 'content-type: application/json' \
  -d "$PAYLOAD" \
  "$WEBHOOK" ) || { log ERROR "Falha de conexão no curl"; cat "$TMP_BODY" || true; exit 2; }
RESP_BODY="$(cat "$TMP_BODY")"; rm -f "$TMP_BODY"

box "🌐 Resposta do Webhook"
echo "HTTP Status: $HTTP_CODE"
# tenta formatar como JSON; se não der, imprime cru
if jq -e . >/dev/null 2>&1 <<<"$RESP_BODY"; then
  echo "$RESP_BODY" | jq .
else
  echo "$RESP_BODY"
fi

# ---------- Validate ----------
if [[ "$HTTP_CODE" =~ ^2 ]]; then
  log OK "Payload enviado com sucesso."
  # Se a resposta tiver um id/uuid em data, loga
  UUID=$(jq -r '.data.id // .data.uuid // empty' <<<"$RESP_BODY" 2>/dev/null || true)
  [[ -n "$UUID" ]] && log INFO "Exec ID: ${BOLD}$UUID${RESET}"
else
  log ERROR "Servidor retornou status $HTTP_CODE"
  exit 3
fi

# ---------- Debug extra ----------
if [[ "$DEBUG" == "1" ]]; then
  box "🔧 Debug (payload completo, sem redigir)"
  echo "$PAYLOAD" | jq .
fi
