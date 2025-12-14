curl -v --request POST \
  'https://harness.onefiserv.net/ng/api/file-store?accountIdentifier=fgDto6qoTT6ctfZS9eWbEw&orgIdentifier=Fiserv&projectIdentifier=sitef' \
  -H 'x-api-key: pat.fgDto6qoTT6ctfZS9eWbEw.693f147c43bfca2e849b46f4.WtMpaUZG5pxwDZcIkzl0' \
  -F 'name=curl-breno' \
  -F 'type=FILE' \
  -F 'parentIdentifier=Root' \
  -F 'tags=[{"key":"env","value":"testeee"}]' \
  -F 'content=@<ARQUIVO>'
