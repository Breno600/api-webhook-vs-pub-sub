---
# =====================================================================================
# PREDEPLOY POR MÁQUINA
# Incluído pelo predeploy_from_execution.yml
#
# Compatível com:
# - include_tasks com loop padrão (item)
# - include_tasks com loop_control loop_var: machine_name
#
# Correções importantes:
# 1) Não usar variáveis definidas no mesmo set_fact
# 2) Resolver nome da máquina de modo seguro
# 3) Não usar "warn" no module command
# =====================================================================================

# -----------------------------------------------------------------------------
# 1) Resolver nome da máquina de forma segura
# -----------------------------------------------------------------------------
- name: "Resolver nome da máquina atual"
  ansible.builtin.set_fact:
    current_machine: >-
      {{
        machine_name
          if (machine_name is defined and (machine_name | string | trim) | length > 0)
          else item
      }}

# -----------------------------------------------------------------------------
# 2) Definir paths base do repo (sem dependência interna)
# -----------------------------------------------------------------------------
- name: "Definir paths base do repositório"
  ansible.builtin.set_fact:
    repo_root_safe: "{{ repo_root_resolved | default(playbook_dir ~ '/..') }}"
    status_root_safe: "{{ status_dir_resolved | default((playbook_dir ~ '/..') ~ '/status/' ~ (deployment_ref | default('')) ) }}"

# -----------------------------------------------------------------------------
# 3) Definir paths derivados do repo (agora seguro)
# -----------------------------------------------------------------------------
- name: "Definir caminhos para {{ current_machine }}"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"
    machines_dir: "{{ repo_root_safe }}/execution/machines"
    packages_dir: "{{ repo_root_safe }}/packages"

    machine_file: "{{ machines_dir }}/{{ current_machine }}.yml"

    machine_status_dir: "{{ status_root_safe }}/{{ current_machine }}"
    machine_status_file: "{{ machine_status_dir }}/status.json"

    deploy_base_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy"
    deploy_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    deploy_scripts_package_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"

# -----------------------------------------------------------------------------
# 4) Validar arquivo da máquina
# -----------------------------------------------------------------------------
- name: "Validar arquivo da máquina"
  ansible.builtin.stat:
    path: "{{ machine_file }}"
  register: machine_file_stat

# Se você quiser o comportamento do teu log (onde isso estava skipando),
# troque o when abaixo por "when: false".
- name: "Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: "Arquivo de máquina não encontrado: {{ machine_file }}"
  when: not machine_file_stat.stat.exists

# -----------------------------------------------------------------------------
# 5) Carregar config da máquina
# -----------------------------------------------------------------------------
- name: "Carregar config da máquina {{ current_machine }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

# -----------------------------------------------------------------------------
# 6) Validar package definido
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
# 7) Carregar config do pacote
# -----------------------------------------------------------------------------
- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    name: package_cfg

# -----------------------------------------------------------------------------
# 8) Validar script do pacote
# -----------------------------------------------------------------------------
- name: "Validar script do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      - package_cfg.script is defined or package_cfg.scripts is defined
    fail_msg: "Config de script ausente no pacote {{ machine_cfg.package }}"
    success_msg: "All assertions passed"

# -----------------------------------------------------------------------------
# 9) Validar components do pacote
# -----------------------------------------------------------------------------
- name: "Validar components do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg.components is defined
      - package_cfg.components | length > 0
    fail_msg: "components ausente/vazio no pacote {{ machine_cfg.package }}"
    success_msg: "All assertions passed"

# -----------------------------------------------------------------------------
# 10) Definir usuário alvo padrão
# -----------------------------------------------------------------------------
- name: "Definir usuário alvo padrão"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.target_user | default('root') }}"

# -----------------------------------------------------------------------------
# 11) Definir ssh_common_args padrão
# -----------------------------------------------------------------------------
- name: "Definir ssh_common_args padrão"
  ansible.builtin.set_fact:
    ssh_common_args: "{{ machine_cfg.ssh_common_args | default('-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null') }}"

# -----------------------------------------------------------------------------
# 12) Aplicar ProxyJump via bastion (quando necessário)
# -----------------------------------------------------------------------------
- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    ssh_common_args: >-
      {{ ssh_common_args }}
      -o ProxyJump={{ machine_cfg.bastion_user | default(target_user) }}@{{ machine_cfg.bastion_host }}
  when:
    - machine_cfg.bastion_host is defined
    - (machine_cfg.bastion_host | string | length) > 0

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
# 14) Montar env final
# -----------------------------------------------------------------------------
- name: "Montar env final"
  ansible.builtin.set_fact:
    env_final:
      MACHINE_NAME: "{{ current_machine }}"
      DEPLOYMENT_REF: "{{ deployment_ref | default('') }}"
      FILESTORE_ENV: "{{ filestore_env_resolved | default(filestore_env | default('dev')) }}"
      FILESTORE_BASE_DIR: "{{ filestore_base_dir_resolved | default(filestore_base_dir | default('')) }}"
      NEXUS_BASE_URL: "{{ nexus_base_url | default('') }}"
      PACKAGE: "{{ machine_cfg.package }}"

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
        "filestore_env": "{{ filestore_env_resolved | default(filestore_env | default('dev')) }}",
        "filestore_base_dir": "{{ filestore_base_dir_resolved | default(filestore_base_dir | default('')) }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 16) Garantir base do pipeline no host
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
# 17) Limpar pasta de scripts do predeploy
#     (sem 'warn' e sem command)
# -----------------------------------------------------------------------------
- name: "Limpar pasta de scripts do predeploy"
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
# 18) Copiar scripts do pacote (ajuste se teu repo usar outro caminho)
# -----------------------------------------------------------------------------
- name: "Copiar scripts do pacote para o host"
  ansible.builtin.copy:
    src: "{{ repo_root_safe }}/scripts/package/{{ machine_cfg.package }}/"
    dest: "{{ deploy_scripts_package_dir }}/"
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"
  ignore_errors: true

# -----------------------------------------------------------------------------
# 19) Atualizar status para predeploy_ready
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
        "filestore_env": "{{ filestore_env_resolved | default(filestore_env | default('dev')) }}",
        "filestore_base_dir": "{{ filestore_base_dir_resolved | default(filestore_base_dir | default('')) }}",
        "nexus_base_url": "{{ nexus_base_url | default('') }}"
      }
  changed_when: true
