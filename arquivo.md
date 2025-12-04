---
# ansible/predeploy_per_machine.yml
#
# Tarefas executadas para CADA máquina no pré-deploy.
# Variáveis disponíveis (vindas do include_tasks em predeploy_from_execution.yml):
# - machine_name
# - repo_root
# - status_dir
# - deployment_ref
#
# O que este arquivo faz, passo a passo:
# 1) Descobre o arquivo da máquina (machine/<machine_name>.yml)
# 2) Carrega host, package, rollback, env_vars, etc.
# 3) Carrega o package (package/<package>.yml) para descobrir script e components.
# 4) Cria/atualiza um JSON de status com "queued".
# 5) No host da máquina:
#    - garante o diretório /opt/SoftwareExpress/sitef/package/linux
#    - baixa os artefatos do Nexus (components)
#    - executa o script init_parallel.sh do package
# 6) Atualiza o JSON para "success" (ou você pode adaptar para tratar erro depois).

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

- name: "Definir caminhos de artefatos e script para {{ machine_name }}"
  ansible.builtin.set_fact:
    # Diretório onde o RPM ficará na MÁQUINA destino
    sitef_pkg_dir: "/opt/SoftwareExpress/sitef/package/linux"
    # Caminho do script init_parallel.sh no repositório (control node)
    package_script_path: "{{ repo_root }}/scripts/{{ package_cfg.script }}/init_parallel.sh"

# ------------------------------------------------------------------------------
# 1ª gravação do JSON: status = queued
# ------------------------------------------------------------------------------
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

# ------------------------------------------------------------------------------
# PRÉ-DEPLOY NO HOST (download do Nexus + backup via init_parallel.sh)
# Tudo que é "pesado" acontece na máquina remota via delegate_to.
# ------------------------------------------------------------------------------

- name: "Garantir diretório de packages no host {{ machine_cfg.host }}"
  ansible.builtin.file:
    path: "{{ sitef_pkg_dir }}"
    state: directory
    owner: root
    group: root
    mode: "0755"
  delegate_to: "{{ machine_cfg.host }}"
  become: true

- name: "Baixar componentes do package {{ machine_cfg.package }} para {{ machine_name }}"
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ sitef_pkg_dir }}/"
    mode: "0644"
    url_username: "{{ nexus_user }}"
    url_password: "{{ nexus_password }}"
    force_basic_auth: true
  loop: "{{ package_cfg.components | default([]) }}"
  when: package_cfg.components is defined and (package_cfg.components | length) > 0
  delegate_to: "{{ machine_cfg.host }}"
  become: true
  register: download_result

- name: "Executar script de pré-deploy (init_parallel.sh) em {{ machine_name }}"
  ansible.builtin.shell: |
    set -e
    cd "{{ sitef_pkg_dir }}"
    # Exporta variáveis de ambiente da máquina (env_vars) antes do script
    {{ machine_cfg.env_vars | default({}) | dict2items | map('regex_replace', '^(.*)$', 'export \1="{{ machine_cfg.env_vars[\\1] }}"') | join('\n') }}
    bash "{{ package_script_remote }}"
  args:
    executable: /bin/bash
  delegate_to: "{{ machine_cfg.host }}"
  become: true
  environment: "{{ machine_cfg.env_vars | default({}) }}"
  register: predeploy_result
  vars:
    # Caminho do script no host remoto: copiamos o init_parallel.sh junto com o repo
    # Ex.: /tmp/elastic-compute-cloud-sitef/scripts/deploy-sitef-0.0.2/init_parallel.sh
    package_script_remote: "{{ package_script_path }}"

# ------------------------------------------------------------------------------
# Atualiza JSON para success
# ------------------------------------------------------------------------------
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
