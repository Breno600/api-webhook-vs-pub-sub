# ler a lista de arquivos do changed-files.json
for file in $(jq -r '.[]' changed-files.json); do
  echo "📂 Processando arquivo: $file"

  # pega valores fixos
  PROJECT=$(jq -r '.harnessProject' "$file")
  WEBHOOK=$(jq -r '.harnessPipelineWebhook' "$file")

  echo "  Harness Project: $PROJECT"
  echo "  Webhook: $WEBHOOK"

  # percorre array "variables"
  jq -c '.variables[]' "$file" | while read -r item; do
    REGION=$(echo "$item" | jq -r '.awsRegion')
    echo "  >>> Região: $REGION"

    echo "    --- Secrets ---"
    echo "$item" | jq -c '.secretManager[]?' | while read -r secret; do
      NAME=$(echo "$secret" | jq -r '.name')
      VALUE=$(echo "$secret" | jq -r '.valueIdentifyFromHarness')
      echo "      Secret $NAME -> $VALUE"
    done

    echo "    --- Parameters ---"
    echo "$item" | jq -c '.parameterManager[]?' | while read -r param; do
      NAME=$(echo "$param" | jq -r '.name')
      VALUE=$(echo "$param" | jq -r '.valueIdentifyFromHarness')
      echo "      Param $NAME -> $VALUE"
    done
  done

  echo "--------------------------------"
done
