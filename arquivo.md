-- pipeline

predeploy:
- git clone e entra na tag especifica como parametro
- exportar as variáveis em tempo de execução repo/machine/sitef-01.yml e repo/package/sitef-core-0.0.2-0.yml
-----
package.yml
env_vars:
  PACKAGE_VERSION: 'sitef-0.0.20'
-----
machine.yml
env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"
-----
- limpar pasta /opt/SoftwareExpress/sitef-pipeline/deploy/scripts
- jogar o package para /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package/
- fazer o download do nexus dos componentes /opt/SoftwareExpress/sitef-pipeline/deploy/(path do nexus especifico. ex: package/linux/sitef-core-0.0.1-0.rpm)
- copiar o script para /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/
- entrar na pasta do scripts e chamar o init_parallel.sh
- Atualizar ou criar arquivo de status no harness-file-store e subir o log tb

deploy:
- exportar as variáveis em tempo de execução repo/machine/sitef-01.yml e repo/package/sitef-core-0.0.2-0.yml
-----
package.yml
env_vars:
  PACKAGE_VERSION: 'sitef-0.0.20'
-----
machine.yml
env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"
-----
- entrar na pasta do scripts e chamar o init_deploy.sh  /opt/SoftwareExpress/sitef-pipeline/deploy/scripts
- Atualizar ou criar arquivo de status no harness-file-store e subir o log tb

rollback
- exportar as variáveis em tempo de execução repo/machine/sitef-01.yml e repo/package/sitef-core-0.0.2-0.yml
-----
package.yml
env_vars:
  PACKAGE_VERSION: 'sitef-0.0.20'
-----
machine.yml
env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"
-----
- limpar a pasta /opt/SoftwareExpress/sitef-pipeline/rollback/scripts
- jogar o package para /opt/SoftwareExpress/sitef-pipeline/rollback/scripts/package/
- baixar novamente dos components do nexus para /opt/SoftwareExpress/sitef-pipeline/rollback/
- copiar o script para /opt/SoftwareExpress/sitef-pipeline/rollback/scripts
- entrar na pasta do scripts e chamar o init_rollback.sh
- Atualizar ou criar arquivo de status no harness-file-store e subir o log tb

harness-file-store:
dev/DEV000000007/dev000000007-sitef-01-dev.json  -- se der erro na pipeline 01 ent coloca dev000000007:pre-deploy:error, se fazer o rerun da pipeline 01 e ir com sucesso então atualizada a tag de pre-deploy para ok e adiciona a tag dev000000007:deploy:pending e se der certo dev000000007:pre-deploy:ok e dev000000007:deploy:pending, pipeline 02 atualizo apenas o status do deploy dev000000007:deploy:ok se der problema ent dev000000007:deploy:error, se precisar de rollback ent adiciona a tag dev000000007:rollback:status(ok, error)

{
    "machine": "sitef-01",
    "host": "100.99.41.58",
    "package": "sitef-core-0.0.2-0",
    "rollback": "",
    "status": "success",
    "deployment_ref": "DEV000000007",
    "timestamp": "2025-12-08T18:15:52Z"
    "log_path": "dev/DEV000000007/dev000000007-sitef-01-dev.log"
}
dev/DEV000000007/dev000000007-sitef-01-dev.log

---- pipeline pre deploy ----
log
---- pipeline pre deploy ----

---- pipeline deploy -----
log
---- pipeline deploy -----

dev/DEV000000007/dev000000007-sitef-02-dev.json
dev/DEV000000007/dev000000007-sitef-02-dev.log

cat/CAT000000007/
prd/PRD000000007-(change_id)/sitef-01.json

----
segue oq tenho atualmente
#deploy_from_status.yml
# PIPELINE 2 - DEPLOY (PARTE PESADA)
# - Lê status/<TAG>/predeploy-*.json
# - Extrai máquinas aptas
# - Para cada máquina chama deploy_per_machine.yml
# - Cada máquina ganha um JSON em status/<TAG>/deploy-<machine>.json

