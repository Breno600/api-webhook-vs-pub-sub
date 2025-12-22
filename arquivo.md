set -euo pipefail
set +x

RAW="${PAIRS:-}"
if [ -z "${RAW//[[:space:]]/}" ] || [ "$RAW" = "null" ]; then
  echo "ERRO: input vazio/null (PAIRS)" >&2
  exit 1
fi

# Converte a sequência textual "\x1F" em um separador real (ASCII 31)
US="$(printf '\037')"
RAW_REAL="${RAW//\\x1F/$US}"

SECRET_JSON="$(python3 - <<'PY'
import os, sys, json, re, hashlib

raw = os.environ.get("RAW_REAL","")
US = chr(31)

key_re = re.compile(r"^[A-Za-z_][A-Za-z0-9_]*$")

out = {}
for chunk in raw.split(US):
  chunk = chunk.strip()
  if not chunk:
    continue
  if "=" not in chunk:
    print(f"ERRO: item sem '=': {chunk}", file=sys.stderr); sys.exit(1)
  k, v = chunk.split("=", 1)
  k, v = k.strip(), v.strip()
  if not key_re.match(k):
    print(f"ERRO: key inválida: {k}", file=sys.stderr); sys.exit(1)
  out[k] = v

if not out:
  print("ERRO: nenhuma variável encontrada", file=sys.stderr); sys.exit(1)

print("--- VALIDAÇÃO (sem mostrar valor) ---", file=sys.stderr)
for k in sorted(out.keys()):
  v = out[k]
  sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}", file=sys.stderr)

sys.stdout.write(json.dumps(out, ensure_ascii=False, separators=(",",":")))
PY
)" 2>/dev/null

# OBS: o python acima manda validação em stderr; não contamina o SECRET_JSON

export SECRET_JSON
echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})" >&2