template:
  name: aws_sm_replace_from_repo_with_harness_secrets
  identifier: aws_sm_replace_from_repo_with_harness_secrets
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
              name: Render repo JSON + Harness secrets -> AWS Secrets Manager
              identifier: render_and_replace
              timeout: 15m
              spec:
                shell: Bash
                onDelegate: true
                environmentVariables:
                  - name: GIT_URL
                    type: String
                    value: <+input>
                  - name: GIT_REF
                    type: String
                    value: <+input>
                  - name: GIT_TOKEN
                    type: Secret
                    value: <+input>

                  - name: JSON_PATH
                    type: String
                    value: <+input>   # ex: secrets/exportador-sitef.json

                  - name: AWS_REGION
                    type: String
                    value: <+input>
                  - name: AWS_SECRET_NAME
                    type: String
                    value: <+input>

                  - name: AWS_KMS_KEY_ID
                    type: String
                    value: <+input>   # opcional (só ao criar)
                  - name: AWS_SECRET_DESCRIPTION
                    type: String
                    value: <+input>   # opcional (só ao criar)

                  # secrets/values do Harness (adicione quantos quiser)
                  - name: Docker_Hub_MRC
                    type: Secret
                    value: <+input>
                  - name: DB_PASS
                    type: Secret
                    value: <+input>

                source:
                  type: Inline
                  spec:
                    script: |-
                      set -euo pipefail
                      set +x

                      workdir="$(mktemp -d)"
                      trap 'rm -rf "$workdir"' EXIT

                      command -v git >/dev/null 2>&1 || { echo "ERRO: git não encontrado no delegate"; exit 1; }
                      command -v python3 >/dev/null 2>&1 || { echo "ERRO: python3 não encontrado no delegate"; exit 1; }

                      # awscli se não existir
                      if ! command -v aws >/dev/null 2>&1; then
                        echo "awscli não encontrado. Instalando localmente..."
                        cd "$workdir"
                        curl -sSL "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
                        unzip -q awscliv2.zip
                        ./aws/install -i "$workdir/aws-cli" -b "$workdir/bin"
                        export PATH="$workdir/bin:$PATH"
                      fi

                      echo "Clonando repo..."
                      git -c http.extraHeader="Authorization: Bearer ${GIT_TOKEN}" \
                        clone --depth 1 --branch "${GIT_REF}" "${GIT_URL}" "$workdir/repo" >/dev/null 2>&1 || {
                          echo "ERRO: falha ao clonar. Verifique GIT_URL/GIT_REF/GIT_TOKEN"
                          exit 1
                        }

                      cd "$workdir/repo"
                      [ -f "$JSON_PATH" ] || { echo "ERRO: JSON_PATH não existe: $JSON_PATH"; exit 1; }

                      # Renderiza placeholders ${VAR} usando ENV do step (Docker_Hub_MRC, DB_PASS, etc.)
                      python3 - <<'PY'
                      import os, re, sys, json

                      path = os.environ["JSON_PATH"]
                      text = open(path, "r", encoding="utf-8").read()

                      # substitui ${VAR} pelo valor de ENV[VAR]
                      pattern = re.compile(r"\$\{([A-Za-z_][A-Za-z0-9_]*)\}")

                      def repl(m):
                        k = m.group(1)
                        v = os.environ.get(k)
                        if v is None:
                          # se não existir env, mantém como está (pra você perceber)
                          return m.group(0)
                        # escape básico para manter JSON válido quando o placeholder está dentro de string
                        return v.replace("\\", "\\\\").replace('"', '\\"')

                      rendered = pattern.sub(repl, text)

                      # valida se virou JSON válido
                      try:
                        obj = json.loads(rendered)
                      except Exception as e:
                        print("ERRO: JSON renderizado ficou inválido:", e)
                        sys.exit(1)

                      out_path = os.environ.get("OUT_PATH", "rendered.json")
                      open(out_path, "w", encoding="utf-8").write(json.dumps(obj, ensure_ascii=False, separators=(",", ":")))
                      print("OK: JSON renderizado em", out_path)
                      PY

                      REGION="${AWS_REGION}"
                      NAME="${AWS_SECRET_NAME}"
                      KMS="${AWS_KMS_KEY_ID:-}"
                      DESC="${AWS_SECRET_DESCRIPTION:-}"

                      # valida credenciais
                      aws sts get-caller-identity --region "$REGION" >/dev/null

                      FINAL_JSON="$(cat rendered.json)"

                      if aws secretsmanager describe-secret --region "$REGION" --secret-id "$NAME" >/dev/null 2>&1; then
                        aws secretsmanager put-secret-value \
                          --region "$REGION" \
                          --secret-id "$NAME" \
                          --secret-string "$FINAL_JSON" >/dev/null
                        echo "OK: atualizado -> $NAME"
                      else
                        args=(--region "$REGION" --name "$NAME" --secret-string "$FINAL_JSON")
                        [ -n "$KMS" ] && args+=(--kms-key-id "$KMS")
                        [ -n "$DESC" ] && args+=(--description "$DESC")
                        aws secretsmanager create-secret "${args[@]}" >/dev/null
                        echo "OK: criado -> $NAME"
                      fi
