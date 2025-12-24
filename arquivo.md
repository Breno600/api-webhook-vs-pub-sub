export HARNESS_ENDPOINT="https://harness.onefiserv.net"
export ACC="fgDto6qoTT6ctfZS9eWbEw"
export ORG="Fiserv"
export PROJ="sitef"
export TOKEN="SEU_PAT_AQUI"

echo "teste" > /tmp/hf_test.txt

curl -v -sS -o /tmp/hf_resp.json -w "\nHTTP_CODE=%{http_code}\n" \
  -X POST "$HARNESS_ENDPOINT/ng/api/file-store?accountIdentifier=$ACC&orgIdentifier=$ORG&projectIdentifier=$PROJ" \
  -H "x-api-key: $TOKEN" \
  -F "name=hf_test_$(date +%s).txt" \
  -F "type=FILE" \
  -F "parentIdentifier=Root" \
  -F "file=@/tmp/hf_test.txt;type=text/plain"

echo "BODY:"
cat /tmp/hf_resp.json
echo
