- name: "Harness | Resolver token (vars > env)"
  no_log: true
  ansible.builtin.set_fact:
    # NÃO referenciar harness_api_key/harness_token aqui (pode estar recursivo)
    harness_api_key_resolved: >-
      {{
        (harness_api_key_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_API_KEY') | default('', true) | string | trim), true)
          | default((lookup('ansible.builtin.env','HARNESS_PAT')     | default('', true) | string | trim), true)
          | default((lookup('ansible.builtin.env','HARNESS_TOKEN')   | default('', true) | string | trim), true)
      }}


- name: "Harness | Debug seguro do token (não mostra valor)"
  ansible.builtin.debug:
    msg: >-
      Harness token presente? {{
        (harness_api_key_resolved | default('') | length) > 10
      }} (len={{ harness_api_key_resolved | default('') | length }})

- name: "Harness | Falhar se token vazio"
  ansible.builtin.assert:
    that:
      - (harness_api_key_resolved | default('') | length) > 10
    fail_msg: >-
      Harness token não encontrado. Defina harness_api_key_override OU exporte
      HARNESS_API_KEY (ou HARNESS_PAT / HARNESS_TOKEN) no step ANTES do ansible.
