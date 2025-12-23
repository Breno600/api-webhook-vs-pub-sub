#!/usr/bin/env bash
set -euo pipefail

# ajusta aqui os nomes dos arquivos exatamente como estão na sua máquina
WVC_P12="wvc-ap11753-me-kpc-d.1dc.com"
KAFKA_TRUSTSTORE="kafka-truststore"
SNOWFLAKE_CERT="zvc-ap11753-me-kpc-c.1dc.com"  # <- troque se o cert correto for outro arquivo

# base64 “uma linha só” (compatível Linux/macOS). Se der erro no -w, uso fallback.
b64_1line() {
  if base64 --help 2>/dev/null | grep -q -- "-w"; then
    base64 -w 0 "$1"
  else
    base64 "$1" | tr -d '\n'
  fi
}

PAIRS="$(
  printf "wvcAp11753=%s***kafka-truststore=%s***snowflake-cert=%s\n" \
    "$(b64_1line "$WVC_P12")" \
    "$(b64_1line "$KAFKA_TRUSTSTORE")" \
    "$(b64_1line "$SNOWFLAKE_CERT")"
)"

echo "$PAIRS"
echo
echo "Tamanho PAIRS: ${#PAIRS} chars"