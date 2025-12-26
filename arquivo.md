TASK [Harness | Falhar GIT_TAG se não OK] **************************************
skipping: [localhost]
TASK [Harness | Definir nomes versionados (evitar DUPLICATE_FIELD)] ************
fatal: [localhost]: FAILED! => {"msg": "The task includes an option with an undefined variable. The error was: 'hfs_tag_safe' is undefined. 'hfs_tag_safe' is undefined\n\nThe error appears to be in '/tmp/tmp.1wQCnxfkHv/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml': line 275, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n\n- name: \"Harness | Definir nomes versionados (evitar DUPLICATE_FIELD)\"\n  ^ here\n"}
PLAY RECAP *********************************************************************
localhost                  : ok=67   changed=11   unreachable=0    failed=1    skipped=7    rescued=0    ignored=0   
