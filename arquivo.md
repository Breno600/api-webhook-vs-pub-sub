set -euo pipefail
set +x

# Você passa PAIRS pelo Harness (agora ele contém JSON)
RAW_JSON="${PAIRS:-}"

if [ -z "$RAW_JSON" ] || [ "$RAW_JSON" = "null" ]; then
  echo "ERRO: input JSON vazio/null (PAIRS)"
  exit 1
fi

# valida JSON e compacta (1 linha)
SECRET_JSON="$(RAW_JSON="$RAW_JSON" python3 - <<'PY'
import os, json, sys, hashlib

raw = os.environ.get("RAW_JSON","")
try:
  obj = json.loads(raw)
except Exception as e:
  print(f"ERRO: JSON inválido: {e}", file=sys.stderr)
  sys.exit(1)

if not isinstance(obj, dict) or not obj:
  print("ERRO: JSON precisa ser um objeto e não pode ser vazio", file=sys.stderr)
  sys.exit(1)

# valida tipos e imprime só métricas (sem vazar)
for k,v in obj.items():
  if not isinstance(k,str) or not k:
    print("ERRO: chave inválida no JSON", file=sys.stderr); sys.exit(1)
  if not isinstance(v,str):
    print(f"ERRO: valor de '{k}' precisa ser string", file=sys.stderr); sys.exit(1)

print("--- VALIDAÇÃO (sem mostrar valor) ---")
for k in sorted(obj.keys()):
  v = obj[k]
  sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}")

# compacta para CloudFormation
sys.stdout.write(json.dumps(obj, ensure_ascii=False, separators=(",",":")))
PY
)"

echo "SECRET_JSON=$SECRET_JSON" >> "$HARNESS_OUTPUT_VARIABLES"