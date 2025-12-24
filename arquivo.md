- name: "Harness | Map token para header x-api-key (sem recursão)"
  no_log: true
  ansible.builtin.set_fact:
    harness_x_api_key_resolved: "{{ harness_api_key_resolved }}"

- name: "Harness | Falhar se token vazio (upload)"
  ansible.builtin.assert:
    that:
      - (harness_x_api_key_resolved | default('') | length) > 10
    fail_msg: "Token do Harness vazio para upload."
