set -euo pipefail
set +x

RAW_JSON="${PAIRS:-}"
if [ -z "$RAW_JSON" ] || [ "$RAW_JSON" = "null" ]; then
  echo "ERRO: input JSON vazio/null"
  exit 1
fi

# Valida JSON e gera versão compacta (1 linha)
SECRET_JSON="$(python3 - <<'PY'
import os, json, sys
raw = os.environ["RAW_JSON"]

try:
  obj = json.loads(raw)
except Exception as e:
  print(f"ERRO: JSON inválido: {e}", file=sys.stderr)
  sys.exit(1)

if not isinstance(obj, dict) or not obj:
  print("ERRO: JSON precisa ser um objeto (dict) e não pode ser vazio", file=sys.stderr)
  sys.exit(1)

# valida chaves
for k,v in obj.items():
  if not isinstance(k,str) or not k:
    print("ERRO: chave inválida no JSON", file=sys.stderr); sys.exit(1)
  if not isinstance(v,str):
    print(f"ERRO: valor de '{k}' precisa ser string", file=sys.stderr); sys.exit(1)

# compacta pra passar no CloudFormation sem espaços/quebras
sys.stdout.write(json.dumps(obj, ensure_ascii=False, separators=(",",":")))
PY
)"

echo "SECRET_JSON=$SECRET_JSON" >> "$HARNESS_OUTPUT_VARIABLES"