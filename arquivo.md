set -euo pipefail
set +x

DELIM='***'

PAIRS_RAW="${PAIRS:-}"
if [ -z "${PAIRS_RAW}" ] || [ "${PAIRS_RAW}" = "null" ]; then
  echo "ERRO: PAIRS vazio/null"
  exit 1
fi

# Detecta se chegou placeholder de secret NÃO resolvido.
# IMPORTANTE: não podemos escrever "<+" literal no script, senão o Harness tenta avaliar.
TOKEN="<""+secrets.getValue"
if echo "${PAIRS_RAW}" | grep -Fq "${TOKEN}"; then
  echo "ERRO: detectei placeholder de secret não resolvido dentro do PAIRS."
  echo "Exemplo: <""+secrets.getValue(...)>"
  echo "Isso indica que o Harness NÃO resolveu os secrets dentro desse input."
  exit 1
fi

SECRET_JSON="$(
  PAIRS_RAW="${PAIRS_RAW}" DELIM="${DELIM}" python3 - <<'PY'
import os, re, sys, json, hashlib

raw = os.environ.get("PAIRS_RAW","")
delim = os.environ.get("DELIM","***")

parts = [p.strip() for p in raw.split(delim)]

obj = {}
seen = set()

# Aceita letras/números/underscore/ponto/hífen (inclui kafka-truststore)
key_re = re.compile(r"^[A-Za-z_][A-Za-z0-9_.-]*$")

for part in parts:
  if not part:
    continue
  if "=" not in part:
    print(f"ERRO: item sem '=': {part}", file=sys.stderr)
    sys.exit(1)

  k, v = part.split("=", 1)
  k = k.strip()
  v = v.strip()

  if not k or not key_re.match(k):
    print(f"ERRO: key inválida: '{k}' (permitido: [A-Za-z_][A-Za-z0-9_.-]*)", file=sys.stderr)
    sys.exit(1)

  if k in seen:
    print(f"ERRO: key duplicada: '{k}'", file=sys.stderr)
    sys.exit(1)

  if v == "" or v.lower() == "null":
    print(f"AVISO: valor vazio/null para '{k}'", file=sys.stderr)

  obj[k] = v
  seen.add(k)

if not obj:
  print("ERRO: nenhuma chave/valor encontrado após split pelo delimitador", file=sys.stderr)
  sys.exit(1)

# Logs de validação SEM vazar valores -> stderr
print("--- VALIDAÇÃO (sem mostrar valor) ---", file=sys.stderr)
for k in sorted(obj.keys()):
  v = obj[k]
  sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}", file=sys.stderr)

# Stdout somente JSON compacto
sys.stdout.write(json.dumps(obj, ensure_ascii=False, separators=(",",":")))
PY
)"

echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})"

# Exporta output para o próximo step
OUT_FILE="${HARNESS_OUTPUT_VARIABLES:-${HARNESS_ENV_EXPORT:-}}"
if [ -z "${OUT_FILE}" ]; then
  echo "ERRO: Harness não forneceu arquivo de output (HARNESS_OUTPUT_VARIABLES/HARNESS_ENV_EXPORT)."
  exit 1
fi

echo "SECRET_JSON=${SECRET_JSON}" >> "${OUT_FILE}"