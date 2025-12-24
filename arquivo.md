- name: "Harness | Garantir folder ENV (POST) com debug"
  ansible.builtin.uri:
    url: "{{ harness_base_url }}/ng/api/file-store?accountIdentifier={{ harness_account }}&orgIdentifier={{ harness_org }}&projectIdentifier={{ harness_project }}"
    method: POST
    headers:
      x-api-key: "{{ harness_token }}"
      Content-Type: "application/json"
    body_format: json
    body:
      name: "{{ filestore_env }}"
      type: "FOLDER"
      parentIdentifier: "Root"
      identifier: "{{ filestore_env_identifier | default(filestore_env) }}"
    return_content: true
    status_code: [200, 201, 400]   # <<< aceita 400
  register: harness_env_post

- name: "Harness | Considerar OK quando pasta ENV já existe"
  ansible.builtin.set_fact:
    harness_env_ok: >-
      {{
        (harness_env_post.status in [200, 201])
        or (
          harness_env_post.status == 400
          and (harness_env_post.json.code | default('')) == 'DUPLICATE_FIELD'
        )
      }}

- name: "Harness | Falhar ENV (com body) - só se NÃO for DUPLICATE_FIELD"
  ansible.builtin.fail:
    msg: |
      Falha ao criar/garantir folder ENV no Harness.
      http_code={{ harness_env_post.status }}
      body={{ harness_env_post.content | default(harness_env_post.json | default({})) }}
  when: not harness_env_ok
