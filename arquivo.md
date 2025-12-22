template:
  name: DEV
  identifier: DEV
  versionLabel: v1
  type: Stage
  projectIdentifier: ExportadorSitef
  orgIdentifier: Fiserv
  tags: {}
  spec:
    type: Custom
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
                    value: <+stage.variables.AWS_STRUCTURE_SECRET_MANAGER>
                source:
                  type: Inline
                  spec:
                    script: |-
                      set -euo pipefail
                      set +x

                      # Input JSON vem em PAIRS
                      RAW_JSON="${PAIRS:-}"
                      if [ -z "$RAW_JSON" ] || [ "$RAW_JSON" = "null" ]; then
                        echo "ERRO: input JSON vazio/null (PAIRS)"
                        exit 1
                      fi

                      # Valida JSON e compacta para 1 linha (para CloudFormation)
                      SECRET_JSON="$(RAW_JSON="$RAW_JSON" python3 - <<'PY'
                      import os, json, sys, hashlib

                      raw = os.environ.get("RAW_JSON","")
                      try:
                        obj = json.loads(raw)
                      except Exception as e:
                        print(f"ERRO: JSON inválido: {e}", file=sys.stderr)
                        sys.exit(1)

                      if not isinstance(obj, dict) or not obj:
                        print("ERRO: JSON precisa ser um objeto (dict) e não pode ser vazio", file=sys.stderr)
                        sys.exit(1)

                      for k, v in obj.items():
                        if not isinstance(k, str) or not k:
                          print("ERRO: chave inválida no JSON", file=sys.stderr)
                          sys.exit(1)
                        if not isinstance(v, str):
                          print(f"ERRO: valor de '{k}' precisa ser string", file=sys.stderr)
                          sys.exit(1)

                      print("--- VALIDAÇÃO (sem mostrar valor) ---")
                      for k in sorted(obj.keys()):
                        v = obj[k]
                        sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
                        print(f"{k}: len={len(v)} sha256={sha}")

                      sys.stdout.write(json.dumps(obj, ensure_ascii=False, separators=(",",":")))
                      PY
                      )"

                      # IMPORTANTISSIMO: no seu executor NÃO existe HARNESS_OUTPUT_VARIABLES/HARNESS_ENV_EXPORT.
                      # Então a forma certa é exportar a env var, e o Harness captura via outputVariables.
                      export SECRET_JSON
                      echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})"
                outputVariables:
                  - name: SECRET_JSON
                    type: String
                    value: SECRET_JSON

          - step:
              type: CreateStack
              name: CreateStack_Secrets
              identifier: CreateStack_Secrets
              timeout: 10m
              failureStrategies:
                - onFailure:
                    errors:
                      - AllErrors
                    action:
                      type: Ignore
              spec:
                provisionerIdentifier: ProvisionerSecretsFromHarness_<+pipeline.variables.ENVIRONMENT>
                configuration:
                  stackName: StackSecretsFromHarness_<+pipeline.variables.ENVIRONMENT>
                  connectorRef: <+input>
                  region: <+input>
                  templateFile:
                    type: Inline
                    spec:
                      templateBody: |-
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

    variables:
      - name: AWS_STRUCTURE_SECRET_MANAGER
        type: String
        description: |-
          Ex (JSON):
          {"VAR1":"<+secrets.getValue(\"VAR1\")>","VAR2":"<+secrets.getValue(\"VAR2\")>"}
        required: true
        value: <+input>