- name: "Deploy a partir do status do predeploy"
  hosts: localhost
  connection: local
  gather_facts: true

  vars:
    deployment_ref: "{{ deployment_ref | default('DEV000000001') }}"
    repo_root: "{{ playbook_dir }}/.."
    status_dir: "{{ repo_root }}/status/{{ deployment_ref }}"

  tasks:
    - name: "Resolver defaults de pacote (sem recursão)"
      ansible.builtin.set_fact:
        package_name_eff: "{{ vars.get('package_name', 'sitef-core') }}"
        package_version_eff: "{{ vars.get('package_version', '0.0.2-0') }}"

    - name: "Mostrar variáveis de entrada"
      ansible.builtin.debug:
        msg:
          - "deployment_ref      = {{ deployment_ref }}"
          - "repo_root           = {{ repo_root }}"
          - "status_dir          = {{ status_dir }}"
          - "package_name_eff    = {{ package_name_eff }}"
          - "package_version_eff = {{ package_version_eff }}"

    - name: "Garantir que o diretório de status exista"
      ansible.builtin.stat:
        path: "{{ status_dir }}"
      register: status_dir_stat

    - name: "Falhar se status_dir não existir"
      ansible.builtin.fail:
        msg: "Diretório de status não encontrado: {{ status_dir }}. Rode o predeploy primeiro."
      when: not status_dir_stat.stat.exists

    - name: "Listar arquivos de predeploy da TAG"
      ansible.builtin.find:
        paths: "{{ status_dir }}"
        patterns: "predeploy-*.json"
        file_type: file
      register: predeploy_files

    - name: "Falhar se não houver predeploy-*.json"
      ansible.builtin.fail:
        msg: "Nenhum arquivo predeploy-*.json encontrado em {{ status_dir }}"
      when: predeploy_files.matched | int == 0

    - name: "Carregar JSONs de predeploy"
      ansible.builtin.slurp:
        src: "{{ item.path }}"
      loop: "{{ predeploy_files.files }}"
      register: predeploy_slurped

    - name: "Montar lista de predeploys parseados"
      ansible.builtin.set_fact:
        predeploy_jsons: "{{ predeploy_jsons | default([]) + [ (item.content | b64decode | from_json) ] }}"
      loop: "{{ predeploy_slurped.results }}"

    - name: "Inicializar lista de máquinas para deploy"
      ansible.builtin.set_fact:
        machines_to_deploy: []

    - name: "Montar lista de máquinas com predeploy success (machine ou machine_name)"
      ansible.builtin.set_fact:
        machines_to_deploy: "{{ machines_to_deploy + [ (item.machine_name | default(item.machine)) ] }}"
      loop: "{{ predeploy_jsons }}"
      when:
        - item.status is defined
        - item.status == "success"


    - name: "Falhar se nenhuma máquina estiver apta para deploy"
      ansible.builtin.fail:
        msg: "Nenhuma máquina com status=success no predeploy para a TAG {{ deployment_ref }}"
      when: machines_to_deploy | length == 0

    - name: "Exibir máquinas aptas para deploy"
      ansible.builtin.debug:
        var: machines_to_deploy

    - name: "Executar deploy por máquina"
      ansible.builtin.include_tasks: deploy_per_machine.yml
      loop: "{{ machines_to_deploy }}"
      loop_control:
        loop_var: target_machine
      vars:
        deployment_ref: "{{ deployment_ref }}"
        package_name: "{{ package_name_eff }}"
        package_version: "{{ package_version_eff }}"

#deploy_per_machine.yml
# deploy_per_machine.yml
#
# Responsável por executar o deploy em uma única máquina a partir
# da lista montada no deploy_from_status.yml.
#
# Espera receber:
# - target_machine (recomendado) OU machine_name
# - deployment_ref
# - repo_root
# - status_dir
#
# Saída:
# - status/<TAG>/deploy-<machine>.json

# -------------------------------------------------------------------
# Normalização do loop_var para evitar colisão de variável
# -------------------------------------------------------------------
- name: "Normalizar variável de máquina"
  ansible.builtin.set_fact:
    machine_name: "{{ target_machine | default(machine_name) }}"

