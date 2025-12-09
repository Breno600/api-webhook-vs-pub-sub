sh-5.1$ sudo ls -Ral /opt/SoftwareExpress/

/opt/SoftwareExpress/:

total 0

drwxr-xr-x.  2 root root   6 Dec  9 17:42 .

drwxr-xr-x. 10 root root 134 Dec  9 10:39 ..

sh-5.1$

 ---
# =====================================================================================
# PREDEPLOY POR MÁQUINA
# Chamado por predeploy_from_execution.yml
#
# Responsabilidades:
# - Carregar machine + package
# - Mesclar env_vars (machine + package)
# - Preparar diretórios:
#     /opt/SoftwareExpress/sitef-pipeline/deploy
#     /opt/SoftwareExpress/sitef-pipeline/deploy/scripts
#     /opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package
# - Copiar package.yml para /deploy/scripts/package/
# - Baixar components do Nexus para /deploy/<path do componente>
#   (ex: /deploy/package/linux/sitef-core-0.0.2-0.rpm)
# - Copiar scripts do pacote para /deploy/scripts
# - Executar init_parallel.sh na máquina alvo com as env_vars
# - Gravar status.json local com status + log_path
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

# -----------------------------------------------------------------------------
# 2) Definir paths base do repositório
# -----------------------------------------------------------------------------
- name: "Definir paths base do repositório"
  ansible.builtin.set_fact:
    repo_root_safe: >-
      {{
        repo_root
          | default(repo_root_resolved | default(playbook_dir ~ '/..'))
      }}

# -----------------------------------------------------------------------------
# 3) Definir diretórios principais do repositório
# -----------------------------------------------------------------------------
- name: "Definir execution_dir do repositório"
  ansible.builtin.set_fact:
    execution_dir: "{{ repo_root_safe }}/execution"

- name: "Definir machines_dir, packages_dir e scripts_dir do repositório"
  ansible.builtin.set_fact:
    machines_dir: "{{ repo_root_safe }}/machines"
    packages_dir: "{{ repo_root_safe }}/packages"
    scripts_root_dir: "{{ repo_root_safe }}/scripts"

# -----------------------------------------------------------------------------
# 4) Candidatos de arquivo de máquina
# -----------------------------------------------------------------------------
- name: "Definir candidatos de arquivo da máquina"
  ansible.builtin.set_fact:
    candidate_machine_files:
      - "{{ execution_dir }}/machines/{{ current_machine }}.yml"
      - "{{ execution_dir }}/{{ current_machine }}.yml"
      - "{{ machines_dir }}/{{ current_machine }}.yml"
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
# 11) Validações mínimas do pacote
# -----------------------------------------------------------------------------
- name: "Validar components do pacote"
  ansible.builtin.assert:
    that:
      - package_cfg is defined
      - package_cfg.components is defined
      - package_cfg.components | length > 0
    fail_msg: "components ausente/vazio no pacote {{ machine_cfg.package }}"

# -----------------------------------------------------------------------------
# 12) Calcular env_vars mescladas (machine + package + extras)
# -----------------------------------------------------------------------------
- name: "Calcular env_vars mescladas"
  ansible.builtin.set_fact:
    merged_env_vars: >-
      {{
        (machine_cfg.env_vars | default({}))
        | combine(package_cfg.env_vars | default({}), recursive=True)
        | combine({
            'SITEF_MACHINE': current_machine,
            'SITEF_HOST': machine_cfg.host | default(machine_cfg.ip | default('')),
            'DEPLOYMENT_REF': deployment_ref | default(''),
            'PACKAGE_NAME': machine_cfg.package,
            'ROLLBACK_PACKAGE': machine_cfg.rollback | default('')
          }, recursive=True)
      }}

# -----------------------------------------------------------------------------
# 13) Usuário/SSH defaults
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
    - (machine_cfg.bastion_host | string | length) > 0

# -----------------------------------------------------------------------------
# 14) Registrar host dinâmico
# -----------------------------------------------------------------------------
- name: "Registrar host dinâmico para {{ current_machine }}"
  ansible.builtin.add_host:
    name: "{{ current_machine }}"
    ansible_host: "{{ machine_cfg.host | default(machine_cfg.ip | default(machine_cfg.host | default(''))) }}"
    ansible_user: "{{ target_user }}"
    ansible_ssh_common_args: "{{ ssh_common_args }}"
  changed_when: true

# -----------------------------------------------------------------------------
# 15) Definir caminhos de status e deploy (BASE = /opt/.../deploy)
# -----------------------------------------------------------------------------
- name: "Definir caminhos de status e deploy"
  ansible.builtin.set_fact:
    machine_status_dir: "{{ status_dir }}/{{ current_machine }}"
    machine_status_file: "{{ status_dir }}/{{ current_machine }}/status.json"

    deploy_base_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/components"
    deploy_scripts_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts"
    deploy_scripts_package_dir: "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package"

