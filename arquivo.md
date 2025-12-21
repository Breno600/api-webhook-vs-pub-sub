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
        tags: {}
        failureStrategies:
          - onFailure:
              errors:
                - AllErrors
              action:
                type: MarkAsFailure
        spec:
          execution:
            steps:
              - step:
                  type: ShellScript
                  name: Prepare_Secret_JSON
                  identifier: Prepare_Secret_JSON
                  timeout: 10m
                  spec:
                    shell: Bash
                    executionTarget: {}
                    environmentVariables:
                      - name: PAIRS
                        type: String
                        value: DB_USERNAME=<+secrets.getValue("DB_USERNAME")>;KAFKA_KEYSTORE_PASSWORD=<+secrets.getValue("KAFKA_KEYSTORE_PASSWORD")>
                    source:
                      type: Inline
                      spec:
                        script: |
                          set -euo pipefail
                          set +x

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

                          echo "SECRET_JSON=$SECRET_JSON" >> "$HARNESS_OUTPUT_VARIABLES"
                    outputVariables:
                      - name: SECRET_JSON
                        type: String
                        value: SECRET_JSON
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
                                    Default: 'application-secrets-dev'
                                SecretDescription:
                                    Type: String
                                    Description: 'Secret Description'
                                    Default: 'Secret for environment dev'
                                SecretValue:
                                    Type: String
                                    Description: 'Secret JSON'
                                    NoEcho: true
                                Project:
                                    Type: String
                                    Description: 'Project name for tagging'
                                    Default: '<+project.name>'
                                Environment:
                                    Type: String
                                    Description: 'Environment name for tagging'
                                    Default: 'dev'
                                ManagedBy:
                                    Type: String
                                    Description: 'Managed by for tagging'
                                    Default: 'cloudformation'
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
                      roleArn: ""
                      parameterOverrides:
                        - name: SecretName
                          value: application-secrets-<+pipeline.variables.ENVIRONMENT>
                          type: String
                        - name: SecretDescription
                          value: Secret for environment <+pipeline.variables.ENVIRONMENT>
                          type: String
                        - name: SecretValue
                          value: <+execution.steps.Prepare_Secret_JSON.output.outputVariables.SECRET_JSON>
                          type: String
                        - name: Project
                          value: <+project.name>
                          type: String
                        - name: Environment
                          value: <+pipeline.variables.ENVIRONMENT>
                          type: String
                        - name: ManagedBy
                          value: cloudformation
                          type: String
                  timeout: 10m
