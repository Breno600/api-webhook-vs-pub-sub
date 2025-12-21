set -euo pipefail

echo "ORG=<+org.identifier> PROJECT=<+project.identifier> PIPELINE=<+pipeline.identifier>"
echo "RAW PAIRS: $PAIRS"

if [ -z "${PAIRS:-}" ] || [ "$PAIRS" = "null" ]; then
  echo "ERRO: PAIRS veio vazio/null. Project variable não está chegando no step."
  exit 1
fi

export PAIRS
python3 - <<'PY'
import os, json, re, sys
pairs = os.environ.get("PAIRS","").strip()

out = {}
for chunk in pairs.split(";"):
  chunk = chunk.strip()
  if not chunk:
    continue
  if "=" not in chunk:
    print(f"ERRO item sem '=': {chunk}", file=sys.stderr); sys.exit(1)
  k, v = chunk.split("=", 1)
  k, v = k.strip(), v.strip()
  if not re.match(r"^[A-Za-z_][A-Za-z0-9_]*$", k):
    print(f"ERRO key inválida: {k}", file=sys.stderr); sys.exit(1)
  out[k] = v

print("\n--- KEY=VALUE ---")
for k in sorted(out):
  print(f"{k}={out[k]}")

print("\n--- JSON (pretty) ---")
print(json.dumps(out, ensure_ascii=False, indent=2))
PY
