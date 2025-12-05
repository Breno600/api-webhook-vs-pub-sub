# ansible/predeploy_per_machine.yml
#
# Tarefas executadas para CADA máquina no pré-deploy.
# Variáveis de contexto (vêm do loop do play principal):
#   - machine_name
#   - repo_root
#   - status_dir
#   - deployment_ref
#
# Este arquivo foi ajustado para:
#   1) Evitar execução "local" acidental no bastion/controller
#   2) Garantir que delegate realmente execute no host alvo
#   3) Suportar bastion via ProxyJump opcional
#
# Como usar bastion:
#   - Cenário A (mais comum no seu fluxo):
#       O pipeline conecta no bastion via JSCH
#       e roda ansible *de dentro do bastion*.
#       => NÃO precisa ProxyJump.
#
#   - Cenário B:
#       Você roda ansible de fora da rede privada.
#       => Defina em algum lugar do play principal ou vars:
#            bastion_host: "SEU_IP_OU_DNS_DO_BASTION"
#            bastion_user: "ec2-user"
#         ou defina machine_cfg.ssh_common_args por máquina.
#
# Obs:
#   Este arquivo tenta respeitar machine/<nome>.yml usando:
#     machine_cfg.user (opcional)
#     machine_cfg.env_vars (opcional)
#     machine_cfg.ssh_common_args (opcional)
#     machine_cfg.package
#     machine_cfg.host

# ----------------------------------------------------------
# Definir caminhos locais de trabalho
# ----------------------------------------------------------
- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
    host_base_dir: "/opt/SoftwareExpress/sitef"
    host_pkg_dir: "/opt/SoftwareExpress/sitef/package/linux"
    host_scripts_dir: "/opt/SoftwareExpress/sitef/scripts"

# ----------------------------------------------------------
# Carregar configs
# ----------------------------------------------------------
- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

# ----------------------------------------------------------
# Calcular SSH args (com suporte a bastion opcional)
# ----------------------------------------------------------
- name: "Calcular ssh_common_args para {{ machine_name }}"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default('ec2-user') }}"
    target_ssh_common_args: >-
      {{
        machine_cfg.ssh_common_args
          | default(
              (bastion_host is defined)
                | ternary(
                    '-o ProxyJump=' ~ (bastion_user | default('ec2-user')) ~ '@' ~ bastion_host,
                    ''
                  )
            )
      }}

# ----------------------------------------------------------
# Registrar host dinâmico para GARANTIR conexão SSH real
# Isso evita o problema de "localhost/connection local"
# ----------------------------------------------------------
- name: "Registrar host dinâmico para {{ machine_name }}"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"

# ----------------------------------------------------------
# Status inicial (queued)
# ----------------------------------------------------------
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

# ----------------------------------------------------------
# Criar diretórios NA MÁQUINA ALVO
# ----------------------------------------------------------
- name: "Garantir diretórios base no host {{ machine_cfg.host }}"
  become: true
  ansible.builtin.file:
    path: "{{ item }}"
    state: directory
    mode: "0755"
    owner: root
    group: root
  loop:
    - "{{ host_base_dir }}"
    - "{{ host_pkg_dir }}"
    - "{{ host_scripts_dir }}"
    - "{{ host_scripts_dir }}/{{ package_cfg.script }}"
  delegate_to: "{{ machine_name }}"

# (Opcional mas útil) Provar no log que os diretórios existem do ponto de vista do host alvo
- name: "Stat diretórios base no host {{ machine_cfg.host }}"
  become: true
  ansible.builtin.stat:
    path: "{{ item }}"
  loop:
    - "{{ host_base_dir }}"
    - "{{ host_pkg_dir }}"
    - "{{ host_scripts_dir }}"
    - "{{ host_scripts_dir }}/{{ package_cfg.script }}"
  delegate_to: "{{ machine_name }}"
  register: dirs_stat

- debug:
    var: dirs_stat

# ----------------------------------------------------------
# Baixar RPM(s) do Nexus na MÁQUINA ALVO
# package_cfg.components:
#   - packages/linux/sitef-core-0.0.1-0.x86_64.rpm
# ----------------------------------------------------------
- name: "Baixar componentes do package {{ machine_cfg.package }} para {{ machine_name }}"
  become: true
  ansible.builtin.get_url:
    url: "{{ nexus_base_url }}/{{ item }}"
    dest: "{{ host_pkg_dir }}/{{ item | basename }}"
    mode: "0644"
    url_username: "{{ nexus_user }}"
    url_password: "{{ nexus_password }}"
    force: true
  loop: "{{ package_cfg.components }}"
  delegate_to: "{{ machine_name }}"
  register: geturl_result

- debug:
    var: geturl_result

# Verificar se o RPM realmente apareceu na máquina alvo
- name: "Verificar se RPM foi criado em {{ machine_cfg.host }}"
  become: true
  ansible.builtin.stat:
    path: "{{ host_pkg_dir }}/{{ (package_cfg.components[0] | basename) }}"
  delegate_to: "{{ machine_name }}"
  register: rpm_stat

