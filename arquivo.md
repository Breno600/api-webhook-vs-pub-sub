set -euo pipefail
set +x

RAW_JSON="${PAIRS:-}"

# Debug seguro (não vaza segredo): tamanho + começo do payload
python3 - <<'PY'
import os, hashlib
raw=os.environ.get("RAW_JSON","")
print(f"[DEBUG] RAW_JSON len={len(raw)} sha256={hashlib.sha256(raw.encode('utf-8')).hexdigest()} head={raw[:30]!r}")
PY

if [ -z "${RAW_JSON//[[:space:]]/}" ] || [ "$RAW_JSON" = "null" ]; then
  echo "ERRO: input JSON vazio/null (PAIRS)"
  exit 1
fi

# roda python e se falhar, derruba o step
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
    print("ERRO: chave inválida no JSON", file=sys.stderr); sys.exit(1)
  if not isinstance(v, str):
    print(f"ERRO: valor de '{k}' precisa ser string", file=sys.stderr); sys.exit(1)

# validação no stderr (log), não contamina o JSON
print("--- VALIDAÇÃO (sem mostrar valor) ---", file=sys.stderr)
for k in sorted(obj.keys()):
  v = obj[k]
  sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}", file=sys.stderr)

# stdout = somente o JSON compactado
sys.stdout.write(json.dumps(obj, ensure_ascii=False, separators=(",",":")))
PY
)" || { echo "ERRO: falha ao gerar SECRET_JSON"; exit 1; }

# garante que não ficou vazio
if [ -z "${SECRET_JSON//[[:space:]]/}" ]; then
  echo "ERRO: SECRET_JSON ficou vazio"
  exit 1
fi

export SECRET_JSON
echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})"