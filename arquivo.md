set -euo pipefail
set +x

RAW="${PAIRS:-}"
if [ -z "${RAW}" ] || [ "${RAW}" = "null" ]; then
  echo "ERRO: PAIRS vazio/null"
  exit 1
fi

# Detecta modo
MODE=""
DELIM=""

trimmed="$(printf "%s" "$RAW" | sed -e 's/^[[:space:]]*//')"

if printf "%s" "$trimmed" | grep -qE '^\{'; then
  MODE="json"
elif printf "%s" "$RAW" | grep -Fq "***"; then
  MODE="pairs"; DELIM="***"
elif printf "%s" "$RAW" | grep -Fq ";"; then
  MODE="pairs"; DELIM=";"
else
  MODE="pairs"; DELIM=""   # 1 item só (KEY=VALUE)
fi

SECRET_JSON="$(
  RAW="$RAW" MODE="$MODE" DELIM="$DELIM" python3 - <<'PY'
import os, re, sys, json, hashlib

raw  = os.environ.get("RAW","")
mode = os.environ.get("MODE","pairs")
delim = os.environ.get("DELIM","")

key_re = re.compile(r"^[A-Za-z_][A-Za-z0-9_.-]*$")

def log(s):  # stderr pra não “contaminar” o secret
  print(s, file=sys.stderr)

def compact(obj):
  log("--- VALIDAÇÃO (sem mostrar valor) ---")
  for k in sorted(obj.keys()):
    v = obj[k]
    sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
    log(f"{k}: len={len(v)} sha256={sha}")
  return json.dumps(obj, ensure_ascii=False, separators=(",",":"))

if mode == "json":
  try:
    obj = json.loads(raw)
  except Exception as e:
    log(f"ERRO: JSON inválido: {e}")
    sys.exit(1)
  if not isinstance(obj, dict) or not obj:
    log("ERRO: JSON precisa ser um objeto e não pode ser vazio")
    sys.exit(1)
  for k,v in obj.items():
    if not isinstance(k,str) or not k or not key_re.match(k):
      log(f"ERRO: key inválida no JSON: '{k}'"); sys.exit(1)
    if not isinstance(v,str):
      log(f"ERRO: valor de '{k}' precisa ser string"); sys.exit(1)
  sys.stdout.write(compact(obj))
  sys.exit(0)

# pairs mode
parts = [raw.strip()] if not delim else [p.strip() for p in raw.split(delim)]
obj = {}

for part in parts:
  if not part:
    continue
  if "=" not in part:
    log(f"ERRO: item sem '=': {part}")
    sys.exit(1)
  k, v = part.split("=", 1)  # mantém '=' dentro do valor
  k = k.strip(); v = v.strip()

  if not k or not key_re.match(k):
    log(f"ERRO: key inválida: '{k}'")
    sys.exit(1)

  obj[k] = v

if not obj:
  log("ERRO: nenhum par key=value encontrado")
  sys.exit(1)

sys.stdout.write(compact(obj))
PY
)"

echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})"
export SECRET_JSON