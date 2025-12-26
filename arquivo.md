TASK [Harness | Normalizar http_code ENV] **************************************
ok: [localhost]
TASK [Harness | Detectar DUPLICATE_FIELD ENV (robusto)] ************************
fatal: [localhost]: FAILED! => {"msg": "Unexpected templating type error occurred on ({{\n  (hfs_env_http_code == '400')\n  and (\n    (\n      (hfs_env_create_body.stdout | default(''))\n      | regex_search('\\\\\"code\\\\\"\\\\\\\\s*:\\\\\\\\s*\\\\\"DUPLICATE_FIELD\\\\\"')\n      | default('')\n    ) | length > 0\n  )\n}}): object of type 'NoneType' has no len(). object of type 'NoneType' has no len()"}
PLAY RECAP *********************************************************************
localhost                  : ok=65   changed=11   unreachable=0    failed=1    skipped=5    rescued=0    ignored=0   
Command finished with status FAILURE