- debug:
    var: rpm_stat

- name: "Falhar se RPM não existir em {{ host_pkg_dir }} na máquina {{ machine_cfg.host }}"
  ansible.builtin.fail:
    msg: "RPM {{ package_cfg.components[0] | basename }} não encontrado em {{ host_pkg_dir }} na máquina {{ machine_cfg.host }}"
  when: not rpm_stat.stat.exists

# ----------------------------------------------------------
# Copiar scripts do package para a máquina alvo
# scripts/<script>/...
# ----------------------------------------------------------
- name: "Copiar scripts do package {{ machine_cfg.package }} para {{ machine_name }}"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ package_cfg.script }}/"
    dest: "{{ host_scripts_dir }}/{{ package_cfg.script }}/"
    mode: "0755"
  delegate_to: "{{ machine_name }}"
  register: copy_scripts_result

- debug:
    var: copy_scripts_result

# Verificar se o init_parallel.sh está na máquina alvo
- name: "Verificar se init_parallel.sh existe na máquina alvo"
  become: true
  ansible.builtin.stat:
    path: "{{ host_scripts_dir }}/{{ package_cfg.script }}/init_parallel.sh"
  delegate_to: "{{ machine_name }}"
  register: init_stat

- debug:
    var: init_stat

- name: "Falhar se init_parallel.sh não existir na máquina {{ machine_cfg.host }}"
  ansible.builtin.fail:
    msg: "init_parallel.sh não encontrado em {{ host_scripts_dir }}/{{ package_cfg.script }} na máquina {{ machine_cfg.host }}"
  when: not init_stat.stat.exists

# ----------------------------------------------------------
# Executar o init_parallel.sh NA MÁQUINA ALVO
# Usando env_vars definidos no machine/<nome>.yml
# ----------------------------------------------------------
- name: "Executar script de pré-deploy (init_parallel.sh) em {{ machine_name }}"
  become: true
  ansible.builtin.shell: |
    cd "{{ host_scripts_dir }}/{{ package_cfg.script }}"
    ./init_parallel.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ machine_cfg.env_vars | default({}) }}"
  register: predeploy_result
  ignore_errors: true

# ----------------------------------------------------------
# PROVA DEFINITIVA DE IDENTIDADE DO HOST + EVIDÊNCIAS NO LOG
# ----------------------------------------------------------
- name: "PROVA: criar marcador único no host alvo ({{ machine_name }})"
  become: true
  ansible.builtin.copy:
    dest: "{{ host_base_dir }}/.predeploy_probe_{{ deployment_ref }}_{{ machine_name }}_{{ ansible_date_time.epoch }}"
    mode: "0644"
    content: |
      machine={{ machine_name }}
      host={{ machine_cfg.host }}
      package={{ machine_cfg.package }}
      deployment_ref={{ deployment_ref }}
      ansible_epoch={{ ansible_date_time.epoch }}
  delegate_to: "{{ machine_name }}"
  register: probe_copy

- debug:
    var: probe_copy

- name: "PROVA: capturar identidade e listar evidências no host ({{ machine_name }})"
  become: true
  ansible.builtin.shell: |
    echo "=== HOST IDENTIDADE ==="
    echo "machine_name={{ machine_name }}"
    echo "machine_cfg.host={{ machine_cfg.host }}"
    echo "package={{ machine_cfg.package }}"
    echo "deployment_ref={{ deployment_ref }}"
    echo "--- hostname ---"
    hostname
    echo "--- ips ---"
    hostname -I || ip a
    echo
    echo "=== LISTAGEM BASE ==="
    ls -ld /opt /opt/SoftwareExpress "{{ host_base_dir }}" || true
    echo
    echo "=== LISTAGEM PACKAGE ==="
    ls -la "{{ host_pkg_dir }}" || true
    echo
    echo "=== LISTAGEM SCRIPTS ==="
    ls -la "{{ host_scripts_dir }}" || true
    ls -la "{{ host_scripts_dir }}/{{ package_cfg.script }}" || true
    echo
    echo "=== PROBES ==="
    ls -la "{{ host_base_dir }}/.predeploy_probe_*" || true
    echo
    echo "=== PARALLEL.TXT ==="
    cat "{{ host_scripts_dir }}/{{ package_cfg.script }}/parallel.txt" || echo "parallel.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: probe_ls

- debug:
    var: probe_ls.stdout_lines

# ----------------------------------------------------------
# Atualizar status para success ou failed
# ----------------------------------------------------------
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

- name: "Atualizar status para failed de {{ machine_name }}"
  ansible.builtin.copy:
    dest: "{{ status_file }}"
    content: |
      {
        "machine": "{{ machine_name }}",
        "host": "{{ machine_cfg.host }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "failed",
        "deployment_ref": "{{ deployment_ref }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "stdout": {{ predeploy_result.stdout | to_json }},
        "stderr": {{ predeploy_result.stderr | to_json }}
      }
  when: predeploy_result is failed
