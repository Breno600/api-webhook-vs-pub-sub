set -euo pipefail
set +x

# INPUT vindo do Harness (uma linha)
# Formato esperado:
#   KEY=VALOR***KEY2=VALOR2***KEY3=VALOR3
RAW="${PAIRS:-}"

if [ -z "$RAW" ] || [ "$RAW" = "null" ]; then
  echo "ERRO: input vazio/null (PAIRS)" >&2
  exit 1
fi

# Onde gravar output variables (depende do runner/versão do Harness)
OUT_FILE="${HARNESS_OUTPUT_VARIABLES:-${HARNESS_ENV_EXPORT:-}}"
if [ -z "${OUT_FILE:-}" ]; then
  echo "ERRO: Harness não forneceu arquivo de output (HARNESS_OUTPUT_VARIABLES/HARNESS_ENV_EXPORT)." >&2
  env | grep -E 'HARNESS_(OUTPUT|ENV)' || true
  exit 1
fi

# Gera JSON compacto a partir do RAW (separador ***), sem vazar valores nos logs
SECRET_JSON="$(RAW="$RAW" python3 - <<'PY'
import os, sys, json, re, hashlib

raw = os.environ.get("RAW", "").strip()

# separador entre pares: ***
chunks = [c.strip() for c in re.split(r"\s*\*\*\*\s*", raw) if c.strip()]

out = {}
key_re = re.compile(r"^[A-Za-z_][A-Za-z0-9_]*$")

for chunk in chunks:
  if "=" not in chunk:
    print(f"ERRO: item sem '=': {chunk}", file=sys.stderr)
    sys.exit(1)

  # somente o PRIMEIRO '=' separa key do valor (valor pode conter '=')
  k, v = chunk.split("=", 1)
  k, v = k.strip(), v.strip()

  if not key_re.match(k):
    print(f"ERRO: key inválida: {k}", file=sys.stderr)
    sys.exit(1)

  # mantém exatamente o valor (não tenta interpretar, não faz replace)
  out[k] = v

if not out:
  print("ERRO: nenhuma variável encontrada após split por '***'", file=sys.stderr)
  sys.exit(1)

# Validação sem vazar valores
print("--- VALIDAÇÃO (sem mostrar valor) ---", file=sys.stderr)
for k in sorted(out.keys()):
  v = out[k]
  sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}", file=sys.stderr)

# stdout precisa ser somente o JSON (pra capturar no bash)
sys.stdout.write(json.dumps(out, ensure_ascii=False, separators=(",",":")))
PY
)"

# sanity
if [ -z "${SECRET_JSON:-}" ]; then
  echo "ERRO: SECRET_JSON ficou vazio" >&2
  exit 1
fi

echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})" >&2

# Exporta pro próximo step (CreateStack)
printf 'SECRET_JSON=%s\n' "$SECRET_JSON" >> "$OUT_FILE"