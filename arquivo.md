#!/usr/bin/env bash
set -euo pipefail
set +x

# ===== Inputs obrigatórios =====
: "${AWS_REGION:?AWS_REGION obrigatório (ex: us-east-1)}"
: "${ASSUME_ROLE_ARN:?ASSUME_ROLE_ARN obrigatório}"
: "${ASSUME_ROLE_EXTERNAL_ID:?ASSUME_ROLE_EXTERNAL_ID obrigatório}"
: "${SECRET_NAME:?SECRET_NAME obrigatório (ex: application-secrets-dev)}"

# ===== Inputs opcionais =====
SECRET_DESCRIPTION="${SECRET_DESCRIPTION:-Secret managed by Harness}"
PROJECT_TAG="${PROJECT_TAG:-ExportadorSitef}"
ENVIRONMENT_TAG="${ENVIRONMENT_TAG:-dev}"
MANAGED_BY_TAG="${MANAGED_BY_TAG:-harness}"
DELIM="${DELIM:-***}"

# Você pode passar OU SECRET_JSON OU PAIRS
SECRET_JSON="${SECRET_JSON:-}"
PAIRS="${PAIRS:-}"

# ===== 1) Assume role =====
SESSION_NAME="harness-secrets-$(date +%s)"

assume_out="$(aws sts assume-role \
  --region "$AWS_REGION" \
  --role-arn "$ASSUME_ROLE_ARN" \
  --role-session-name "$SESSION_NAME" \
  --external-id "$ASSUME_ROLE_EXTERNAL_ID" \
  --duration-seconds 3600)"

export AWS_ACCESS_KEY_ID="$(printf '%s' "$assume_out" | python3 -c "import sys,json;print(json.load(sys.stdin)['Credentials']['AccessKeyId'])")"
export AWS_SECRET_ACCESS_KEY="$(printf '%s' "$assume_out" | python3 -c "import sys,json;print(json.load(sys.stdin)['Credentials']['SecretAccessKey'])")"
export AWS_SESSION_TOKEN="$(printf '%s' "$assume_out" | python3 -c "import sys,json;print(json.load(sys.stdin)['Credentials']['SessionToken'])")"

# ===== 2) Monta JSON (se não veio pronto) =====
if [[ -z "$SECRET_JSON" ]]; then
  if [[ -z "$PAIRS" ]]; then
    echo "ERRO: informe SECRET_JSON ou PAIRS"
    exit 1
  fi

  # Monta JSON com escape correto, valida keys e não mostra valores
  SECRET_JSON="$(PAIRS_RAW="$PAIRS" DELIM="$DELIM" python3 - <<'PY'
import os, re, sys, json, hashlib

raw = os.environ.get("PAIRS_RAW","")
delim = os.environ.get("DELIM","***")

parts = [p.strip() for p in raw.split(delim)]
obj = {}

key_re = re.compile(r"^[A-Za-z_][A-Za-z0-9_.-]*$")  # permite hífen/ponto/underscore

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

    # valor pode ser qualquer string (inclusive com espaços, : ; etc)
    obj[k] = v

if not obj:
    print("ERRO: nenhum par KEY=VALUE detectado", file=sys.stderr)
    sys.exit(1)

print("--- VALIDAÇÃO (sem mostrar valor) ---")
for k in sorted(obj.keys()):
    sha = hashlib.sha256(obj[k].encode("utf-8")).hexdigest()
    print(f"{k}: len={len(obj[k])} sha256={sha}")

# JSON compactado (1 linha)
sys.stdout.write(json.dumps(obj, ensure_ascii=False, separators=(",",":")))
PY
)"
fi

# Limite do Secrets Manager: SecretString até 65536 bytes (~64KB)
if (( ${#SECRET_JSON} > 65000 )); then
  echo "ERRO: SECRET_JSON muito grande (${#SECRET_JSON} chars). Secrets Manager suporta ~64KB."
  exit 1
fi

echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})"

# ===== 3) Upsert no Secrets Manager =====
if aws secretsmanager describe-secret --region "$AWS_REGION" --secret-id "$SECRET_NAME" >/dev/null 2>&1; then
  echo "Secret existe -> atualizando valor"
  aws secretsmanager put-secret-value \
    --region "$AWS_REGION" \
    --secret-id "$SECRET_NAME" \
    --secret-string "$SECRET_JSON" >/dev/null

  echo "Atualizando descrição (opcional)"
  aws secretsmanager update-secret \
    --region "$AWS_REGION" \
    --secret-id "$SECRET_NAME" \
    --description "$SECRET_DESCRIPTION" >/dev/null
else
  echo "Secret não existe -> criando"
  aws secretsmanager create-secret \
    --region "$AWS_REGION" \
    --name "$SECRET_NAME" \
    --description "$SECRET_DESCRIPTION" \
    --secret-string "$SECRET_JSON" \
    --tags \
      Key=Project,Value="$PROJECT_TAG" \
      Key=Environment,Value="$ENVIRONMENT_TAG" \
      Key=ManagedBy,Value="$MANAGED_BY_TAG" >/dev/null
fi

# ===== 4) Upsert tags (sempre) =====
SECRET_ARN="$(aws secretsmanager describe-secret --region "$AWS_REGION" --secret-id "$SECRET_NAME" \
  --query ARN --output text)"

aws secretsmanager tag-resource \
  --region "$AWS_REGION" \
  --secret-id "$SECRET_ARN" \
  --tags \
    Key=Project,Value="$PROJECT_TAG" \
    Key=Environment,Value="$ENVIRONMENT_TAG" \
    Key=ManagedBy,Value="$MANAGED_BY_TAG" >/dev/null

echo "SUCESSO: secret upserted -> $SECRET_NAME"