template:
  name: aws_secretsmanager_sync
  identifier: aws_secretsmanager_sync
  versionLabel: v1
  type: Stage
  spec:
    type: CI
    spec:
      cloneCodebase: false
      infrastructure:
        type: KubernetesDirect
        spec:
          connectorRef: <+input>          # Connector do cluster onde roda o CI
          namespace: harness-ci
          automountServiceAccountToken: true
          os: Linux
      execution:
        steps:
          - step:
              type: Run
              name: Sync secret to AWS Secrets Manager
              identifier: sync_secret
              spec:
                image: public.ecr.aws/aws-cli/aws-cli:2
                shell: Bash
                envVariables:
                  AWS_REGION: <+input>
                  AWS_SECRET_NAME: <+input>
                  AWS_KMS_KEY_ID: <+input>   # opcional (pode deixar vazio)
                  AWS_SECRET_DESCRIPTION: <+input> # opcional
                  # Esse aqui deve ser preenchido com um Secret do Harness (Secret Text)
                  SECRET_PAYLOAD: <+input>
                command: |-
                  set -euo pipefail
                  set +x  # evita logar comandos/variáveis

                  REGION="${AWS_REGION}"
                  NAME="${AWS_SECRET_NAME}"
                  KMS="${AWS_KMS_KEY_ID:-}"
                  DESC="${AWS_SECRET_DESCRIPTION:-}"
                  PAYLOAD="${SECRET_PAYLOAD}"

                  # verifica se existe
                  if aws secretsmanager describe-secret --region "$REGION" --secret-id "$NAME" >/dev/null 2>&1; then
                    aws secretsmanager put-secret-value \
                      --region "$REGION" \
                      --secret-id "$NAME" \
                      --secret-string "$PAYLOAD" >/dev/null
                    echo "OK: atualizado -> $NAME"
                  else
                    if [ -n "$KMS" ] && [ -n "$DESC" ]; then
                      aws secretsmanager create-secret \
                        --region "$REGION" \
                        --name "$NAME" \
                        --kms-key-id "$KMS" \
                        --description "$DESC" \
                        --secret-string "$PAYLOAD" >/dev/null
                    elif [ -n "$KMS" ]; then
                      aws secretsmanager create-secret \
                        --region "$REGION" \
                        --name "$NAME" \
                        --kms-key-id "$KMS" \
                        --secret-string "$PAYLOAD" >/dev/null
                    elif [ -n "$DESC" ]; then
                      aws secretsmanager create-secret \
                        --region "$REGION" \
                        --name "$NAME" \
                        --description "$DESC" \
                        --secret-string "$PAYLOAD" >/dev/null
                    else
                      aws secretsmanager create-secret \
                        --region "$REGION" \
                        --name "$NAME" \
                        --secret-string "$PAYLOAD" >/dev/null
                    fi
                    echo "OK: criado -> $NAME"
                  fi
