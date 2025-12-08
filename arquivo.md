---
# ansible/predeploy_from_execution.yml
#
# PIPELINE 1 - PREDEPLOY (PARTE LEVE)
# - Lê execution/<arquivo>.yml (lista de máquinas)
# - Para cada máquina chama predeploy_per_machine.yml (em loop)
# - Cada máquina ganha um JSON em status/<TAG>/predeploy-<machine>.json

- name: "Predeploy a partir do arquivo de execução"
  hosts: localhost
  connection: local
  gather_facts: true   # preciso de ansible_date_time p/ timestamp

  vars:
    # Vêm da pipeline via -e
    execution_file_name: "{{ execution_file_name | default('execution/machine_list_dev.yml') }}"
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"

    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

    # Nexus
    nexus_base_url: "https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"
    nexus_user: "{{ nexus_user | default('AS4hZF20') }}"
    nexus_password: "{{ nexus_password | default('CHANGE_ME') }}"

  tasks:

    - name: "Mostrar variáveis de entrada"
      ansible.builtin.debug:
        msg:
          - "execution_file_name = {{ execution_file_name }}"
          - "deployment_ref     = {{ deployment_ref }}"
          - "repo_root          = {{ repo_root }}"
          - "status_dir         = {{ status_dir }}"

    - name: "Criar diretório de status da TAG"
      ansible.builtin.file:
        path: "{{ status_dir }}"
        state: directory
        mode: "0755"

    # Carrega o arquivo de execução
    # execution/machine_list_dev.yml
    # machines:
    #   - sitef-01
    #   - sitef-02
    - name: "Carregar arquivo de execução"
      ansible.builtin.include_vars:
        file: "{{ repo_root }}/{{ execution_file_name }}"
        name: execution_cfg

    - name: "Exibir lista de máquinas"
      ansible.builtin.debug:
        var: execution_cfg.machines

    - name: "Falhar se não tiver máquinas no arquivo de execução"
      ansible.builtin.fail:
        msg: "Nenhuma máquina encontrada em execution_cfg.machines"
      when: >
        execution_cfg.machines is not defined or
        execution_cfg.machines | length == 0

    # ------------------------------------------------------------------
    # Loop chamando o arquivo de tarefas por máquina
    # ------------------------------------------------------------------
    - name: "Executar pré-deploy por máquina"
      ansible.builtin.include_tasks: predeploy_per_machine.yml
      loop: "{{ execution_cfg.machines }}"
      loop_control:
        loop_var: machine_name
      vars:
        nexus_base_url: "{{ nexus_base_url }}"
        nexus_user: "{{ nexus_user }}"
        nexus_password: "{{ nexus_password }}"


---
# ansible/predeploy_per_machine.yml
#
# Tarefas executadas para CADA máquina no pré-deploy.
# Variáveis de contexto (vêm do loop do play principal):
#   - machine_name
#   - repo_root
#   - status_dir
#   - deployment_ref
#
# Ajustado para garantir execução no HOST ALVO e log detalhado.

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
- name: "Definir usuário alvo padrão para {{ machine_name }}"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default('ec2-user') }}"

- name: "Definir ssh_common_args padrão da máquina (se existir)"
  ansible.builtin.set_fact:
    target_ssh_common_args: "{{ machine_cfg.ssh_common_args | default('') }}"

# Só aplica ProxyJump se:
#  - a máquina NÃO definiu ssh_common_args
#  - bastion_host foi definido em vars do play/pipeline
- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    target_ssh_common_args: "-o ProxyJump={{ bastion_user | default('ec2-user') }}@{{ bastion_host }}"
  when:
    - (target_ssh_common_args | length) == 0
    - bastion_host is defined

# ----------------------------------------------------------
# Registrar host dinâmico para GARANTIR conexão SSH real
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

- name: "DEBUG - diretórios no host alvo"
  ansible.builtin.debug:
    var: dirs_stat

# ----------------------------------------------------------
# Baixar RPM(s) do Nexus na MÁQUINA ALVO
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
  no_log: true   # evita vazar credenciais

- name: "DEBUG - resultado do download (sem segredos)"
  ansible.builtin.debug:
    msg:
      - "Baixados {{ package_cfg.components | length }} componente(s) para {{ machine_name }}"
      - "Destino: {{ host_pkg_dir }}"

