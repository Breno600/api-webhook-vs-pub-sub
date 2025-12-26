TASK [Harness | UPDATE status.json (se já existe)] *****************************
ok: [localhost]
TASK [Harness | Falhar status se create e update não deram certo] **************
fatal: [localhost]: FAILED! => {"changed": false, "msg": "Falha ao enviar status.json\ncreate_http=400\ncreate_body={\"status\":\"ERROR\",\"code\":\"DUPLICATE_FIELD\",\"message\":\"Try another name, file with name [status.json] already exists in the parent folder [sitef_02_pre_deploy].\",\"correlationId\":\"a6f13a83-aa77-4e5a-8b89-94714de6133e\",\"detailedMessage\":null,\"responseMessages\":[{\"code\":\"DUPLICATE_FIELD\",\"level\":\"ERROR\",\"message\":\"Try another name, file with name [status.json] already exists in the parent folder [sitef_02_pre_deploy].\",\"exception\":null,\"failureTypes\":[],\"additionalInfo\":{}}],\"metadata\":null}\nupdate_http=400\n"}
PLAY RECAP *********************************************************************
localhost                  : ok=75   changed=12   unreachable=0    failed=1    skipped=8    rescued=0    ignored=0   
Command finished with status FAILURE