- name: "Falhar se machine_name não foi informado"
  ansible.builtin.assert:
    that:
      - machine_name is defined
      - machine_name | length > 0
    fail_msg: "machine_name/target_machine não foi definido no include_tasks."

# -------------------------------------------------------------------
# Caminhos locais do repo e status
# -------------------------------------------------------------------
- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/deploy-{{ machine_name }}.json"
    host_base_dir: "/opt/SoftwareExpress/sitef"
    host_pkg_dir: "/opt/SoftwareExpress/sitef/package/linux"
    host_scripts_dir: "/opt/SoftwareExpress/sitef/scripts"

# -------------------------------------------------------------------
# Validar existência do arquivo da máquina
# -------------------------------------------------------------------
- name: "Validar arquivo da máquina {{ machine_name }}"
  ansible.builtin.stat:
    path: "{{ machine_file }}"
  register: machine_file_stat

- name: "Falhar se arquivo da máquina não existir"
  ansible.builtin.fail:
    msg: "Arquivo da máquina não encontrado: {{ machine_file }}"
  when: not machine_file_stat.stat.exists

# -------------------------------------------------------------------
# Carregar configs
# -------------------------------------------------------------------
- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Validar package definido para {{ machine_name }}"
  ansible.builtin.assert:
    that:
      - machine_cfg.package is defined
      - machine_cfg.package | length > 0
    fail_msg: "machine_cfg.package não definido em {{ machine_file }}"

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

- name: "Validar script definido no pacote {{ machine_cfg.package }}"
  ansible.builtin.assert:
    that:
      - package_cfg.script is defined
      - package_cfg.script | length > 0
    fail_msg: "package_cfg.script não definido em {{ repo_root }}/package/{{ machine_cfg.package }}.yml"

# -------------------------------------------------------------------
# Defaults de conexão
# -------------------------------------------------------------------
- name: "Definir usuário alvo padrão para {{ machine_name }}"
  ansible.builtin.set_fact:
    target_user: "{{ machine_cfg.user | default('ec2-user') }}"

- name: "Definir ssh_common_args padrão da máquina (se existir)"
  ansible.builtin.set_fact:
    target_ssh_common_args: "{{ machine_cfg.ssh_common_args | default('') }}"

- name: "Aplicar ProxyJump via bastion (quando necessário)"
  ansible.builtin.set_fact:
    target_ssh_common_args: "-o ProxyJump={{ bastion_user | default('ec2-user') }}@{{ bastion_host }}"
  when:
    - (target_ssh_common_args | length) == 0
    - bastion_host is defined

# -------------------------------------------------------------------
# Registrar host dinâmico
# -------------------------------------------------------------------
- name: "Registrar host dinâmico para {{ machine_name }}"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"

# -------------------------------------------------------------------
# Criar status inicial
# -------------------------------------------------------------------
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

# -------------------------------------------------------------------
# Opcional: validar se o predeploy ocorreu antes
# -------------------------------------------------------------------
- name: "Verificar se status de predeploy existe"
  ansible.builtin.stat:
    path: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
  register: predeploy_status_stat

- name: "Avisar se predeploy não foi encontrado (não bloqueante)"
  ansible.builtin.debug:
    msg: "ATENÇÃO: predeploy-{{ machine_name }}.json não encontrado. Deploy continuará mesmo assim."
  when: not predeploy_status_stat.stat.exists

# -------------------------------------------------------------------
# Garantir diretórios no host alvo
# -------------------------------------------------------------------
- name: "Garantir diretórios do deploy no host {{ machine_cfg.host }}"
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

# -------------------------------------------------------------------
# Executar init.sh
# -------------------------------------------------------------------
- name: "Executar script de deploy (init.sh) em {{ machine_name }}"
  become: true
  ansible.builtin.shell: |
    set -e
    cd "{{ host_scripts_dir }}/{{ package_cfg.script }}"
    ./init.sh
  delegate_to: "{{ machine_name }}"
  environment: "{{ machine_cfg.env_vars | default({}) }}"
  register: deploy_result
  ignore_errors: true

