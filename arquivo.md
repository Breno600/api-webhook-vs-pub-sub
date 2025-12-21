Executing command ...
ORG=Fiserv PROJECT=ExportadorSitef PIPELINE=TESTE
RAW PAIRS: null
ERRO: PAIRS veio vazio/null. Project variable não está chegando no step.
CommandExecution failed with exit code: (1)



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

                          echo "ORG=<+org.identifier> PROJECT=<+project.identifier> PIPELINE=<+pipeline.identifier>"
                          echo "RAW PAIRS: $PAIRS"

                          if [ -z "${PAIRS:-}" ] || [ "$PAIRS" = "null" ]; then
                            echo "ERRO: PAIRS veio vazio/null. Project variable não está chegando no step."
                            exit 1
                          fi

                          export PAIRS
                          python3 - <<'PY'
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

                          print("\n--- KEY=VALUE ---")
                          for k in sorted(out):
                            print(f"{k}={out[k]}")

                          print("\n--- JSON (pretty) ---")
                          print(json.dumps(out, ensure_ascii=False, indent=2))
                          PY
                    environmentVariables:
                      - name: PAIRS
                        type: String
                        value: <+project.variables.AWS_STRUCTURE_SECRET_MANAGER>
                    outputVariables: []
                  timeout: 10m
        tags: {}


VARIABLE:
AWS_STRUCTURE_SECRET_MANAGER
DB_USERNAME=<+secrets.getValue("DB_USERNAME")>;KAFKA_KEYSTORE_PASSWORD=<+secrets.getValue("KAFKA_KEYSTORE_PASSWORD")>
