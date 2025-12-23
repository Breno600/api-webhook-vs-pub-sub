#!/usr/bin/env bash
set -euo pipefail
set +x

#############################################
# INPUTS obrigatórios
#############################################
: "${AWS_REGION:?AWS_REGION obrigatório}"
: "${ASSUME_ROLE_ARN:?ASSUME_ROLE_ARN obrigatório}"
: "${ASSUME_ROLE_EXTERNAL_ID:?ASSUME_ROLE_EXTERNAL_ID obrigatório}"
: "${SECRET_NAME:?SECRET_NAME obrigatório}"

# Credenciais base (vindas do Harness Secret Manager)
: "${BASE_AWS_ACCESS_KEY_ID:?BASE_AWS_ACCESS_KEY_ID obrigatório}"
: "${BASE_AWS_SECRET_ACCESS_KEY:?BASE_AWS_SECRET_ACCESS_KEY obrigatório}"
BASE_AWS_SESSION_TOKEN="${BASE_AWS_SESSION_TOKEN:-}"

# Payload em pares (um-liner com *** separando)
: "${PAIRS:?PAIRS obrigatório}"
DELIM="${DELIM:-***}"

#############################################
# 1) Assume Role (precisa das credenciais base)
#############################################
export AWS_ACCESS_KEY_ID="$BASE_AWS_ACCESS_KEY_ID"
export AWS_SECRET_ACCESS_KEY="$BASE_AWS_SECRET_ACCESS_KEY"
if [[ -n "$BASE_AWS_SESSION_TOKEN" ]]; then
  export AWS_SESSION_TOKEN="$BASE_AWS_SESSION_TOKEN"
else
  unset AWS_SESSION_TOKEN || true
fi

SESSION_NAME="harness-secrets-$(date +%s)"

ASSUME_JSON="$(aws sts assume-role \
  --region "$AWS_REGION" \
  --role-arn "$ASSUME_ROLE_ARN" \
  --role-session-name "$SESSION_NAME" \
  --external-id "$ASSUME_ROLE_EXTERNAL_ID" \
  --duration-seconds 3600)"

export AWS_ACCESS_KEY_ID="$(printf '%s' "$ASSUME_JSON" | python3 -c "import sys,json;print(json.load(sys.stdin)['Credentials']['AccessKeyId'])")"
export AWS_SECRET_ACCESS_KEY="$(printf '%s' "$ASSUME_JSON" | python3 -c "import sys,json;print(json.load(sys.stdin)['Credentials']['SecretAccessKey'])")"
export AWS_SESSION_TOKEN="$(printf '%s' "$ASSUME_JSON" | python3 -c "import sys,json;print(json.load(sys.stdin)['Credentials']['SessionToken'])")"

aws sts get-caller-identity --region "$AWS_REGION" >/dev/null
echo "OK: Role assumida com sucesso."

#############################################
# 2) Montar JSON do Secret a partir de PAIRS
#    - valida keys (permite _ . -)
#    - não imprime valores
#############################################
SECRET_JSON="$(PAIRS_RAW="$PAIRS" DELIM="$DELIM" python3 - <<'PY'
import os, re, sys, json, hashlib

raw = os.environ.get("PAIRS_RAW","")
delim = os.environ.get("DELIM","***")

parts = [p.strip() for p in raw.split(delim)]
obj = {}

key_re = re.compile(r"^[A-Za-z_][A-Za-z0-9_.-]*$")  # aceita hífen

for part in parts:
    if not part:
        continue
    if "=" not in part:
        print(f"ERRO: item sem '=': {part}", file=sys.stderr)
        sys.exit(1)
    k, v = part.split("=", 1)
    k = k.strip()
    v = v.strip()

    if not k or not key_re.match(k):
        print(f"ERRO: key inválida: '{k}'", file=sys.stderr)
        sys.exit(1)

    obj[k] = v

# validação segura (sem mostrar valor)
print("--- VALIDAÇÃO (sem mostrar valor) ---", file=sys.stderr)
for k, v in obj.items():
    h = hashlib.sha256(v.encode("utf-8")).hexdigest()
    print(f"{k}: len={len(v)} sha256={h}", file=sys.stderr)

print(json.dumps(obj, ensure_ascii=False))
PY
)"

# limite do Secrets Manager é ~64KB por SecretString
SECRET_LEN_BYTES="$(printf '%s' "$SECRET_JSON" | wc -c | tr -d ' ')"
if (( SECRET_LEN_BYTES > 60000 )); then
  echo "ERRO: SecretString ficou muito grande (${SECRET_LEN_BYTES} bytes). Secrets Manager tem limite ~64KB."
  exit 1
fi

echo "OK: SECRET_JSON pronto (bytes=${SECRET_LEN_BYTES})."

#############################################
# 3) UPSERT no AWS Secrets Manager
#############################################
if aws secretsmanager describe-secret --region "$AWS_REGION" --secret-id "$SECRET_NAME" >/dev/null 2>&1; then
  echo "Secret existe. Atualizando valor..."
  aws secretsmanager put-secret-value \
    --region "$AWS_REGION" \
    --secret-id "$SECRET_NAME" \
    --secret-string "$SECRET_JSON" >/dev/null
else
  echo "Secret não existe. Criando..."
  aws secretsmanager create-secret \
    --region "$AWS_REGION" \
    --name "$SECRET_NAME" \
    --secret-string "$SECRET_JSON" >/dev/null
fi

SECRET_ARN="$(aws secretsmanager describe-secret --region "$AWS_REGION" --secret-id "$SECRET_NAME" --query ARN --output text)"
echo "OK: upsert finalizado. ARN=${SECRET_ARN}"

#############################################
# 4) (Opcional) Exportar outputs para próximos steps
#############################################
if [[ -n "${HARNESS_ENV_EXPORT:-}" && -f "${HARNESS_ENV_EXPORT:-/dev/null}" ]]; then
  {
    echo "SECRET_NAME=${SECRET_NAME}"
    echo "SECRET_ARN=${SECRET_ARN}"
  } >> "$HARNESS_ENV_EXPORT"
else
  echo "WARN: HARNESS_ENV_EXPORT não disponível; pulando export de output vars."
fi