- name: "DEBUG - stdout do init.sh"
  ansible.builtin.debug:
    var: deploy_result.stdout_lines

- name: "DEBUG - stderr do init.sh"
  ansible.builtin.debug:
    var: deploy_result.stderr_lines

# -------------------------------------------------------------------
# Ler deploy.txt do host alvo (se existir)
# -------------------------------------------------------------------
- name: "Ler deploy.txt do host alvo"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_scripts_dir }}/{{ package_cfg.script }}/deploy.txt" 2>/dev/null || echo "deploy.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: deploy_log
  changed_when: false

- name: "DEBUG - conteúdo do deploy.txt"
  ansible.builtin.debug:
    var: deploy_log.stdout_lines

# -------------------------------------------------------------------
# Atualizar status final
# -------------------------------------------------------------------
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
        "rc": {{ deploy_result.rc | default(0) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "deploy_log": {{ deploy_log.stdout | default('') | to_json }}
      }
  when: deploy_result is succeeded

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
        "rc": {{ deploy_result.rc | default(1) }},
        "stdout": {{ deploy_result.stdout | default('') | to_json }},
        "stderr": {{ deploy_result.stderr | default('') | to_json }},
        "deploy_log": {{ deploy_log.stdout | default('') | to_json }}
      }
  when: deploy_result is failed

# -------------------------------------------------------------------
# Se quiser falhar o pipeline quando der erro:
# -------------------------------------------------------------------
- name: "Falhar pipeline se init.sh retornou erro"
  ansible.builtin.fail:
    msg: "Deploy falhou em {{ machine_name }} (host {{ machine_cfg.host }}). Verifique deploy.txt e status JSON."
  when: deploy_result is failed

#predeploy_from_execution.yml
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

    nexus_base_url: "https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"
    nexus_user: "AS4hZF20"
    nexus_password: "l7WwGfJd_Grmh-5Kn7B__U8nqgdNWh1XhrYtVQQ5I_6k"

  tasks:

    - name: "Mostrar variáveis de entrada"
      debug:
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
      debug:
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

#predeploy_per_machine.yml
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

#machine/sitef-01.yml
host: "100.99.41.58"

package: "sitef-core-0.0.2-0"
rollback: "sitef-core-0.0.2-0-rollback"

env_vars:
  SITEF_ENV: "prod"
  OUTRA_VAR: "valor"

tags:
  - name: sitef-2
  - environment: aws-dev
  - aws-account: 942632789850

#package/sitef-core-0.0.2-0.yml
script: deploy-sitef-0.0.2

components:
  - packages/linux/sitef-core-0.0.2-0.x86_64.rpm

env_vars:
  PACKAGE_VERSION: "sitef-core-0.0.2-0"

#package/sitef-core-0.0.2-0-rollback.yml
script: rollback-sitef-0.0.2

components:
  - packages/linux/sitef-core-0.0.1-0.x86_64.rpm

env_vars:
  PACKAGE_VERSION: "sitef-core-0.0.2-0"

#harness/pre-deploy-pipeline-script
#!/bin/bash
set -euo pipefail

# === Variáveis vindas do Harness ===
GIT_TOKEN="xXAiJyF1Hx4noamrBSdV"
GIT_TAG="${GIT_TAG}"                             # ex: DEV000000002
EXECUTION_FILE_NAME="execution/machine_list_dev.yml"  # ex: machine_list_dev.yml
GIT_BRANCH="develop"                                       # branch onde vai o commit

GIT_USER_NAME="${GIT_USER_NAME:-harness-bot}"
GIT_USER_EMAIL="${GIT_USER_EMAIL:-harness-bot@fiserv.com}"

echo "== 1) Clonando repositorio (branch ${GIT_BRANCH}) =="

REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"

WORKDIR="/tmp/elastic-compute-cloud-sitef"   # <= MESMO caminho que aparece no seu print
rm -rf "$WORKDIR" || true
git clone --branch "$GIT_BRANCH" "$REPO_URL" "$WORKDIR"

