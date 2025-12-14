breno@LWGMFC264:~$ curl -i -X POST \
'https://harness.onefiserv.net/ng/api/file-store?accountIdentifier=fgDto6qoTT6ctfZS9eWbEw&orgIdentifier=Fiserv&projectIdentifier=sitef' \
-H 'Content-Type: multipart/form-data' \
-F 'tags=testeee' \
-F 'name=curl-breno' \
-F 'type=FILE' \
-F 'parentIdentifier=Root' \
-H 'x-api-key: pat.fgDto6qoTT6ctfZS9eWbEw.693f147c43bfca2e849b46f4.WtMpaUZG5pxwDZcIkzl0'
HTTP/2 500
date: Sun, 14 Dec 2025 20:00:24 GMT
content-type: application/json;charset=utf-8
content-length: 240
strict-transport-security: max-age=15724800; includeSubDomains

{"status":"ERROR","code":"UNKNOWN_ERROR","message":"Oops, something went wrong on our end. Please contact Harness Support.","correlatbreno@LWGMFC264:~$ ^C
