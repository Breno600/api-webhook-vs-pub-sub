set -euo pipefail
set +x

: "${AWS_ACCESS_KEY_ID:?missing AWS_ACCESS_KEY_ID}"
: "${AWS_SECRET_ACCESS_KEY:?missing AWS_SECRET_ACCESS_KEY}"
: "${ROLE_ARN:?missing ROLE_ARN}"
: "${EXTERNAL_ID:?missing EXTERNAL_ID}"
: "${AWS_REGION:?missing AWS_REGION}"
: "${SECRET_NAME:?missing SECRET_NAME}"
: "${SECRET_JSON:?missing SECRET_JSON}"

# Assume role com ExternalId
CREDS_JSON="$(
  aws sts assume-role \
    --role-arn "$ROLE_ARN" \
    --role-session-name "harness-secret-write" \
    --external-id "$EXTERNAL_ID" \
    --region "$AWS_REGION"
)"

export AWS_ACCESS_KEY_ID="$(python3 -c 'import json,sys; print(json.load(sys.stdin)["Credentials"]["AccessKeyId"])' <<<"$CREDS_JSON")"
export AWS_SECRET_ACCESS_KEY="$(python3 -c 'import json,sys; print(json.load(sys.stdin)["Credentials"]["SecretAccessKey"])' <<<"$CREDS_JSON")"
export AWS_SESSION_TOKEN="$(python3 -c 'import json,sys; print(json.load(sys.stdin)["Credentials"]["SessionToken"])' <<<"$CREDS_JSON")"

# Cria ou atualiza o Secret (sem limite 4096 de CFN Parameter)
if aws secretsmanager describe-secret --secret-id "$SECRET_NAME" --region "$AWS_REGION" >/dev/null 2>&1; then
  aws secretsmanager put-secret-value \
    --secret-id "$SECRET_NAME" \
    --secret-string "$SECRET_JSON" \
    --region "$AWS_REGION" >/dev/null
else
  aws secretsmanager create-secret \
    --name "$SECRET_NAME" \
    --description "Secret for environment ${ENVIRONMENT:-unknown}" \
    --secret-string "$SECRET_JSON" \
    --tags \
      Key=Project,Value="${PROJECT_NAME:-ExportadorSitef}" \
      Key=Environment,Value="${ENVIRONMENT:-unknown}" \
      Key=ManagedBy,Value="harness" \
    --region "$AWS_REGION" >/dev/null
fi

echo "OK: Secret gravado em '$SECRET_NAME' na region '$AWS_REGION'"