cd "$WORKDIR"
git config --global --add safe.directory "$WORKDIR"
git fetch --tags

echo "Repositorio em $(pwd)"
echo "HEAD em:"
git rev-parse --abbrev-ref HEAD
git rev-parse HEAD

echo
echo "== 2) Rodando predeploy via Ansible =="
echo "   TAG (deployment_ref): ${GIT_TAG}"
echo "   execution file      : ${EXECUTION_FILE_NAME}"

cd ansible

ansible-playbook predeploy_from_execution.yml \
  -e "execution_file_name=${EXECUTION_FILE_NAME}" \
  -e "deployment_ref=${GIT_TAG}" \
  --forks 10

cd ..

echo
echo "== 3) Verificando se houve alteracoes em status/${GIT_TAG} =="

if [ ! -d "status/${GIT_TAG}" ]; then
  echo "ERRO: status/${GIT_TAG} nao foi criado pelo playbook."
  echo "Verifique se o predeploy_from_execution.yml criou a pasta status/"
  exit 1
fi

git status status/"${GIT_TAG}"
CHANGES=$(git status --porcelain status/"${GIT_TAG}" || true)

if [ -z "$CHANGES" ]; then
  echo "Nenhuma mudanca em status/${GIT_TAG}. Nada para commitar."
  exit 0
fi

echo
echo "== 4) Fazendo commit e push das mudancas de status =="
git config user.name  "${GIT_USER_NAME}"
git config user.email "${GIT_USER_EMAIL}"

git add "status/${GIT_TAG}"
git commit -m "Predeploy status for ${GIT_TAG} (execution ${EXECUTION_FILE_NAME})"
git push origin "${GIT_BRANCH}"

echo "Commit de status enviado com sucesso para ${GIT_BRANCH}."

#harness/deploy-pipeline
#!/usr/bin/env bash
set -euo pipefail

BRANCH="${BRANCH:-develop}"
STRATEGY="${STRATEGY:-deploy}"      # deploy | rollback
GIT_TAG="${GIT_TAG:-}"
MACHINES="${MACHINES:-${MACHINE:-}}"
GIT_TOKEN="xXAiJyF1Hx4noamrBSdV"
REPO_URL="https://harness:${GIT_TOKEN}@gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/elastic-compute-cloud-sitef.git"


echo "== DEPLOY PIPELINE =="
echo "BRANCH   : ${BRANCH}"
echo "STRATEGY : ${STRATEGY}"
echo "GIT_TAG  : ${GIT_TAG}"
echo "MACHINES : ${MACHINES}"
echo

if [[ -z "${GIT_TAG}" ]]; then
  echo "ERRO: GIT_TAG nao informado"
  exit 1
fi

if [[ -z "${MACHINES}" ]]; then
  echo "ERRO: MACHINES/MACHINE vazio"
  exit 1
fi

# Normaliza lista:
# - aceita "sitef-01.yml", "sitef-01.yaml"
# - aceita separado por espaço ou vírgula
MACHINES_CLEAN=""
for m in ${MACHINES//,/ }; do
  m="${m%.yml}"
  m="${m%.yaml}"
  MACHINES_CLEAN+="${m} "
done

echo "MACHINES_NORMALIZADAS: ${MACHINES_CLEAN}"
echo

WORKDIR="$(mktemp -d)"
echo "Clonando repo em ${WORKDIR}..."
git clone -b "${BRANCH}" "${REPO_URL}" "${WORKDIR}/elastic-compute-cloud-sitef"
cd "${WORKDIR}/elastic-compute-cloud-sitef"

for m in ${MACHINES_CLEAN}; do
  echo "===================================================="
  echo "== ${STRATEGY^^} -> ${m}"
  echo "===================================================="

  ansible-playbook \
    -i localhost, \
    ansible/deploy_from_status.yml \
    -e "machine_name=${m}" \
    -e "deployment_ref=${GIT_TAG}" \
    -e "deploy_action=${STRATEGY}"
done

echo
echo "== Pipeline finalizada com sucesso =="
