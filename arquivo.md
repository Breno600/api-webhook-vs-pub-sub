TASK [Predeploy | Upload JSON + LOG para Harness File Store (sempre)] **********
included: /tmp/tmp.DjKOwhz33x/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml for localhost
TASK [Harness | Validar vars mínimas] ******************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Harness | Definir defaults + normalizações] ******************************
ok: [localhost]
TASK [Harness | Definir stage_label] *******************************************
fatal: [localhost]: FAILED! => {"msg": "The task includes an option with an undefined variable. The error was: 'stage_label' is undefined. 'stage_label' is undefined\n\nThe error appears to be in '/tmp/tmp.DjKOwhz33x/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml': line 62, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n\n- name: \"Harness | Definir stage_label\"\n  ^ here\n"}
PLAY RECAP *********************************************************************
localhost                  : ok=50   changed=11   unreachable=0    failed=1    skipped=4    rescued=0    ignored=0   
Command finished with status FAILURE