# -----------------------------------------------------------------------------
# 16) Calcular caminhos de status/log "lógicos"
# -----------------------------------------------------------------------------
- name: "Calcular caminhos de status/log"
  ansible.builtin.set_fact:
    filestore_log_path: >-
      {{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.log
    filestore_status_path: >-
      {{ filestore_base_dir }}/{{ deployment_ref | lower }}-{{ current_machine }}-{{ filestore_env }}.json

# -----------------------------------------------------------------------------
# 17) Criar diretório de status local e registrar status 'pre-deploy:queued'
# -----------------------------------------------------------------------------
- name: "Criar diretório de status da máquina"
  ansible.builtin.file:
    path: "{{ machine_status_dir }}"
    state: directory
    mode: "0755"

- name: "Escrever status inicial (pre-deploy:queued)"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "pre-deploy:queued",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 18) Garantir base do pipeline no host remoto
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
    - "{{ deploy_base_dir }}/package"
    - "{{ deploy_scripts_dir }}"
    - "{{ deploy_scripts_package_dir }}"

# -----------------------------------------------------------------------------
# 19) Limpar pasta de scripts do pacote
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
# 20) Copiar package.yml para /deploy/scripts/package/
# -----------------------------------------------------------------------------
- name: "Copiar package.yml para o host"
  ansible.builtin.copy:
    src: "{{ packages_dir }}/{{ machine_cfg.package }}.yml"
    dest: "{{ deploy_scripts_package_dir }}/{{ machine_cfg.package }}.yml"
    mode: "0644"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 21) Garantir diretórios dos componentes + baixar do Nexus
#      Ex: components = [ "package/linux/sitef-core-0.0.2-0.rpm", ... ]
#      Resultado: /opt/.../deploy/package/linux/sitef-core-0.0.2-0.rpm
# -----------------------------------------------------------------------------
- name: "Garantir diretórios dos componentes no host"
  ansible.builtin.file:
    path: "{{ deploy_base_dir }}/{{ item | dirname }}"
    state: directory
    mode: "0755"
  loop: "{{ package_cfg.components }}"
  become: true
  delegate_to: "{{ current_machine }}"

- name: "Baixar componentes do Nexus"
  ansible.builtin.get_url:
    url: "{{ nexus_base_url.rstrip('/') }}/{{ item }}"
    dest: "{{ deploy_base_dir }}/{{ item }}"
    mode: "0644"
    url_username: "{{ nexus_user | default(omit) }}"
    url_password: "{{ nexus_password | default(omit) }}"
  loop: "{{ package_cfg.components }}"
  become: true
  delegate_to: "{{ current_machine }}"

# -----------------------------------------------------------------------------
# 22) Copiar scripts do pacote para /deploy/scripts
#      src:  repo_root/scripts/<package_cfg.script>/*
#      dest: /opt/.../deploy/scripts/
# -----------------------------------------------------------------------------
- name: "Copiar scripts do pacote para o host"
  ansible.builtin.copy:
    src: "{{ scripts_root_dir }}/{{ package_cfg.script }}/"
    dest: "{{ deploy_scripts_dir }}/"
    mode: "0755"
  become: true
  delegate_to: "{{ current_machine }}"

# (Opcional, só pra debug enquanto ajusta)
- name: "Listar conteúdo de /deploy/scripts (debug)"
  ansible.builtin.shell: |
    ls -Ral "{{ deploy_scripts_dir }}" || echo "diretório vazio"
  become: true
  delegate_to: "{{ current_machine }}"
  register: scripts_ls
  changed_when: false

- name: "Mostrar conteúdo de scripts (debug)"
  ansible.builtin.debug:
    var: scripts_ls.stdout_lines

# -----------------------------------------------------------------------------
# 23) Executar init_parallel.sh na máquina alvo
# -----------------------------------------------------------------------------
- name: "Executar init_parallel.sh no host"
  ansible.builtin.shell: |
    cd "{{ deploy_scripts_dir }}"
    ./init_parallel.sh
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  become: true
  delegate_to: "{{ current_machine }}"
  register: init_parallel_result
  changed_when: true

# -----------------------------------------------------------------------------
# 24) Atualizar status para pre-deploy:ok ou pre-deploy:error
# -----------------------------------------------------------------------------
- name: "Atualizar status após execução do init_parallel.sh"
  ansible.builtin.copy:
    dest: "{{ machine_status_file }}"
    mode: "0644"
    content: |
      {
        "machine": "{{ current_machine }}",
        "host": "{{ machine_cfg.host | default(machine_cfg.ip | default('')) }}",
        "package": "{{ machine_cfg.package }}",
        "rollback": "{{ machine_cfg.rollback | default('') }}",
        "status": "{{ 'pre-deploy:ok' if init_parallel_result.rc == 0 else 'pre-deploy:error' }}",
        "deployment_ref": "{{ deployment_ref | default('') }}",
        "timestamp": "{{ ansible_date_time.iso8601 }}",
        "log_path": "{{ filestore_log_path }}"
      }
  changed_when: true

