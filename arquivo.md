pipeline:
  name: config_application_secret
  identifier: config_application_secret
  orgIdentifier: Fiserv
  projectIdentifier: sitef

  stages:
    - stage:
        name: Sync to AWS
        identifier: Sync_to_AWS
        type: Custom
        spec:
          execution:
            steps:
              - step:
                  type: ShellScript
                  name: Sync secret to AWS Secrets Manager
                  identifier: sync_secret
                  timeout: 10m
                  spec:
                    shell: Bash
                    onDelegate: true

                    environmentVariables:
                      - name: AWS_REGION
                        type: String
                        value: <+input>

                      - name: AWS_SECRET_NAME
                        type: String
                        value: <+input>

                      - name: AWS_KMS_KEY_ID
                        type: String
                        value: <+input>

                      - name: AWS_SECRET_DESCRIPTION
                        type: String
                        value: <+input>

                      - name: SECRET_PAYLOAD
                        type: Secret
                        value: <+input>

                    source:
                      type: Inline
                      spec:
                        script: |-
                          set -euo pipefail
                          set +x

                          REGION="${AWS_REGION}"
                          NAME="${AWS_SECRET_NAME}"
                          KMS="${AWS_KMS_KEY_ID:-}"
                          DESC="${AWS_SECRET_DESCRIPTION:-}"
                          PAYLOAD="${SECRET_PAYLOAD}"

                          workdir="$(mktemp -d)"
                          trap 'rm -rf "$workdir"' EXIT

                          if ! command -v aws >/dev/null 2>&1; then
                            echo "awscli não encontrado. Instalando localmente..."
                            cd "$workdir"
                            curl -sSL "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
                            unzip -q awscliv2.zip
                            ./aws/install -i "$workdir/aws-cli" -b "$workdir/bin"
                            export PATH="$workdir/bin:$PATH"
                          fi

                          aws sts get-caller-identity --region "$REGION" >/dev/null

                          if aws secretsmanager describe-secret --region "$REGION" --secret-id "$NAME" >/dev/null 2>&1; then
                            aws secretsmanager put-secret-value \
                              --region "$REGION" \
                              --secret-id "$NAME" \
                              --secret-string "$PAYLOAD" >/dev/null
                            echo "OK: atualizado -> $NAME"
                          else
                            args=(--region "$REGION" --name "$NAME" --secret-string "$PAYLOAD")
                            [ -n "$KMS" ] && args+=(--kms-key-id "$KMS")
                            [ -n "$DESC" ] && args+=(--description "$DESC")

                            aws secretsmanager create-secret "${args[@]}" >/dev/null
                            echo "OK: criado -> $NAME"
                          fi
