set -euo pipefail
set +x

# Entrada (string) no formato:
# KEY1=<+secrets.getValue("SECRET1")>***KEY2=<+secrets.getValue("SECRET2")>***...
RAW_PAIRS="${PAIRS:-}"
SEP='***'

if [ -z "$RAW_PAIRS" ] || [ "$RAW_PAIRS" = "null" ]; then
  echo "ERRO: PAIRS vazio/null"
  exit 1
fi

# Gera JSON compacto (stdout) e manda validação/metrics pra stderr
SECRET_JSON="$(RAW_PAIRS="$RAW_PAIRS" SEP="$SEP" python3 - <<'PY'
import os, re, sys, json, hashlib

raw = os.environ.get("RAW_PAIRS","")
sep = os.environ.get("SEP","***")

out = {}
parts = raw.split(sep)

for idx, chunk in enumerate(parts, 1):
  chunk = chunk.strip()
  if not chunk:
    continue
  if "=" not in chunk:
    print(f"ERRO: item {idx} sem '=': {chunk!r}", file=sys.stderr)
    sys.exit(1)

  k, v = chunk.split("=", 1)
  k = k.strip()
  v = v.strip()

  if not re.match(r"^[A-Za-z_][A-Za-z0-9_]*$", k):
    print(f"ERRO: key inválida: {k!r}", file=sys.stderr)
    sys.exit(1)

  out[k] = v

if not out:
  print("ERRO: nenhuma chave encontrada após parse (out vazio). Verifique separador e formato.", file=sys.stderr)
  sys.exit(1)

# Validação sem vazar segredo -> manda pra STDERR (não entra no SECRET_JSON)
print("--- VALIDAÇÃO (sem mostrar valor) ---", file=sys.stderr)
for k in sorted(out.keys()):
  v = out[k]
  sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}", file=sys.stderr)

# JSON compacto -> STDOUT (isso vira o SECRET_JSON)
sys.stdout.write(json.dumps(out, ensure_ascii=False, separators=(",",":")))
PY
)"

echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})"

# Exporta output pro próximo step (Harness)
if [ -n "${HARNESS_OUTPUT_VARIABLES:-}" ]; then
  echo "SECRET_JSON=$SECRET_JSON" >> "$HARNESS_OUTPUT_VARIABLES"
elif [ -n "${HARNESS_ENV_EXPORT:-}" ]; then
  echo "SECRET_JSON=$SECRET_JSON" >> "$HARNESS_ENV_EXPORT"
else
  echo "WARN: Harness não forneceu arquivo de output (HARNESS_OUTPUT_VARIABLES/HARNESS_ENV_EXPORT)."
  # não falha aqui pra você enxergar o log, mas sem isso não passa pro próximo step.
fi