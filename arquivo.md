- name: "Harness | Resolver current_machine (força override sem recursão)"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        (lookup('env','MACHINE') | default('') | string | trim)
        if (lookup('env','MACHINE') | default('') | string | trim) | length > 0
        else
          (
            (lookup('env','MACHINES') | default('') | string | trim).split(',')[0] | trim
            if (lookup('env','MACHINES') | default('') | string | trim) | length > 0
            else ''
          )
      }}
