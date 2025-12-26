segue log

TASK [Harness | Falhar ENV se não for OK e não for DUPLICATE_FIELD] ************
fatal: [localhost]: FAILED! => {"changed": false, "msg": "Falha ao criar/garantir folder ENV no Harness.\nhttp_code=400\nbody={\"status\":\"ERROR\",\"code\":\"DUPLICATE_FIELD\",\"message\":\"Try another name, folder with name [dev] already exists in the parent folder [Root].\",\"correlationId\":\"659e75a4-79ac-49cb-9694-39c3148d38b6\",\"detailedMessage\":null,\"responseMessages\":[{\"code\":\"DUPLICATE_FIELD\",\"level\":\"ERROR\",\"message\":\"Try another name, folder with name [dev] already exists in the parent folder [Root].\",\"exception\":null,\"failureTypes\":[],\"additionalInfo\":{}}],\"metadata\":null}\n"}

segue ansible
- name: "Harness | Falhar ENV se não for OK e não for DUPLICATE_FIELD"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ hfs_env_http_code | default('') }}
      body={{ hfs_env_create_body.stdout | default('') }}
  when:
    - (hfs_env_http_code | default('') | trim) not in hfs_ok_create_codes
    - not (hfs_env_is_duplicate | bool)
