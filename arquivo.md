set -euo pipefail
set +x

DELIM='***'

PAIRS_RAW="${PAIRS:-}"
if [ -z "${PAIRS_RAW}" ] || [ "${PAIRS_RAW}" = "null" ]; then
  echo "ERRO: PAIRS vazio/null"
  exit 1
fi

# Se o Harness NÃO resolver os secrets, vai chegar literal "<+secrets.getValue(...)>"
if echo "${PAIRS_RAW}" | grep -q '<+secrets\.getValue'; then
  echo "ERRO: detectei '<+secrets.getValue(...)>' literal dentro do PAIRS."
  echo "Isso indica que o Harness não resolveu os secrets dentro do input. Coloque os <+secrets.getValue()> direto em environmentVariables do step (não dentro de variável input)."
  exit 1
fi

SECRET_JSON="$(
  PAIRS_RAW="${PAIRS_RAW}" DELIM="${DELIM}" python3 - <<'PY'
import os, re, sys, json, hashlib

raw = os.environ.get("PAIRS_RAW","")
delim = os.environ.get("DELIM","***")

# Split por delimitador (***)
parts = [p.strip() for p in raw.split(delim)]

obj = {}
seen = set()

# Regex aceita letras/números/underscore/ponto/hífen
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

  # Valor pode ser vazio, mas normalmente não deveria (avisa)
  if v == "" or v.lower() == "null":
    print(f"AVISO: valor vazio/null para '{k}'", file=sys.stderr)

  obj[k] = v
  seen.add(k)

if not obj:
  print("ERRO: nenhuma chave/valor encontrado após split pelo delimitador", file=sys.stderr)
  sys.exit(1)

# Métricas SEM vazar valor -> manda tudo pro STDERR
print("--- VALIDAÇÃO (sem mostrar valor) ---", file=sys.stderr)
for k in sorted(obj.keys()):
  v = obj[k]
  sha = hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}", file=sys.stderr)

# STDOUT precisa ser SOMENTE o JSON compacto (pro CloudFormation / SecretsManager)
sys.stdout.write(json.dumps(obj, ensure_ascii=False, separators=(",",":")))
PY
)"

echo "OK: SECRET_JSON pronto (len=${#SECRET_JSON})"

# Exporta output para o próximo step
OUT_FILE="${HARNESS_OUTPUT_VARIABLES:-${HARNESS_ENV_EXPORT:-}}"
if [ -z "${OUT_FILE}" ]; then
  echo "ERRO: Harness não forneceu arquivo de output (HARNESS_OUTPUT_VARIABLES/HARNESS_ENV_EXPORT)."
  echo "Confirme no step que você configurou outputVariables e que o delegate/runner está suportando output vars."
  exit 1
fi

echo "SECRET_JSON=${SECRET_JSON}" >> "${OUT_FILE}"