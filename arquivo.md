---
# =====================================================================
# PLAY 1 - CONTROLLER (localhost)
# Lê execution/<arquivo>.yml e prepara hosts dinâmicos + JSON status
# =====================================================================
- name: "[CONTROLLER] Preparar predeploy a partir do execution"
  hosts: localhost
  gather_facts: false

  vars:
    repo_root: "{{ playbook_dir | dirname }}"

    # Variáveis vindas da pipeline:
    #  - execution_file_name: nome do YAML em execution/
    #  - deployment_ref: TAG do Git (ex: DEV000000002)
    execution_file_name: "{{ execution_file_name | default('machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV_LOCAL') }}"

    execution_file: "{{ repo_root }}/execution/{{ execution_file_name }}"

  tasks:
    - name: "Carregar arquivo do execution (lista de máquinas)"
      ansible.builtin.include_vars:
        file: "{{ execution_file }}"
        name: execution_cfg

    - name: "Definir lista de máquinas do execution"
      ansible.builtin.set_fact:
        execution_machines: "{{ execution_cfg.machines | default([]) }}"

    - name: "Falhar se nao houver nenhuma maquina no execution"
      ansible.builtin.fail:
        msg: "Nenhuma maquina definida em {{ execution_file }} (chave 'machines')."
      when: execution_machines | length == 0

    - name: "Garantir diretório de status para TAG"
      ansible.builtin.file:
        path: "{{ repo_root }}/status/{{ deployment_ref }}"
        state: directory
        mode: "0755"

    # Para cada máquina:
    #   - lê machine/<machine>.yaml
    #   - lê package/<package>.yaml
    #   - cria host dinâmico para o PLAY 2
    #   - grava JSON inicial (status=queued)
    - name: "Registrar hosts dinamicos e gerar JSON 'queued'"
      vars:
        machine_file: "{{ repo_root }}/machine/{{ item }}.yaml"
        machine_cfg: "{{ lookup('file', machine_file) | from_yaml }}"
        package_name: "{{ machine_cfg.package }}"
        package_file: "{{ repo_root }}/package/{{ package_name }}.yaml"
        package_cfg: "{{ lookup('file', package_file) | from_yaml }}"
        status_file: "{{ repo_root }}/status/{{ deployment_ref }}/predeploy-{{ item }}.json"
      block:
        - name: "Registrar host dinamico para {{ item }}"
          ansible.builtin.add_host:
            name: "predeploy_{{ item }}"
            ansible_host: "{{ machine_cfg.host }}"
            ansible_user: ansible
            ansible_ssh_private_key_file: "~/.ssh/id_rsa"
            groups: dynamic_predeploy

            # Metadados que vão para o PLAY 2
            machine_name: "{{ item }}"
            package_name: "{{ package_name }}"
            rollback_name: "{{ machine_cfg.rollback | default('') }}"
            env_vars: "{{ machine_cfg.env_vars | default({}) }}"
            package_script: "{{ package_cfg.script }}"
            package_components: "{{ package_cfg.components | default([]) }}"
            deployment_ref: "{{ deployment_ref }}"
            status_file: "{{ status_file }}"

        - name: "Gravar JSON de status 'queued' para {{ item }}"
          ansible.builtin.copy:
            dest: "{{ status_file }}"
            mode: "0644"
            content: |
              {
                "machine": "{{ item }}",
                "host": "{{ machine_cfg.host }}",
                "package": "{{ package_name }}",
                "deployment_ref": "{{ deployment_ref }}",
                "status": "queued",
                "updated_at": "{{ lookup('pipe', 'date -u +%Y-%m-%dT%H:%M:%SZ') }}"
              }
      loop: "{{ execution_machines }}"
      loop_control:
        loop_var: item

# =====================================================================
# PLAY 2 - TARGETS (todas as máquinas em dynamic_predeploy)
# Executa init_parallel.sh em paralelo e atualiza JSON
# =====================================================================
- name: "[TARGET] Predeploy paralelo nas máquinas"
  hosts: dynamic_predeploy
  become: yes
  gather_facts: false

  vars:
    remote_base_dir: "/opt/sitef"
    remote_scripts_dir: "{{ remote_base_dir }}/scripts/{{ package_script }}"
    remote_packages_dir: "{{ remote_base_dir }}/packages"

  tasks:
    - name: "Criar diretórios base no target"
      ansible.builtin.file:
        path: "{{ item }}"
        state: directory
        mode: "0755"
      loop:
        - "{{ remote_base_dir }}"
        - "{{ remote_scripts_dir }}"
        - "{{ remote_packages_dir }}"

    - name: "Copiar scripts do package para o target"
      ansible.builtin.copy:
        src: "{{ (playbook_dir | dirname) }}/scripts/{{ package_script }}/"
        dest: "{{ remote_scripts_dir }}/"
        mode: "0755"

    - name: "Atualizar JSON para status 'running'"
      ansible.builtin.copy:
        dest: "{{ status_file }}"
        mode: "0644"
        content: |
          {
            "machine": "{{ machine_name }}",
            "host": "{{ ansible_host }}",
            "package": "{{ package_name }}",
            "deployment_ref": "{{ deployment_ref }}",
            "status": "running",
            "updated_at": "{{ lookup('pipe', 'date -u +%Y-%m-%dT%H:%M:%SZ') }}"
          }
      delegate_to: localhost
      run_once: false

    - name: "Executar init_parallel.sh (predeploy)"
      ansible.builtin.shell: |
        set -e
        cd {{ remote_base_dir }}
        if [ -x "scripts/{{ package_script }}/init_parallel.sh" ]; then
          echo "[PREDEPLOY] Executando scripts/{{ package_script }}/init_parallel.sh"
          bash scripts/{{ package_script }}/init_parallel.sh
        else
          echo "[PREDEPLOY] Nao encontrei scripts/{{ package_script }}/init_parallel.sh, nada a executar."
        fi
      args:
        executable: /bin/bash
      environment: "{{ env_vars | default({}) }}"
      register: predeploy_result
      changed_when: predeploy_result.rc == 0

    - name: "Atualizar JSON para 'success' ou 'failed'"
      ansible.builtin.copy:
        dest: "{{ status_file }}"
        mode: "0644"
        content: |
          {
            "machine": "{{ machine_name }}",
            "host": "{{ ansible_host }}",
            "package": "{{ package_name }}",
            "deployment_ref": "{{ deployment_ref }}",
            "status": "{{ 'success' if predeploy_result.rc == 0 else 'failed' }}",
            "updated_at": "{{ lookup('pipe', 'date -u +%Y-%m-%dT%H:%M:%SZ') }}"
          }
      delegate_to: localhost
      run_once: false