TASK [Upload JSON + LOG do ROLLBACK para Harness File Store (via Ansible) - SEMPRE] ***
included: /tmp/tmp.2tC2xxxcos/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
fatal: [localhost]: FAILED! => {
    "assertion": "log_content is defined",
    "changed": false,
    "evaluated_to": false,
    "msg": "Faltam vars obrigatórias para harness_filestore_upload.yml"
}
PLAY RECAP *********************************************************************
localhost                  : ok=44   changed=9    unreachable=0    failed=1    skipped=3    rescued=0    ignored=0   
Command finished with status FAILURE
