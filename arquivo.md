---
# =====================================================================================
# PREDEPLOY POR MÁQUINA
# Arquivo incluído pelo predeploy_from_execution.yml
#
# Este arquivo roda 1x por máquina (loop item=sitef-01, etc.)
# Objetivos:
# - Definir paths locais e remotos
# - Carregar config de máquina
# - Validar package definidos
# - Carregar config do pacote
# - Validar script/components do pacote
# - Registrar host dinâmico
# - Preparar diretórios
# - Limpar pasta de scripts (SEM usar "warn" no command)
# - Criar status inicial
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Normalizar nome da máquina (compatível com include loop padrão)
# -----------------------------------------------------------------------------
- name: "Definir máquina atual"
  ansible.builtin.set_fact:
    machine_name: "{{ item }}"
  when: machine_name is not defined

# -----------------------------------------------------------------------------
# 2) Definir caminhos para a máquina
#    Ajuste APENAS ESTES PATHS se teu repo tiver layout diferente.
# -----------------------------------------------------------------------------
- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    # --- Repo ---
    # repo_root_resolved vem do predeploy_from_execution.yml (log mostra isso)
    execution_dir: "{{ repo_root_resolved }}/execution"
    machines_dir: "{{ execution_dir }}/machines"
    packages_dir: "{{ repo_root_resolved }}/packages"

    # --- Arquivo da máquina ---
    machine_file: "{{ machines_dir }}/{{ machine_name }}.yml"

    # --- Diretório de status ---
    # status_dir_resolved vem do predeploy_from_execution.yml
    machine_status_dir: "{{ status_dir_resolved }}/{{ machine_name }}"
    machine_status_file: "{{ machine_status_dir }}/status.json"

    # --- Deploy remoto ---
    deploy_base_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy"
    deploy_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    deploy_scripts_package_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"

# -----------------------------------------------------------------------------
# 3) Validar arquivo da máquina
# -----------------------------------------------------------------------------
- name: "Validar arquivo da máquina"
  ansible.builtin.stat:
    path: "{{ machine_file }}"
  register: machine_file_stat

- name: "Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: "Arquivo de máquina não encontrado: {{ machine_file }}"
  when: not machine_file_stat.stat.exists
  # No teu log esse fail estava SKIPando.
  # Se você quer manter o comportamento do log, comente o fail acima e use:
  # when: false

# -----------------------------------------------------------------------------
# 4) Carregar config da máquina
# -----------------------------------------------------------------------------
- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

# -----------------------------------------------------------------------------
# 5) Validar package definido
# -----------------------------------------------------------------------------
- name: "Validar package definido"
  ansible.builtin.assert:
    that:
      - machine_cfg is defined
      - machine_cfg.package is defined
      - (machine_cfg.package | string | length) > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"
    success_msg: "All assertions passed"

# -----------------------------------------------------------------------------
# 6) Carregar config do pacote
#    Aqui assumo que existe um YAML em packages/<package>.yml
# -----------------------------------------------------------------------------
- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 7) Validar script do pacote
# -----------------------------------------------------------------------------
- name: "Validar script do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      # ajuste o nome do campo conforme teu padrão
      # Ex.: package_cfg.script, package_cfg.scripts, etc.
      - package_cfg.script is defined or package_cfg.scripts is defined
    fail_msg: "Config de script ausente no pacote {{ machine_cfg.package }}"
    success_msg: "All assertions passed"

# -----------------------------------------------------------------------------
# 8) Validar components do pacote
# -----------------------------------------------------------------------------
- name: "Validar components do pacote"
  ansible.builtin.assert:
    that:
      # ajuste o campo conforme teu padrão real
      - package_cfg.components is defined
      - package_cfg.components | length > 0
    fail_msg: "components ausente/vazio no pacote {{ machine_cfg.package }}"
    success_msg: "All assertions passed"

# -----------------------------------------------------------------------------
# 9) Definir usuário alvo padrão
# -----------------------------------------------------------------------------
- name: "Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.target_user | default('root') }}"

# -----------------------------------------------------------------------------
# 10) Definir ssh_common_args padrão
# -----------------------------------------------------------------------------
- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

