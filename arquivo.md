---
# =====================================================================================
# PREDEPLOY POR MÁQUINA
# Incluído pelo predeploy_from_execution.yml
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Resolver nome da máquina atual
# -----------------------------------------------------------------------------
- name: "Resolver nome da máquina atual"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        (machine_name | default(item) | default('')) | string | trim
      }}
  when: current_machine is not defined or (current_machine | string | trim) | length == 0

# -----------------------------------------------------------------------------
# 2) Definir paths base do repositório
# -----------------------------------------------------------------------------
- name: "Definir paths base do repositório"
  ansible.builtin.set_fact:
    repo_root_safe: >-
      {{
        repo_root_resolved
          | default(repo_root | default(playbook_dir ~ '/..'))
      }}

# -----------------------------------------------------------------------------
# 3) Definir diretórios principais do repositório (FIX)
#    IMPORTANTÍSSIMO: separar em tarefas para não referenciar variável criada
#    no mesmo set_fact.
# -----------------------------------------------------------------------------
- name: "Definir execution_dir do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"

- name: "Definir machines_dir e packages_dir do repositório"
  ansible.builtin.set_fact:
    machines_dir: "{{ execution_dir }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"

# -----------------------------------------------------------------------------
# 4) Candidatos de arquivo de máquina (robusto)
# -----------------------------------------------------------------------------
- name: "Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ machines_dir }}/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/machines/{{ current_machine }}.yml"
      - "{{ repo_root_safe }}/inventory/machines/{{ current_machine }}.yml"

# -----------------------------------------------------------------------------
# 5) Verificar quais candidatos existem
# -----------------------------------------------------------------------------
- name: "Verificar candidatos de arquivo da máquina"
  ansible.builtin.stat:
    path: "{{ item }}"
  loop: "{{ candidate_machine_files }}"
  register: machine_candidates_stat

# -----------------------------------------------------------------------------
# 6) Selecionar o primeiro arquivo existente
# -----------------------------------------------------------------------------
- name: "Selecionar machine_file existente"
  ansible.builtin.set_fact:
    machine_file: "{{ item.item }}"
  when:
    - item.stat.exists
    - machine_file is not defined
  loop: "{{ machine_candidates_stat.results }}"

# -----------------------------------------------------------------------------
# 7) Falhar se nenhum candidato existir
# -----------------------------------------------------------------------------
- name: "Falhar se arquivo da máquina não existir em nenhum caminho esperado"
  ansible.builtin.fail:
    msg: |
      Arquivo de máquina não encontrado para {{ current_machine }}.
      Caminhos testados:
      {{ candidate_machine_files | to_nice_yaml }}
  when: machine_file is not defined

# -----------------------------------------------------------------------------
# 8) Carregar config da máquina
# -----------------------------------------------------------------------------
- name: "Carregar config da máquina {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

# -----------------------------------------------------------------------------
# 9) Validar package definido
# -----------------------------------------------------------------------------
- name: "Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.package is defined
      - (machine_cfg.package | string | trim) | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

# -----------------------------------------------------------------------------
# 10) Carregar config do pacote
# -----------------------------------------------------------------------------
- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 11) Validações mínimas do pacote (ajuste conforme padrão real)
# -----------------------------------------------------------------------------
- name: "Validar components do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      - package_cfg.components is defined
      - package_cfg.components | length > 0
    fail_msg: "components ausente/vazio no pacote {{ machine_cfg.package }}"

# -----------------------------------------------------------------------------
# 12) Usuário/SSH defaults
# -----------------------------------------------------------------------------
- name: "Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.target_user | default('root') }}"

- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - machine_cfg.bastion_host | string | length > 0

# -----------------------------------------------------------------------------
# 13) Registrar host dinâmico
# -----------------------------------------------------------------------------
- name: "Registrar host dinâmico para {{ current_machine }}"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.ip | default(machine_cfg.ansible_host) }}"
    ansible_user: "{{ target_user }}"
    ansible_ssh_common_args: "{{ ssh_common_args }}"
  changed_when: true

# -----------------------------------------------------------------------------
# 14) Definir caminhos de status e deploy
# -----------------------------------------------------------------------------
- name: "Definir caminhos de status e deploy"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir_resolved }}/{{ current_machine }}"
    machine_status_file: "{{ machine_status_dir }}/status.json"

    deploy_base_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy"
    deploy_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    deploy_scripts_package_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"

# -----------------------------------------------------------------------------
# 15) Criar status inicial (queued)
# -----------------------------------------------------------------------------
- name: "Criar diretório de status da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Escrever status inicial (queued)"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "package": "{{ machine_cfg.package }}",
        "status": "queued",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "filestore_env": "{{ filestore_env_resolved | default('') }}",
        "filestore_base_dir": "{{ filestore_base_dir_resolved | default('') }}",
        "nexus_base_url": "{{ nexus_base_url | default('') }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 16) Garantir base do pipeline no host remoto
# -----------------------------------------------------------------------------
- name: "Garantir base do pipeline no host"
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"
  loop:
    - "{{ deploy_base_dir }}"
    - "{{ deploy_scripts_dir }}"
    - "{{ deploy_scripts_package_dir }}"

# -----------------------------------------------------------------------------
# 17) Limpar pasta de scripts do pacote com segurança
# -----------------------------------------------------------------------------
- name: "Limpar pasta de scripts do pacote"
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: absent
  become: true
  delegate_to: "{{ current_machine }}"

- name: "Recriar pasta de scripts do pacote"
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 18) Atualizar status para predeploy_ready
# -----------------------------------------------------------------------------
- name: "Atualizar status para predeploy_ready"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "package": "{{ machine_cfg.package }}",
        "status": "predeploy_ready",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "filestore_env": "{{ filestore_env_resolved | default('') }}",
        "filestore_base_dir": "{{ filestore_base_dir_resolved | default('') }}",
        "nexus_base_url": "{{ nexus_base_url | default('') }}"
      }
  changed_when: true
