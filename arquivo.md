set -euo pipefail

PAIRS="<+project.variables.AWS_STRUCTURE_SECRET_MANAGER>"
echo "RAW PAIRS: $PAIRS"

export PAIRS
python3 - <<'PY'
import os, json, re, sys
pairs = os.environ.get("PAIRS","").strip()
if not pairs:
  print("ERRO: PAIRS vazio"); sys.exit(1)

out = {}
for chunk in pairs.split(";"):
  chunk = chunk.strip()
  if not chunk:
    continue
  if "=" not in chunk:
    print(f"ERRO item sem '=': {chunk}"); sys.exit(1)
  k,v = chunk.split("=",1)
  k=k.strip(); v=v.strip()
  if not re.match(r"^[A-Za-z_][A-Za-z0-9_]*$", k):
    print(f"ERRO key inválida: {k}"); sys.exit(1)
  out[k]=v

print("\n--- KEY=VALUE ---")
for k in sorted(out): print(f"{k}={out[k]}")
print("\n--- JSON ---")
print(json.dumps(out, ensure_ascii=False, indent=2))
PY