# Verificar se o RPM realmente apareceu na máquina alvo
- name: "Verificar se RPM foi criado em {{ machine_cfg.host }}"
  become: true
  ansible.builtin.stat:
    path: "{{ host_pkg_dir }}/{{ (package_cfg.components[0] | basename) }}"
  delegate_to: "{{ machine_name }}"
  register: rpm_stat

- name: "DEBUG - stat do RPM no host alvo"
  ansible.builtin.debug:
    var: rpm_stat

- name: "Falhar se RPM não existir em {{ host_pkg_dir }} na máquina {{ machine_cfg.host }}"
  ansible.builtin.fail:
    msg: "RPM {{ package_cfg.components[0] | basename }} não encontrado em {{ host_pkg_dir }} na máquina {{ machine_cfg.host }}"
  when: not rpm_stat.stat.exists

# ----------------------------------------------------------
# Copiar scripts do package para a máquina alvo
# ----------------------------------------------------------
- name: "Copiar scripts do package {{ machine_cfg.package }} para {{ machine_name }}"
  become: true
  ansible.builtin.copy:
    src: "{{ repo_root }}/scripts/{{ package_cfg.script }}/"
    dest: "{{ host_scripts_dir }}/{{ package_cfg.script }}/"
    mode: "0755"
  delegate_to: "{{ machine_name }}"
  register: copy_scripts_result

- name: "DEBUG - copy scripts"
  ansible.builtin.debug:
    var: copy_scripts_result

# Verificar se o init_parallel.sh está na máquina alvo
- name: "Verificar se init_parallel.sh existe na máquina alvo"
  become: true
  ansible.builtin.stat:
    path: "{{ host_scripts_dir }}/{{ package_cfg.script }}/init_parallel.sh"
  delegate_to: "{{ machine_name }}"
  register: init_stat

- name: "DEBUG - stat init_parallel.sh"
  ansible.builtin.debug:
    var: init_stat

- name: "Falhar se init_parallel.sh não existir na máquina {{ machine_cfg.host }}"
  ansible.builtin.fail:
    msg: "init_parallel.sh não encontrado em {{ host_scripts_dir }}/{{ package_cfg.script }} na máquina {{ machine_cfg.host }}"
  when: not init_stat.stat.exists

# ----------------------------------------------------------
# Executar o init_parallel.sh NA MÁQUINA ALVO
# ----------------------------------------------------------
- name: "Executar script de pré-deploy (init_parallel.sh) em {{ machine_name }}"
  become: true
  ansible.builtin.shell: |
    cd "{{ host_scripts_dir }}/{{ package_cfg.script }}"
    /bin/bash -x ./init_parallel.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ machine_cfg.env_vars | default({}) }}"
  register: predeploy_result
  ignore_errors: true

- name: "DEBUG - stdout do init_parallel.sh"
  ansible.builtin.debug:
    var: predeploy_result.stdout_lines

- name: "DEBUG - stderr do init_parallel.sh"
  ansible.builtin.debug:
    var: predeploy_result.stderr_lines

# ----------------------------------------------------------
# Ler parallel.txt para log da pipeline
# ----------------------------------------------------------
- name: "Ler parallel.txt do host alvo"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_scripts_dir }}/{{ package_cfg.script }}/parallel.txt" || true
  delegate_to: "{{ machine_name }}"
  register: parallel_log

- name: "DEBUG - conteúdo do parallel.txt"
  ansible.builtin.debug:
    var: parallel_log.stdout_lines

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

- name: "DEBUG - probe criado"
  ansible.builtin.debug:
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

- name: "DEBUG - prova de evidências"
  ansible.builtin.debug:
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
  when: predeploy_result.rc == 0

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
  when: predeploy_result.rc != 0

# Opcional: falha explícita do job se o script falhar
- name: "Falhar pipeline se init_parallel.sh retornou erro"
  ansible.builtin.fail:
    msg: |
      init_parallel.sh falhou para {{ machine_name }} (host {{ machine_cfg.host }})
      RC={{ predeploy_result.rc }}
      STDERR:
      {{ predeploy_result.stderr }}
  when: predeploy_result.rc != 0