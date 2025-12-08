    # ----------------------------------------------------------
    # Definir package conforme ACTION
    # ----------------------------------------------------------
    - name: "Definir package a ser usado conforme ACTION"
      ansible.builtin.set_fact:
        selected_package: >-
          {{
            machine_cfg.package
            if action == 'deploy'
            else machine_cfg.rollback | default('')
          }}
