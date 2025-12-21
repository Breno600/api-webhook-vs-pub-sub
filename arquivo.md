set -euo pipefail
set +x

echo "RAW PAIRS (pode aparecer mascarado pelo Harness)"
echo "$PAIRS"

export PAIRS
python3 - <<'PY'
import os, hashlib, re, sys
pairs = os.environ.get("PAIRS","").strip()
if not pairs or pairs == "null":
  print("ERRO: PAIRS vazio"); sys.exit(1)

items={}
for chunk in pairs.split(";"):
  chunk=chunk.strip()
  if not chunk: 
    continue
  if "=" not in chunk:
    print(f"ERRO item sem '=': {chunk}"); sys.exit(1)
  k,v=chunk.split("=",1)
  k=k.strip(); v=v.strip()
  items[k]=v

print("\n--- VALIDAÇÃO (sem mostrar valor) ---")
for k in sorted(items):
  v=items[k]
  sha=hashlib.sha256(v.encode("utf-8")).hexdigest()
  print(f"{k}: len={len(v)} sha256={sha}")
PY
