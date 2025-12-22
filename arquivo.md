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

# ✅ VALIDAÇÃO vai para STDERR (log), não contamina o SECRET_JSON
print("--- VALIDAÇÃO (sem mostrar valor) ---", file=sys.stderr)
for k in sorted(obj.keys()):
  v = obj[k]
  sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}", file=sys.stderr)

# ✅ SOMENTE JSON no STDOUT (isso vira o SECRET_JSON)
sys.stdout.write(json.dumps(obj, ensure_ascii=False, separators=(",",":")))
PY
)"
export SECRET_JSON
echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})"