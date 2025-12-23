#!/usr/bin/env bash
set -euo pipefail
set +x

: "${AWS_REGION:?AWS_REGION obrigatório}"
: "${ASSUME_ROLE_ARN:?ASSUME_ROLE_ARN obrigatório}"
: "${ASSUME_ROLE_EXTERNAL_ID:?ASSUME_ROLE_EXTERNAL_ID obrigatório}"
: "${SECRET_NAME:?SECRET_NAME obrigatório}"

# Credenciais "base" (as do connector)
: "${BASE_AWS_ACCESS_KEY_ID:?BASE_AWS_ACCESS_KEY_ID obrigatório}"
: "${BASE_AWS_SECRET_ACCESS_KEY:?BASE_AWS_SECRET_ACCESS_KEY obrigatório}"
BASE_AWS_SESSION_TOKEN="${BASE_AWS_SESSION_TOKEN:-}"

export AWS_ACCESS_KEY_ID="$BASE_AWS_ACCESS_KEY_ID"
export AWS_SECRET_ACCESS_KEY="$BASE_AWS_SECRET_ACCESS_KEY"
if [[ -n "$BASE_AWS_SESSION_TOKEN" ]]; then
  export AWS_SESSION_TOKEN="$BASE_AWS_SESSION_TOKEN"
fi

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

# daqui pra frente: você já está na role assumida ✅
aws sts get-caller-identity --region "$AWS_REGION" >/dev/null

echo "OK: assumiu a role. Prosseguindo com upsert do secret..."
# ... (continua com o bloco de montar JSON + create/update do SecretsManager)