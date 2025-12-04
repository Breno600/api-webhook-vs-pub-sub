---
# ansible/predeploy_per_machine.yml
#
# Tarefas executadas para CADA máquina no pré-deploy.
# Variáveis esperadas:
#   - machine_name      (vem do loop no predeploy_from_execution.yml)
#   - repo_root         (definido no play pai)
#   - status_dir        (definido no play pai)
#   - deployment_ref    (definido no play pai)
#   - nexus_base_url    (extra-vars / group_vars)
#   - nexus_user / nexus_password (opcionais)

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    sitef_base_dir: "/opt/SoftwareExpress/sitef"
    machine_file:   "{{ repo_root }}/../machine/{{ machine_name }}.yml"
    status_file:    "{{ status_dir }}/predeploy-{{ machine_name }}.json"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/../package/{{ machine_cfg.package }}.yml"
    name: package_cfg

# ---------------------------------------------------------------------------
# 1) Status inicial "queued" (JSON fica no repositório, host do Ansible)
# ---------------------------------------------------------------------------

- name: "Criar status inicial (queued) para {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "queued",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}"
      }

# ---------------------------------------------------------------------------
# 2) CRIAR DIRS / COPIAR RPM E SCRIPTS **NA MÁQUINA ALVO**
# ---------------------------------------------------------------------------

- name: "Garantir diretórios base no host {{ machine_cfg.host }}"
  become: true
  delegate_to: "{{ machine_cfg.host }}"
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  loop:
    - "{{ sitef_base_dir }}"
    - "{{ sitef_base_dir }}/package"
    - "{{ sitef_base_dir }}/package/linux"
    - "{{ sitef_base_dir }}/scripts"

- name: "Baixar componentes do package {{ machine_cfg.package }} para {{ machine_cfg.host }}"
  become: true
  delegate_to: "{{ machine_cfg.host }}"
  ansible.builtin.get_url:
    # Ex.: components:
    #   - packages/linux/sitef-core-0.0.1-0.x86_64.rpm
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ sitef_base_dir }}/package/linux/{{ item | basename }}"
    mode: "0644"
    force: true
    url_username: "{{ nexus_user | default(omit) }}"
    url_password: "{{ nexus_password | default(omit) }}"
    force_basic_auth: "{{ (nexus_user is defined) | ternary(true, omit) }}"
  loop: "{{ package_cfg.components }}"

- name: "Copiar scripts do package {{ package_cfg.script }} para {{ machine_cfg.host }}"
  become: true
  delegate_to: "{{ machine_cfg.host }}"
  ansible.builtin.copy:
    # pasta local no repo: ../scripts/<script_name>/*
    src: "{{ repo_root }}/../scripts/{{ package_cfg.script }}/"
    dest: "{{ sitef_base_dir }}/scripts/{{ package_cfg.script }}/"
    mode: "0755"

# ---------------------------------------------------------------------------
# 3) Executar pré-deploy via init_parallel.sh **NA MÁQUINA ALVO**
# ---------------------------------------------------------------------------

- name: "Executar script de pré-deploy (init_parallel.sh) em {{ machine_cfg.host }}"
  become: true
  delegate_to: "{{ machine_cfg.host }}"
  ansible.builtin.shell: |
    cd "{{ sitef_base_dir }}/scripts/{{ package_cfg.script }}"
    ./init_parallel.sh
  args:
    executable: /bin/bash
  environment: "{{ machine_cfg.env_vars | default({}) }}"
  register: predeploy_result

# ---------------------------------------------------------------------------
# 4) Atualizar JSON com status=success
# ---------------------------------------------------------------------------

- name: "Atualizar status para success de {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "success",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "stdout": {{ predeploy_result.stdout | to_json }},
        "stderr": {{ predeploy_result.stderr | to_json }}
      }
  when: predeploy_result is succeeded