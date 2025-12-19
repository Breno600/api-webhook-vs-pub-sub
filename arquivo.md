aws secretsmanager get-secret-value \
  --region us-east-1 \
  --secret-id 'omnidata/dev/transaction-rules-v2/env' \
  --query SecretString \
  --output text