# -----------------------------------------------------------------------------
# 25) Falhar play se init_parallel.sh der erro
# -----------------------------------------------------------------------------
- name: "Falhar se init_parallel.sh retornou erro"
  ansible.builtin.fail:
    msg: "init_parallel.sh falhou na máquina {{ current_machine }} com rc={{ init_parallel_result.rc }}"
  when: init_parallel_result.rc != 0


# LOGS:
Exec using JSCH
Connecting to 10.218.238.144 ....
Connection to 10.218.238.144 established
Executing command ...
export GIT_TAG=DEV000000001
Clonando repo em /tmp/tmp.0JkWY2d8Gi...
Cloning into '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef'...
remote: Enumerating objects: 663, done.
remote: Counting objects:   0% (1/613)
remote: Counting objects:   1% (7/613)
remote: Counting objects:   2% (13/613)
remote: Counting objects:   3% (19/613)
remote: Counting objects:   4% (25/613)
remote: Counting objects:   5% (31/613)
remote: Counting objects:   6% (37/613)
remote: Counting objects:   7% (43/613)
remote: Counting objects:   8% (50/613)
remote: Counting objects:   9% (56/613)
remote: Counting objects:  10% (62/613)
remote: Counting objects:  11% (68/613)
remote: Counting objects:  12% (74/613)
remote: Counting objects:  13% (80/613)
remote: Counting objects:  14% (86/613)
remote: Counting objects:  15% (92/613)
remote: Counting objects:  16% (99/613)
remote: Counting objects:  17% (105/613)
remote: Counting objects:  18% (111/613)
remote: Counting objects:  19% (117/613)
remote: Counting objects:  20% (123/613)
remote: Counting objects:  21% (129/613)
remote: Counting objects:  22% (135/613)
remote: Counting objects:  23% (141/613)
remote: Counting objects:  24% (148/613)
remote: Counting objects:  25% (154/613)
remote: Counting objects:  26% (160/613)
remote: Counting objects:  27% (166/613)
remote: Counting objects:  28% (172/613)
remote: Counting objects:  29% (178/613)
remote: Counting objects:  30% (184/613)
remote: Counting objects:  31% (191/613)
remote: Counting objects:  32% (197/613)
remote: Counting objects:  33% (203/613)
remote: Counting objects:  34% (209/613)
remote: Counting objects:  35% (215/613)
remote: Counting objects:  36% (221/613)
remote: Counting objects:  37% (227/613)
remote: Counting objects:  38% (233/613)
remote: Counting objects:  39% (240/613)
remote: Counting objects:  40% (246/613)
remote: Counting objects:  41% (252/613)
remote: Counting objects:  42% (258/613)
remote: Counting objects:  43% (264/613)
remote: Counting objects:  44% (270/613)
remote: Counting objects:  45% (276/613)
remote: Counting objects:  46% (282/613)
remote: Counting objects:  47% (289/613)
remote: Counting objects:  48% (295/613)
remote: Counting objects:  49% (301/613)
remote: Counting objects:  50% (307/613)
remote: Counting objects:  51% (313/613)
remote: Counting objects:  52% (319/613)
remote: Counting objects:  53% (325/613)
remote: Counting objects:  54% (332/613)
remote: Counting objects:  55% (338/613)
remote: Counting objects:  56% (344/613)
remote: Counting objects:  57% (350/613)
remote: Counting objects:  58% (356/613)
remote: Counting objects:  59% (362/613)
remote: Counting objects:  60% (368/613)
remote: Counting objects:  61% (374/613)
remote: Counting objects:  62% (381/613)
remote: Counting objects:  63% (387/613)
remote: Counting objects:  64% (393/613)
remote: Counting objects:  65% (399/613)
remote: Counting objects:  66% (405/613)
remote: Counting objects:  67% (411/613)
remote: Counting objects:  68% (417/613)
remote: Counting objects:  69% (423/613)
remote: Counting objects:  70% (430/613)
remote: Counting objects:  71% (436/613)
remote: Counting objects:  72% (442/613)
remote: Counting objects:  73% (448/613)
remote: Counting objects:  74% (454/613)
remote: Counting objects:  75% (460/613)
remote: Counting objects:  76% (466/613)
remote: Counting objects:  77% (473/613)
remote: Counting objects:  78% (479/613)
remote: Counting objects:  79% (485/613)
remote: Counting objects:  80% (491/613)
remote: Counting objects:  81% (497/613)
remote: Counting objects:  82% (503/613)
remote: Counting objects:  83% (509/613)
remote: Counting objects:  84% (515/613)
remote: Counting objects:  85% (522/613)
remote: Counting objects:  86% (528/613)
remote: Counting objects:  87% (534/613)
remote: Counting objects:  88% (540/613)
remote: Counting objects:  89% (546/613)
remote: Counting objects:  90% (552/613)
remote: Counting objects:  91% (558/613)
remote: Counting objects:  92% (564/613)
remote: Counting objects:  93% (571/613)
remote: Counting objects:  94% (577/613)
remote: Counting objects:  95% (583/613)
remote: Counting objects:  96% (589/613)
remote: Counting objects:  97% (595/613)
remote: Counting objects:  98% (601/613)
remote: Counting objects:  99% (607/613)
remote: Counting objects: 100% (613/613)
remote: Counting objects: 100% (613/613), done.
remote: Compressing objects:   0% (1/600)
remote: Compressing objects:   1% (6/600)
remote: Compressing objects:   2% (12/600)
remote: Compressing objects:   3% (18/600)
remote: Compressing objects:   4% (24/600)
remote: Compressing objects:   5% (30/600)
remote: Compressing objects:   6% (36/600)
remote: Compressing objects:   7% (42/600)
remote: Compressing objects:   8% (48/600)
remote: Compressing objects:   9% (54/600)
remote: Compressing objects:  10% (60/600)
remote: Compressing objects:  11% (66/600)
remote: Compressing objects:  12% (72/600)
remote: Compressing objects:  13% (78/600)
remote: Compressing objects:  14% (84/600)
remote: Compressing objects:  15% (90/600)
remote: Compressing objects:  16% (96/600)
remote: Compressing objects:  17% (102/600)
remote: Compressing objects:  18% (108/600)
remote: Compressing objects:  19% (114/600)
remote: Compressing objects:  20% (120/600)
remote: Compressing objects:  21% (126/600)
remote: Compressing objects:  22% (132/600)
remote: Compressing objects:  23% (138/600)
remote: Compressing objects:  24% (144/600)
remote: Compressing objects:  25% (150/600)
remote: Compressing objects:  26% (156/600)
remote: Compressing objects:  27% (162/600)
remote: Compressing objects:  28% (168/600)
remote: Compressing objects:  29% (174/600)
remote: Compressing objects:  30% (180/600)
remote: Compressing objects:  31% (186/600)
remote: Compressing objects:  32% (192/600)
remote: Compressing objects:  33% (198/600)
remote: Compressing objects:  34% (204/600)
remote: Compressing objects:  35% (210/600)
remote: Compressing objects:  36% (216/600)
remote: Compressing objects:  37% (222/600)
remote: Compressing objects:  38% (228/600)
remote: Compressing objects:  39% (234/600)
remote: Compressing objects:  40% (240/600)
remote: Compressing objects:  41% (246/600)
remote: Compressing objects:  42% (252/600)
remote: Compressing objects:  43% (258/600)
remote: Compressing objects:  44% (264/600)
remote: Compressing objects:  45% (270/600)
remote: Compressing objects:  46% (276/600)
remote: Compressing objects:  47% (282/600)
remote: Compressing objects:  48% (288/600)
remote: Compressing objects:  49% (294/600)
remote: Compressing objects:  50% (300/600)
remote: Compressing objects:  51% (306/600)
remote: Compressing objects:  52% (312/600)
remote: Compressing objects:  53% (318/600)
remote: Compressing objects:  54% (324/600)
remote: Compressing objects:  55% (330/600)
remote: Compressing objects:  56% (336/600)
remote: Compressing objects:  57% (342/600)
remote: Compressing objects:  58% (348/600)
remote: Compressing objects:  59% (354/600)
remote: Compressing objects:  60% (360/600)
remote: Compressing objects:  61% (366/600)
remote: Compressing objects:  62% (372/600)
remote: Compressing objects:  63% (378/600)
remote: Compressing objects:  64% (384/600)
remote: Compressing objects:  65% (390/600)
remote: Compressing objects:  66% (396/600)
remote: Compressing objects:  67% (402/600)
remote: Compressing objects:  68% (408/600)
remote: Compressing objects:  69% (414/600)
remote: Compressing objects:  70% (420/600)
remote: Compressing objects:  71% (426/600)
remote: Compressing objects:  72% (432/600)
remote: Compressing objects:  73% (438/600)
remote: Compressing objects:  74% (444/600)
remote: Compressing objects:  75% (450/600)
remote: Compressing objects:  76% (456/600)
remote: Compressing objects:  77% (462/600)
remote: Compressing objects:  78% (468/600)
remote: Compressing objects:  79% (474/600)
remote: Compressing objects:  80% (480/600)
remote: Compressing objects:  81% (486/600)
remote: Compressing objects:  82% (492/600)
remote: Compressing objects:  83% (498/600)
remote: Compressing objects:  84% (504/600)
remote: Compressing objects:  85% (510/600)
remote: Compressing objects:  86% (516/600)
remote: Compressing objects:  87% (522/600)
remote: Compressing objects:  88% (528/600)
remote: Compressing objects:  89% (534/600)
remote: Compressing objects:  90% (540/600)
remote: Compressing objects:  91% (546/600)
remote: Compressing objects:  92% (552/600)
remote: Compressing objects:  93% (558/600)
remote: Compressing objects:  94% (564/600)
remote: Compressing objects:  95% (570/600)
remote: Compressing objects:  96% (576/600)
remote: Compressing objects:  97% (582/600)
remote: Compressing objects:  98% (588/600)
remote: Compressing objects:  99% (594/600)
remote: Compressing objects: 100% (600/600)
remote: Compressing objects: 100% (600/600), done.
remote: Total 663 (delta 395), reused 0 (delta 0), pack-reused 50
Receiving objects:   0% (1/663)
Receiving objects:   1% (7/663)
Receiving objects:   2% (14/663)
Receiving objects:   3% (20/663)
Receiving objects:   4% (27/663)
Receiving objects:   5% (34/663)
Receiving objects:   6% (40/663)
Receiving objects:   7% (47/663)
Receiving objects:   8% (54/663)
Receiving objects:   9% (60/663)
Receiving objects:  10% (67/663)
Receiving objects:  11% (73/663)
Receiving objects:  12% (80/663)
Receiving objects:  13% (87/663)
Receiving objects:  14% (93/663)
Receiving objects:  15% (100/663)
Receiving objects:  16% (107/663)
Receiving objects:  17% (113/663)
Receiving objects:  18% (120/663)
Receiving objects:  19% (126/663)
Receiving objects:  20% (133/663)
Receiving objects:  21% (140/663)
Receiving objects:  22% (146/663)
Receiving objects:  23% (153/663)
Receiving objects:  24% (160/663)
Receiving objects:  25% (166/663)
Receiving objects:  26% (173/663)
Receiving objects:  27% (180/663)
Receiving objects:  28% (186/663)
Receiving objects:  29% (193/663)
Receiving objects:  30% (199/663)
Receiving objects:  31% (206/663)
Receiving objects:  32% (213/663)
Receiving objects:  33% (219/663)
Receiving objects:  34% (226/663)
Receiving objects:  35% (233/663)
Receiving objects:  36% (239/663)
Receiving objects:  37% (246/663)
Receiving objects:  38% (252/663)
Receiving objects:  39% (259/663)
Receiving objects:  40% (266/663)
Receiving objects:  41% (272/663)
Receiving objects:  42% (279/663)
Receiving objects:  43% (286/663)
Receiving objects:  44% (292/663)
Receiving objects:  45% (299/663)
Receiving objects:  46% (305/663)
Receiving objects:  47% (312/663)
Receiving objects:  48% (319/663)
Receiving objects:  49% (325/663)
Receiving objects:  50% (332/663)
Receiving objects:  51% (339/663)
Receiving objects:  52% (345/663)
Receiving objects:  53% (352/663)
Receiving objects:  54% (359/663)
Receiving objects:  55% (365/663)
Receiving objects:  56% (372/663)
Receiving objects:  57% (378/663)
Receiving objects:  58% (385/663)
Receiving objects:  59% (392/663)
Receiving objects:  60% (398/663)
Receiving objects:  61% (405/663)
Receiving objects:  62% (412/663)
Receiving objects:  63% (418/663)
Receiving objects:  64% (425/663)
Receiving objects:  65% (431/663)
Receiving objects:  66% (438/663)
Receiving objects:  67% (445/663)
Receiving objects:  68% (451/663)
Receiving objects:  69% (458/663)
Receiving objects:  70% (465/663)
Receiving objects:  71% (471/663)
Receiving objects:  72% (478/663)
Receiving objects:  73% (484/663)
Receiving objects:  74% (491/663)
Receiving objects:  75% (498/663)
Receiving objects:  76% (504/663)
Receiving objects:  77% (511/663)
Receiving objects:  78% (518/663)
Receiving objects:  79% (524/663)
Receiving objects:  80% (531/663)
Receiving objects:  81% (538/663)
Receiving objects:  82% (544/663)
Receiving objects:  83% (551/663)
Receiving objects:  84% (557/663)
Receiving objects:  85% (564/663)
Receiving objects:  86% (571/663)
Receiving objects:  87% (577/663)
Receiving objects:  88% (584/663)
Receiving objects:  89% (591/663)
Receiving objects:  90% (597/663)
Receiving objects:  91% (604/663)
Receiving objects:  92% (610/663)
Receiving objects:  93% (617/663)
Receiving objects:  94% (624/663)
Receiving objects:  95% (630/663)
Receiving objects:  96% (637/663)
Receiving objects:  97% (644/663)
Receiving objects:  98% (650/663)
Receiving objects:  99% (657/663)
Receiving objects: 100% (663/663)
Receiving objects: 100% (663/663), 110.52 KiB | 4.42 MiB/s, done.
Resolving deltas:   0% (0/414)
Resolving deltas:   1% (5/414)
Resolving deltas:   2% (9/414)
Resolving deltas:   3% (13/414)
Resolving deltas:   4% (17/414)
Resolving deltas:   5% (21/414)
Resolving deltas:   6% (25/414)
Resolving deltas:   7% (29/414)
Resolving deltas:   8% (34/414)
Resolving deltas:   9% (38/414)
Resolving deltas:  10% (42/414)
Resolving deltas:  11% (46/414)
Resolving deltas:  12% (50/414)
Resolving deltas:  13% (54/414)
Resolving deltas:  14% (58/414)
Resolving deltas:  15% (63/414)
Resolving deltas:  16% (67/414)
Resolving deltas:  17% (71/414)
Resolving deltas:  18% (75/414)
Resolving deltas:  19% (79/414)
Resolving deltas:  20% (83/414)
Resolving deltas:  21% (87/414)
Resolving deltas:  22% (92/414)
Resolving deltas:  23% (96/414)
Resolving deltas:  24% (100/414)
Resolving deltas:  25% (104/414)
Resolving deltas:  26% (108/414)
Resolving deltas:  27% (112/414)
Resolving deltas:  28% (116/414)
Resolving deltas:  29% (121/414)
Resolving deltas:  30% (125/414)
Resolving deltas:  31% (129/414)
Resolving deltas:  32% (133/414)
Resolving deltas:  33% (137/414)
Resolving deltas:  34% (141/414)
Resolving deltas:  35% (145/414)
Resolving deltas:  36% (150/414)
Resolving deltas:  37% (154/414)
Resolving deltas:  38% (158/414)
Resolving deltas:  39% (162/414)
Resolving deltas:  40% (166/414)
Resolving deltas:  41% (170/414)
Resolving deltas:  42% (174/414)
Resolving deltas:  43% (179/414)
Resolving deltas:  44% (183/414)
Resolving deltas:  45% (187/414)
Resolving deltas:  46% (191/414)
Resolving deltas:  47% (195/414)
Resolving deltas:  48% (199/414)
Resolving deltas:  49% (203/414)
Resolving deltas:  50% (207/414)
Resolving deltas:  51% (212/414)
Resolving deltas:  52% (216/414)
Resolving deltas:  53% (220/414)
Resolving deltas:  54% (224/414)
Resolving deltas:  55% (228/414)
Resolving deltas:  56% (232/414)
Resolving deltas:  57% (236/414)
Resolving deltas:  58% (241/414)
Resolving deltas:  59% (245/414)
Resolving deltas:  60% (249/414)
Resolving deltas:  61% (253/414)
Resolving deltas:  62% (257/414)
Resolving deltas:  63% (261/414)
Resolving deltas:  64% (265/414)
Resolving deltas:  65% (270/414)
Resolving deltas:  66% (274/414)
Resolving deltas:  67% (278/414)
Resolving deltas:  68% (282/414)
Resolving deltas:  69% (286/414)
Resolving deltas:  70% (290/414)
Resolving deltas:  71% (294/414)
Resolving deltas:  72% (299/414)
Resolving deltas:  73% (303/414)
Resolving deltas:  74% (307/414)
Resolving deltas:  75% (311/414)
Resolving deltas:  76% (315/414)
Resolving deltas:  77% (319/414)
Resolving deltas:  78% (323/414)
Resolving deltas:  79% (328/414)
Resolving deltas:  80% (332/414)
Resolving deltas:  81% (336/414)
Resolving deltas:  82% (340/414)
Resolving deltas:  83% (344/414)
Resolving deltas:  84% (348/414)
Resolving deltas:  85% (352/414)
Resolving deltas:  86% (357/414)
Resolving deltas:  87% (361/414)
Resolving deltas:  88% (365/414)
Resolving deltas:  89% (369/414)
Resolving deltas:  90% (373/414)
Resolving deltas:  91% (377/414)
Resolving deltas:  92% (381/414)
Resolving deltas:  93% (386/414)
Resolving deltas:  94% (390/414)
Resolving deltas:  95% (394/414)
Resolving deltas:  96% (398/414)
Resolving deltas:  97% (402/414)
Resolving deltas:  98% (406/414)
Resolving deltas:  99% (410/414)
Resolving deltas: 100% (414/414)
Resolving deltas: 100% (414/414), done.
== PIPELINE PREDEPLOY ==
TAG   : DEV000000001
EXEC  : execution/machine_list_dev.yml
BRANCH: develop
[WARNING]: Unable to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/localhost as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml with ini plugin:
/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml:8: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_from_status.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml with ini plugin:
/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml:3: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/deploy_per_machine.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with auto plugin: no root 'plugin'
key found, '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml' is not a valid YAML inventory
plugin config file
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml with ini plugin:
/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml:6: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_from_execution.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml with ini plugin: Invalid host pattern
'---' supplied, '---' is normally a sign this is a YAML file.
[WARNING]: Unable to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/predeploy_per_machine.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml with ini plugin:
/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml:3: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_from_status.yml as an inventory source
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with auto plugin: no root 'plugin' key
found, '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml' is not a valid YAML inventory plugin
config file
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with yaml plugin: YAML inventory has
invalid structure, it should be a dictionary, got: &lt;class
'ansible.parsing.yaml.objects.AnsibleSequence'>
[WARNING]:  * Failed to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml with ini plugin:
/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml:3: Expected key=value host variable
assignment, got: name:
[WARNING]: Unable to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible/rollback_per_machine.yml as an inventory source
[WARNING]: Unable to parse /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-
sitef/ansible as an inventory source
[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'
PLAY [Predeploy a partir do arquivo de execução] *******************************
TASK [Gathering Facts] *********************************************************
ok: [localhost]
TASK [Resolver filestore_env e filestore_base_dir sem recursão] ****************
ok: [localhost]
TASK [Mostrar variáveis de entrada e resolvidas] *******************************
ok: [localhost] => {
    "msg": [
        "execution_file_name          = execution/machine_list_dev.yml",
        "deployment_ref               = DEV000000001",
        "repo_root_resolved           = /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/..",
        "status_dir_resolved          = /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../status/DEV000000001",
        "filestore_env_resolved       = dev",
        "filestore_base_dir_resolved  = dev/DEV000000001",
        "nexus_base_url               = https://nexus-ci.onefiserv.net/repository/raw-apm0004548-dev"
    ]
}
TASK [Criar diretório de status da TAG] ****************************************
changed: [localhost]
TASK [Carregar arquivo de execução] ********************************************
ok: [localhost]
TASK [Falhar se não tiver máquinas no arquivo de execução] *********************
skipping: [localhost]
TASK [Executar pré-deploy por máquina] *****************************************
included: /tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/predeploy_per_machine.yml for localhost => (item=sitef-01)
TASK [Resolver nome da máquina atual] ******************************************
ok: [localhost]
TASK [Definir paths base do repositório] ***************************************
ok: [localhost]
TASK [Definir execution_dir do repositório] ************************************
ok: [localhost]
TASK [Definir machines_dir, packages_dir e scripts_dir do repositório] *********
ok: [localhost]
TASK [Definir candidatos de arquivo da máquina] ********************************
ok: [localhost]
TASK [Verificar candidatos de arquivo da máquina] ******************************
ok: [localhost] => (item=/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-01.yml)
ok: [localhost] => (item=/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../execution/sitef-01.yml)
ok: [localhost] => (item=/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../machines/sitef-01.yml)
ok: [localhost] => (item=/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-01.yml)
TASK [Selecionar machine_file existente] ***************************************
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-01.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../execution/machines/sitef-01.yml', 'ansible_loop_var': 'item'}) 
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../execution/sitef-01.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../execution/sitef-01.yml', 'ansible_loop_var': 'item'}) 
ok: [localhost] => (item={'changed': False, 'stat': {'exists': True, 'path': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../machines/sitef-01.yml', 'mode': '0640', 'isdir': False, 'ischr': False, 'isblk': False, 'isreg': True, 'isfifo': False, 'islnk': False, 'issock': False, 'uid': 1000, 'gid': 1000, 'size': 223, 'inode': 4338768, 'dev': 64775, 'nlink': 1, 'atime': 1765302228.9170992, 'mtime': 1765302228.9170992, 'ctime': 1765302228.9170992, 'wusr': True, 'rusr': True, 'xusr': False, 'wgrp': False, 'rgrp': True, 'xgrp': False, 'woth': False, 'roth': False, 'xoth': False, 'isuid': False, 'isgid': False, 'blocks': 8, 'block_size': 4096, 'device_type': 0, 'readable': True, 'writeable': True, 'executable': False, 'pw_name': 'ec2-user', 'gr_name': 'ec2-user', 'checksum': 'f8e045478135f2325c4af1b7ed9ee09e2cde05c0', 'mimetype': 'text/plain', 'charset': 'us-ascii', 'version': '3172765644', 'attributes': [], 'attr_flags': ''}, 'invocation': {'module_args': {'path': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../machines/sitef-01.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../machines/sitef-01.yml', 'ansible_loop_var': 'item'})
skipping: [localhost] => (item={'changed': False, 'stat': {'exists': False}, 'invocation': {'module_args': {'path': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-01.yml', 'follow': False, 'get_md5': False, 'get_checksum': True, 'get_mime': True, 'get_attributes': True, 'checksum_algorithm': 'sha1'}}, 'failed': False, 'item': '/tmp/tmp.0JkWY2d8Gi/elastic-compute-cloud-sitef/ansible/../inventory/machines/sitef-01.yml', 'ansible_loop_var': 'item'}) 
TASK [Falhar se arquivo da máquina não existir em nenhum caminho esperado] *****
skipping: [localhost]
TASK [Carregar config da máquina sitef-01] *************************************
ok: [localhost]
TASK [Validar package definido] ************************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Carregar config do pacote sitef-core-0.0.2-0] ****************************
ok: [localhost]
TASK [Validar components do pacote] ********************************************
ok: [localhost] => {
    "changed": false,
    "msg": "All assertions passed"
}
TASK [Calcular env_vars mescladas] *********************************************
ok: [localhost]
TASK [Definir usuário alvo padrão] *********************************************
ok: [localhost]
TASK [Definir ssh_common_args padrão] ******************************************
ok: [localhost]
TASK [Aplicar ProxyJump via bastion (quando necessário)] ***********************
skipping: [localhost]
TASK [Registrar host dinâmico para sitef-01] ***********************************
changed: [localhost]
TASK [Definir caminhos de status e deploy] *************************************
ok: [localhost]
TASK [Calcular caminhos de status/log] *****************************************
ok: [localhost]
TASK [Criar diretório de status da máquina] ************************************
changed: [localhost]
TASK [Escrever status inicial (pre-deploy:queued)] *****************************
changed: [localhost]
TASK [Garantir base do pipeline no host] ***************************************
ok: [localhost -> sitef-01(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/components)
ok: [localhost -> sitef-01(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/components/package)
ok: [localhost -> sitef-01(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts)
ok: [localhost -> sitef-01(100.99.25.45)] => (item=/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package)
TASK [Limpar pasta de scripts do pacote] ***************************************
changed: [localhost -> sitef-01(100.99.25.45)]
TASK [Recriar pasta de scripts do pacote] **************************************
changed: [localhost -> sitef-01(100.99.25.45)]
TASK [Copiar package.yml para o host] ******************************************
changed: [localhost -> sitef-01(100.99.25.45)]
TASK [Garantir diretórios dos componentes no host] *****************************
ok: [localhost -> sitef-01(100.99.25.45)] => (item=packages/linux/sitef-core-0.0.2-0.x86_64.rpm)
TASK [Baixar componentes do Nexus] *********************************************
ok: [localhost -> sitef-01(100.99.25.45)] => (item=packages/linux/sitef-core-0.0.2-0.x86_64.rpm)
TASK [Copiar scripts do pacote para o host] ************************************
ok: [localhost -> sitef-01(100.99.25.45)]
TASK [Listar conteúdo de /deploy/scripts (debug)] ******************************
ok: [localhost -> sitef-01(100.99.25.45)]
TASK [Mostrar conteúdo de scripts (debug)] *************************************
ok: [localhost] => {
    "scripts_ls.stdout_lines": [
        "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts:",
        "total 60",
        "drwxr-xr-x. 4 root root  4096 Dec  9 17:43 .",
        "drwxr-xr-x. 6 root root    70 Dec  9 17:39 ..",
        "-rwxr-xr-x. 1 root root  5470 Dec  9 14:24 backup.py",
        "-rwxr-xr-x. 1 root root  1017 Dec  9 13:56 init_deploy.sh",
        "-rwxr-xr-x. 1 root root   744 Dec  9 13:56 init_parallel.sh",
        "drwxr-xr-x. 2 root root    36 Dec  9 17:43 package",
        "-rw-r-----. 1 root root 19337 Dec  9 17:39 parallel.txt",
        "drwxr-x---. 2 root root    63 Dec  9 14:24 __pycache__",
        "-rwxr-xr-x. 1 root root  1566 Dec  9 13:56 script_deploy.py",
        "-rwxr-xr-x. 1 root root   175 Dec  9 13:56 script_prepare.py",
        "-rwxr-xr-x. 1 root root   677 Dec  9 13:56 script_rollback.py",
        "-rwxr-xr-x. 1 root root  1262 Dec  9 13:56 sitef_packages.py",
        "-rwxr-xr-x. 1 root root  3571 Dec  9 13:56 sitef.py",
        "",
        "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/package:",
        "total 8",
        "drwxr-xr-x. 2 root root   36 Dec  9 17:43 .",
        "drwxr-xr-x. 4 root root 4096 Dec  9 17:43 ..",
        "-rw-r--r--. 1 root root  140 Dec  9 17:43 sitef-core-0.0.2-0.yml",
        "",
        "/opt/SoftwareExpress/sitef-pipeline/deploy/scripts/__pycache__:",
        "total 12",
        "drwxr-x---. 2 root root   63 Dec  9 14:24 .",
        "drwxr-xr-x. 4 root root 4096 Dec  9 17:43 ..",
        "-rw-r-----. 1 root root 3399 Dec  9 14:24 backup.cpython-39.pyc",
        "-rw-r-----. 1 root root 3540 Dec  9 13:56 sitef.cpython-39.pyc"
    ]
}
TASK [Executar init_parallel.sh no host] ***************************************
changed: [localhost -> sitef-01(100.99.25.45)]
TASK [Atualizar status após execução do init_parallel.sh] **********************
changed: [localhost]
TASK [Falhar se init_parallel.sh retornou erro] ********************************
skipping: [localhost]
PLAY RECAP *********************************************************************
localhost                  : ok=36   changed=9    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0   
Command finished with status SUCCESS
