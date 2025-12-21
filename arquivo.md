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




---

- step:
    type: CreateStack
    name: CreateStack_Secrets
    identifier: CreateStack_Secrets
    timeout: 10m
    spec:
      provisionerIdentifier: ProvisionerSecretsFromHarness_<+pipeline.variables.ENVIRONMENT>
      configuration:
        connectorRef: <+input>     # AWS Connector
        region: <+input>           # ex: sa-east-1
        stackName: StackSecretsFromHarness_<+pipeline.variables.ENVIRONMENT>

        templateFile:
          type: Remote
          spec:
            store:
              type: Github
              spec:
                connectorRef: <+input>      # Git connector
                repoName: <+input>          # ex: exportador-sitef-iac
                branch: <+input>            # ex: main
                filePath: <+input>          # ex: cloudformation/secret.yaml

        parameters:
          - identifier: SecretName
            value: application-secrets-<+pipeline.variables.ENVIRONMENT>
          - identifier: SecretDescription
            value: Secret for environment <+pipeline.variables.ENVIRONMENT>
          - identifier: SecretValue
            value: <+execution.steps.Prepare_Secret_JSON.output.outputVariables.SECRET_JSON>
          - identifier: Project
            value: <+project.name>
          - identifier: Environment
            value: <+pipeline.variables.ENVIRONMENT>
          - identifier: ManagedBy
            value: cloudformation

      roleArn: ""
