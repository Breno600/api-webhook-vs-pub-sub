set -euo pipefail

echo "=== DRY-RUN: lendo AWS_STRUCTURE_SECRET_MANAGER ==="

PAIRS="${AWS_STRUCTURE_SECRET_MANAGER:-}"

if [ -z "$PAIRS" ]; then
  echo "ERRO: AWS_STRUCTURE_SECRET_MANAGER está vazia"
  exit 1
fi

python3 - <<'PY'
import os, json, re, sys

pairs = os.environ.get("PAIRS","").strip()
out = {}

# divide por ; e monta dict
for chunk in pairs.split(";"):
  chunk = chunk.strip()
  if not chunk:
    continue
  if "=" not in chunk:
    print(f"ERRO: item inválido (sem '='): {chunk}", file=sys.stderr)
    sys.exit(1)

  k, v = chunk.split("=", 1)
  k, v = k.strip(), v.strip()

  if not re.match(r"^[A-Za-z_][A-Za-z0-9_]*$", k):
    print(f"ERRO: key inválida: {k}", file=sys.stderr)
    sys.exit(1)

  out[k] = v

print("\n--- PAIRS RESOLVIDOS (chave=valor) ---")
for k in sorted(out.keys()):
  print(f"{k}={out[k]}")

print("\n--- JSON FINAL (pretty) ---")
print(json.dumps(out, ensure_ascii=False, indent=2))

print("\n--- JSON FINAL (compact) ---")
print(json.dumps(out, ensure_ascii=False, separators=(",",":")))
PY
PY
