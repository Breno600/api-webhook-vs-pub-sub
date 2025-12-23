cd /home/breno/omnidata

cat > gen_pairs.sh <<'BASH'
#!/usr/bin/env bash
set -euo pipefail

# Arquivos (iguais ao seu ls)
KAFKA_TRUSTSTORE="kafka-truststore.p12"
WVC_P12="wvc-ap11753-me-kpc-d.1dc.com.p12"
ZVC_CERT_C="zvc-ap11753-me-kpc-c.1dc.com.p12"
ZVC_CERT_P="zvc-ap11753-me-kpc-p.1dc.com.p12"

# base64 em 1 linha só
b64_1line() {
  if base64 --help 2>/dev/null | grep -q -- "-w"; then
    base64 -w 0 "$1"
  else
    base64 "$1" | tr -d '\n'
  fi
}

# checagem
for f in "$KAFKA_TRUSTSTORE" "$WVC_P12" "$ZVC_CERT_C" "$ZVC_CERT_P"; do
  [[ -f "$f" ]] || { echo "ERRO: arquivo não encontrado: $f" >&2; exit 1; }
done

# >>> Versão COMPATÍVEL com as keys que você já estava usando no Secrets Manager:
# wvcAp11753, kafka-truststore, snowflake-cert
PAIRS="wvcAp11753=$(b64_1line "$WVC_P12")***kafka-truststore=$(b64_1line "$KAFKA_TRUSTSTORE")***snowflake-cert=$(b64_1line "$ZVC_CERT_C")"

# (Opcional) Se você quiser também levar o zvc -p como outra key (só descomenta):
# PAIRS="${PAIRS}***zvc-ap11753-me-kpc-p=$(b64_1line "$ZVC_CERT_P")"

# grava em arquivo pra você copiar/colar
umask 077
printf "%s" "$PAIRS" > pairs.txt

echo "OK: gerado pairs.txt"
echo "Tamanho: $(wc -c < pairs.txt | tr -d ' ') bytes"
BASH

chmod 700 gen_pairs.sh
./gen_pairs.sh