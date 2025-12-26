ok: [localhost]
TASK [Harness | Definir leaf_identifier_final (folder da máquina/stage)] *******
fatal: [localhost]: FAILED! => {"msg": "The task includes an option with an undefined variable. The error was: 'leaf_identifier_raw' is undefined. 'leaf_identifier_raw' is undefined\n\nThe error appears to be in '/tmp/tmp.Rpc2XDrVEM/elastic-compute-cloud-sitef/ansible/harness_filestore_upload.yml': line 72, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n# Para o IDENTIFIER do leaf (sem hífen, e é isso que o UI costuma mostrar)\n- name: \"Harness | Definir leaf_identifier_final (folder da máquina/stage)\"\n  ^ here\n"}
PLAY RECAP *********************************************************************
localhost                  : ok=51   changed=11   unreachable=0    failed=1    skipped=4    rescued=0    ignored=0   
