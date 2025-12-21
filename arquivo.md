pipeline:
  name: TESTE
  identifier: TESTE
  projectIdentifier: ExportadorSitef
  orgIdentifier: Fiserv
  tags: {}

  variables:
    - name: ENVIRONMENT
      type: String
      value: dev

  stages:
    - stage:
        name: DEV
        identifier: DEV
        description: ""
        type: Custom
        spec:
          execution:
            steps:
              - step:
                  type: ShellScript
                  name: Prepare_Secret_JSON
                  identifier: ShellScript_1
                  spec:
                    shell: Bash
                    executionTarget: {}
                    source:
                      type: Inline
                      spec:
                        script: |
                          set -euo pipefail
                          set +x

                          # PAIRS vem resolvido pelo Harness (mas log mascara secrets)
                          if [ -z "${PAIRS:-}" ] || [ "$PAIRS" = "null" ]; then
                            echo "ERRO: PAIRS vazio/null"
                            exit 1
                          fi

                          SECRET_JSON="$(PAIRS="$PAIRS" python3 - <<'PY'
                          import os, json, re, sys
                          pairs = os.environ.get("PAIRS","").strip()
                          out = {}
                          for chunk in pairs.split(";"):
                            chunk = chunk.strip()
                            if not chunk:
                              continue
                            if "=" not in chunk:
                              print(f"ERRO item sem '=': {chunk}", file=sys.stderr); sys.exit(1)
                            k, v = chunk.split("=", 1)
                            k, v = k.strip(), v.strip()
                            if not re.match(r"^[A-Za-z_][A-Za-z0-9_]*$", k):
                              print(f"ERRO key inválida: {k}", file=sys.stderr); sys.exit(1)
                            out[k] = v
                          sys.stdout.write(json.dumps(out, ensure_ascii=False, separators=(",",":")))
                          PY
                          )"

                          # NÃO imprimir o JSON (evita vazamento)
                          # echo "$SECRET_JSON"

                          echo "SECRET_JSON=$SECRET_JSON" >> "$HARNESS_OUTPUT_VARIABLES"

                    environmentVariables:
                      - name: PAIRS
                        type: String
                        value: DB_USERNAME=<+secrets.getValue("DB_USERNAME")>;KAFKA_KEYSTORE_PASSWORD=<+secrets.getValue("KAFKA_KEYSTORE_PASSWORD")>

                    outputVariables:
                      - name: SECRET_JSON
                        type: String
                  timeout: 10m

              - step:
                  type: CreateStack
                  name: CreateStack_Secrets
                  identifier: CreateStack_Secrets
                  spec:
                    provisionerIdentifier: ProvisionerSecretsFromHarness_<+pipeline.variables.ENVIRONMENT>
                    configuration:
                      stackName: StackSecretsFromHarness_<+pipeline.variables.ENVIRONMENT>
                      connectorRef: <+input>
                      region: <+input>
                      templateFile:
                        type: Inline
                        spec:
                          templateBody: |
                            AWSTemplateFormatVersion: '2010-09-09'
                            Parameters:
                              SecretName:
                                Type: String
                                Description: 'Secret Name'
                              SecretDescription:
                                Type: String
                                Description: 'Secret Description'
                              SecretValue:
                                Type: String
                                Description: 'Secret JSON'
                                NoEcho: true
                              Project:
                                Type: String
                                Description: 'Project name for tagging'
                              Environment:
                                Type: String
                                Description: 'Environment name for tagging'
                              ManagedBy:
                                Type: String
                                Description: 'Managed by for tagging'
                            Resources:
                              SecretHarness:
                                Type: AWS::SecretsManager::Secret
                                Properties:
                                  Name: !Ref SecretName
                                  Description: !Ref SecretDescription
                                  SecretString: !Ref SecretValue
                                  Tags:
                                    - Key: Project
                                      Value: !Ref Project
                                    - Key: Environment
                                      Value: !Ref Environment
                                    - Key: ManagedBy
                                      Value: !Ref ManagedBy

                      # AQUI entra o JSON gerado no step 1 + demais parâmetros
                      parameters:
                        - name: SecretName
                          value: application-secrets-<+pipeline.variables.ENVIRONMENT>
                        - name: SecretDescription
                          value: Secret for environment <+pipeline.variables.ENVIRONMENT>
                        - name: SecretValue
                          value: <+execution.steps.ShellScript_1.output.outputVariables.SECRET_JSON>
                        - name: Project
                          value: <+project.name>
                        - name: Environment
                          value: <+pipeline.variables.ENVIRONMENT>
                        - name: ManagedBy
                          value: cloudformation

                      roleArn: ""
                  timeout: 10m
        tags: {}