# -----------------------------------------------------------------------------
# 11) Aplicar ProxyJump via bastion (quando necessário)
#     Mantido para bater com o log (task pode skipar)
# -----------------------------------------------------------------------------
- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - machine_cfg.bastion_host | string | length > 0

# -----------------------------------------------------------------------------
# 12) Registrar host dinâmico para {{ machine_name }}
#     Isso permite delegate_to e uso do host em tasks seguintes
# -----------------------------------------------------------------------------
- name: "Registrar host dinâmico para {{ machine_name }}"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.ip | default(machine_cfg.ansible_host) }}"
    ansible_user: "{{ target_user }}"
    ansible_ssh_common_args: "{{ ssh_common_args }}"
  changed_when: true

# -----------------------------------------------------------------------------
# 13) Montar env final
#     Aqui você pode juntar variáveis globais + machine_cfg + package_cfg
# -----------------------------------------------------------------------------
- name: "Montar env final"
  ansible.builtin.set_fact:
    env_final:
      MACHINE_NAME: "{{ machine_name }}"
      DEPLOYMENT_REF: "{{ deployment_ref | default('') }}"
      FILESTORE_ENV: "{{ filestore_env_resolved | default('') }}"
      FILESTORE_BASE_DIR: "{{ filestore_base_dir_resolved | default('') }}"
      NEXUS_BASE_URL: "{{ nexus_base_url | default('') }}"
      PACKAGE: "{{ machine_cfg.package }}"
      # Você pode adicionar qualquer var necessária ao teu script
      # Ex.: versões específicas, paths, feature flags, etc.

# -----------------------------------------------------------------------------
# 14) Criar status inicial (queued)
# -----------------------------------------------------------------------------
- name: "Criar status inicial (queued)"
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
        "machine": "{{ machine_name }}",
        "package": "{{ machine_cfg.package }}",
        "status": "queued",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "filestore_env": "{{ filestore_env_resolved | default('') }}",
        "filestore_base_dir": "{{ filestore_base_dir_resolved | default('') }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 15) Garantir base do pipeline no host
# -----------------------------------------------------------------------------
- name: "Garantir base do pipeline no host"
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ machine_name }}"
  loop:
    - "{{ deploy_base_dir }}"
    - "{{ deploy_scripts_dir }}"
    - "{{ deploy_scripts_package_dir }}"

# -----------------------------------------------------------------------------
# 16) Limpar pasta de scripts do predeploy
#     ✅ CORREÇÃO DEFINITIVA: nada de "warn" no command.
#     Usar file é o jeito mais seguro e compatível.
# -----------------------------------------------------------------------------
- name: "Limpar pasta de scripts do predeploy"
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: absent
  become: true
  delegate_to: "{{ machine_name }}"

- name: "Recriar pasta de scripts do pacote"
  ansible.builtin.file:
    path: "{{ deploy_scripts_package_dir }}"
    state: directory
    mode: "0755"
  become: true
  delegate_to: "{{ machine_name }}"

# -----------------------------------------------------------------------------
# 17) (Opcional) Copiar scripts do pacote / preparar execução
#     Ajuste conforme tua estrutura real de scripts.
# -----------------------------------------------------------------------------
- name: "Copiar scripts do pacote para o host"
  ansible.builtin.copy:
    src: "{{ repo_root_resolved }}/scripts/package/{{ machine_cfg.package }}/"
    dest: "{{ deploy_scripts_package_dir }}/"
    mode: "0755"
  become: true
  delegate_to: "{{ machine_name }}"
  ignore_errors: true
  # ignore_errors porque eu não sei se esse path existe no teu repo.
  # Se existir, remova o ignore_errors.

# -----------------------------------------------------------------------------
# 18) (Opcional) Atualizar status para 'predeploy_ready'
# -----------------------------------------------------------------------------
- name: "Atualizar status para predeploy_ready"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ machine_name }}",
        "package": "{{ machine_cfg.package }}",
        "status": "predeploy_ready",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "filestore_env": "{{ filestore_env_resolved | default('') }}",
        "filestore_base_dir": "{{ filestore_base_dir_resolved | default('') }}",
        "nexus_base_url": "{{ nexus_base_url | default('') }}"
      }
  changed_when: true
