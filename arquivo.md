set -euo pipefail
set +x

SECRET_NAME="application-secrets-${ENVIRONMENT}"
REGION="${AWS_REGION}"

# (Opcional) assume role se você precisar
if [[ -n "${ROLE_ARN:-}" ]]; then
  CREDS="$(aws sts assume-role \
    --role-arn "$ROLE_ARN" \
    --role-session-name harness-secret \
    ${EXTERNAL_ID:+--external-id "$EXTERNAL_ID"} \
  )"
  export AWS_ACCESS_KEY_ID="$(python3 -c 'import json,sys; print(json.load(sys.stdin)["Credentials"]["AccessKeyId"])' <<<"$CREDS")"
  export AWS_SECRET_ACCESS_KEY="$(python3 -c 'import json,sys; print(json.load(sys.stdin)["Credentials"]["SecretAccessKey"])' <<<"$CREDS")"
  export AWS_SESSION_TOKEN="$(python3 -c 'import json,sys; print(json.load(sys.stdin)["Credentials"]["SessionToken"])' <<<"$CREDS")"
fi

# cria ou atualiza o valor (sem limite 4096 do CFN Parameter)
if aws secretsmanager describe-secret --secret-id "$SECRET_NAME" --region "$REGION" >/dev/null 2>&1; then
  aws secretsmanager put-secret-value \
    --secret-id "$SECRET_NAME" \
    --secret-string "$SECRET_JSON" \
    --region "$REGION" >/dev/null
else
  aws secretsmanager create-secret \
    --name "$SECRET_NAME" \
    --description "Secret for environment ${ENVIRONMENT}" \
    --secret-string "$SECRET_JSON" \
    --tags Key=Project,Value="${PROJECT_NAME:-ExportadorSitef}" Key=Environment,Value="${ENVIRONMENT}" Key=ManagedBy,Value="harness" \
    --region "$REGION" >/dev/null
fi

echo "OK: Secret atualizado em $SECRET_NAME"