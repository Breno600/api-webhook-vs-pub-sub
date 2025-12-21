pipeline:
  name: TESTE
  identifier: TESTE
  projectIdentifier: ExportadorSitef
  orgIdentifier: Fiserv
  tags: {}
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
                  name: ShellScript_1
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

                          echo "RAW PAIRS (pode aparecer mascarado pelo Harness)"
                          echo "$PAIRS"

                          export PAIRS
                          python3 - <<'PY'
                          import os, hashlib, re, sys
                          pairs = os.environ.get("PAIRS","").strip()
                          if not pairs or pairs == "null":
                            print("ERRO: PAIRS vazio"); sys.exit(1)

                          items={}
                          for chunk in pairs.split(";"):
                            chunk=chunk.strip()
                            if not chunk: 
                              continue
                            if "=" not in chunk:
                              print(f"ERRO item sem '=': {chunk}"); sys.exit(1)
                            k,v=chunk.split("=",1)
                            k=k.strip(); v=v.strip()
                            items[k]=v

                          print("\n--- VALIDAÇÃO (sem mostrar valor) ---")
                          for k in sorted(items):
                            v=items[k]
                            sha=hashlib.sha256(v.encode("utf-8")).hexdigest()
                            print(f"{k}: len={len(v)} sha256={sha}")
                          PY
                    environmentVariables:
                      - name: PAIRS
                        type: String
                        value: DB_USERNAME=<+secrets.getValue("DB_USERNAME")>;KAFKA_KEYSTORE_PASSWORD=<+secrets.getValue("KAFKA_KEYSTORE_PASSWORD")>
                    outputVariables:
                      - name: SECRET_JSON
                        type: String
                        value: ""
                  timeout: 10m
              - step:
                  type: CreateStack
                  name: CreateStack_Secrets
                  identifier: CreateStack_Secrets
                  spec:
                    provisionerIdentifier: ProvisionerSecretsFromHarness<+execution.steps.Validation.output.outputVariables.ENVIRONMENT>
                    configuration:
                      stackName: StackSecretsFromHarness<+execution.steps.Validation.output.outputVariables.ENVIRONMENT>
                      connectorRef: <+input>
                      region: <+input>.executionInput()
                      templateFile:
                        type: Inline
                        spec:
                          templateBody: |
                            AWSTemplateFormatVersion: '2010-09-09'
                            Parameters:
                                SecretName:
                                    Type: String
                                    Description: 'Secret Name'
                                    Default: 'application-secrets-<+execution.steps.Validation.output.outputVariables.ENVIRONMENT>'
                                SecretDescription:
                                    Type: String
                                    Description: 'Secret Description'
                                    Default: 'Secret for environment <+execution.steps.Validation.output.outputVariables.ENVIRONMENT>'
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
                                    Default: '<+execution.steps.Validation.output.outputVariables.ENVIRONMENT>'
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
                  timeout: 10m
        tags: {}
