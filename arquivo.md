- name: "Harness | Ler envs (MACHINE/MACHINES) e normalizar"
  ansible.builtin.set_fact:
    _env_MACHINE: "{{ lookup('ansible.builtin.env', 'MACHINE') | default('', true) | string | trim }}"
    _env_MACHINES: "{{ lookup('ansible.builtin.env', 'MACHINES') | default('', true) | string | trim }}"
    _machine_name: "{{ machine_name | default('') | string | trim }}"

- name: "Harness | Resolver current_machine de forma segura (evita recursão)"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        _env_MACHINE
        or _machine_name
        or (_env_MACHINES.split(',')[0] | trim if (_env_MACHINES | length) > 0 else '')
        or 'localhost'
      }}
