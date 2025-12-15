curl -s -X GET \
"https://harness.onefiserv.net/ng/api/file-store/list?accountIdentifier=fgDto6qoTT6ctfZS9eWbEw&orgIdentifier=Fiserv&projectIdentifier=sitef&parentIdentifier=dev000000002" \
-H "x-api-key: $HARNESS_X_API_KEY" | jq -r '.data.content[].name'
