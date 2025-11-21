ip:
  bin: "1.0.2"
  sitef: "2.0.0"
  modulo_x: "3.1.0"

gruoup:
machines:
  - "Ip-10.0.0.1.yaml"
  - "Ip-10.0.0.2.yaml"

modules:
  bin: "3.0.0"
  sitef: "2.5.0"
  modulo_y: "4.0.0"

deploy_from_machine.yml
---
# =========================================================
# PLAY 1 - CONTROL NODE (máquina Ansible/jumper)
# Lê YAMLs, aplica groups alterados e atualiza Ip-*.yaml.
# Monta inventário dinâmico para as máquinas alvo.
# =========================================================
- name: "[CONTROL NODE] Preparar mapa de máquinas a partir dos YAML"
  hosts: localhost
  gather_facts: false

  vars:
    repo_root: "{{ playbook_dir }}/.."              # raiz do repo
    machines_dir: "{{ repo_root }}/machine"
    group_machines_dir: "{{ repo_root }}/group-machine"

  tasks:
    - name: "[LOG] Caminhos configurados"
      ansible.builtin.debug:
        msg:
          - "Repo root:          {{ repo_root }}"
          - "Diretório máquinas: {{ machines_dir }}"
          - "Diretório groups:   {{ group_machines_dir }}"

    # ---------- GIT DIFF ----------
    - name: "[GIT] Arquivos alterados no último commit (HEAD~1..HEAD)"
      ansible.builtin.command:
        cmd: "git diff --name-only HEAD~1 HEAD"
        chdir: "{{ repo_root }}"
      register: git_diff
      failed_when: false
      changed_when: false

    - name: "[LOG] Arquivos alterados (git diff)"
      ansible.builtin.debug:
        msg:
          - "Arquivos alterados: {{ git_diff.stdout_lines | default([]) }}"

    - name: "[SET] Base names dos arquivos alterados"
      ansible.builtin.set_fact:
        changed_files: "{{ git_diff.stdout_lines | default([]) }}"
        changed_basenames: "{{ (git_diff.stdout_lines | default([])) | map('basename') | list }}"

    # ---------- MACHINES ----------
    - name: "[SCAN] Encontrar arquivos de máquina (Ip-*.yaml)"
      ansible.builtin.find:
        paths: "{{ machines_dir }}"
        patterns: "Ip-*.yaml"
      register: machine_files

    - name: "[FAIL] Nenhum arquivo de máquina encontrado"
      ansible.builtin.fail:
        msg: "Nenhum Ip-*.yaml encontrado em {{ machines_dir }}"
      when: machine_files.matched | default(0) | int == 0

    - name: "[LOG] Arquivos de máquina encontrados"
      ansible.builtin.debug:
        msg:
          - "Count: {{ machine_files.matched }}"
          - "Lista: {{ machine_files.files | map(attribute='path') | list }}"

    - name: "[LOAD] Carregar definições de máquinas (módulos dinâmicos)"
      ansible.builtin.set_fact:
        machines: "{{ (machines | default({})) | combine({ ip: {
                    'path': item.path,
                    'modules': machine_data
                  }}, recursive=True) }}"
      loop: "{{ machine_files.files }}"
      loop_control:
        label: "{{ item.path }}"
      vars:
        file_raw: "{{ lookup('ansible.builtin.file', item.path) }}"
        machine_data: "{{ file_raw | from_yaml }}"
        ip: "{{ item.path | basename
                        | regex_replace('^Ip-', '')
                        | regex_replace('\\.yaml$', '') }}"

    - name: "[LOG] Estado inicial das máquinas (antes dos groups)"
      ansible.builtin.debug:
        var: machines

    # ---------- GROUP-MACHINE ----------
    - name: "[STAT] Verificar se diretório group-machine existe"
      ansible.builtin.stat:
        path: "{{ group_machines_dir }}"
      register: st_group_dir

    - name: "[INFO] Diretório group-machine não existe (sem groups)"
      ansible.builtin.debug:
        msg: "Diretório {{ group_machines_dir }} não existe; nenhum group será aplicado."
      when: not st_group_dir.stat.exists

    - name: "[SCAN] Encontrar arquivos de group-machine (*.yaml)"
      ansible.builtin.find:
        paths: "{{ group_machines_dir }}"
        patterns: "*.yaml"
      register: group_files
      when: st_group_dir.stat.exists

    - name: "[LOG] group-machine encontrados"
      ansible.builtin.debug:
        msg:
          - "Groups encontrados: {{ group_files.matched | default(0) }}"
          - "Lista: {{ group_files.files | map(attribute='path') | list }}"
      when:
        - st_group_dir.stat.exists
        - group_files.matched | default(0) | int > 0

    - name: "[GROUP] Marcar groups não alterados como ignorados"
      ansible.builtin.debug:
        msg: "Group '{{ item.path | basename }}' NÃO alterado neste commit; será ignorado."
      loop: "{{ group_files.files | default([]) }}"
      loop_control:
        label: "{{ item.path }}"
      when:
        - st_group_dir.stat.exists
        - group_files.matched | default(0) | int > 0
        - (item.path | basename) not in changed_basenames

    - name: "[GROUP] Aplicar groups ALTERADOS (upgrade/downgrade em lote nos YAML)"
      when:
        - st_group_dir.stat.exists
        - group_files.matched | default(0) | int > 0
      block:
        - name: "[GROUP] Processar groups alterados"
          ansible.builtin.include_tasks: group_apply.yml
          loop: "{{ group_files.files }}"
          loop_control:
            label: "{{ item.path }}"
          when: (item.path | basename) in changed_basenames
          vars:
            changed_basenames: "{{ changed_basenames }}"

    - name: "[LOG] Estado final das máquinas após aplicar groups"
      ansible.builtin.debug:
        var: machines

    # ---------- ADICIONAR HOSTS DINÂMICOS ----------
    - name: "[ADD_HOST] Adicionar máquinas ao inventário dinâmico (grupo 'targets')"
      ansible.builtin.add_host:
        name: "{{ item.key }}"
        ansible_host: "{{ item.key }}"
        groups: "targets"
        modules: "{{ item.value.modules }}"
      loop: "{{ machines | dict2items }}"
      loop_control:
        label: "{{ item.key }}"

    - name: "[LOG] Hosts adicionados ao grupo 'targets'"
      ansible.builtin.debug:
        msg:
          - "Hosts no grupo 'targets': {{ machines.keys() | list }}"

# =========================================================
# PLAY 2 - TARGETS (máquinas finais)
# SIMULAÇÃO: cria arquivos de marcador para cada módulo/versão.
# =========================================================
- name: "[TARGETS] SIMULAÇÃO - Criar arquivos marcadores por módulo/versão"
  hosts: targets
  become: true
  gather_facts: false

  vars:
    marker_dir: "/var/tmp/deploy-modulos"   # diretório onde os 'touch' vão ficar

  pre_tasks:
    - name: "[LOG] Iniciando simulação na máquina alvo"
      ansible.builtin.debug:
        msg:
          - "Host alvo: {{ inventory_hostname }}"
          - "Módulos declarados: {{ modules | default({}) }}"

    - name: "[CHECK] Garantir que há módulos definidos para este host"
      ansible.builtin.fail:
        msg: "Nenhum módulo definido para o host {{ inventory_hostname }} (variável 'modules' vazia)."
      when: modules | default({}) | length == 0

    - name: "[DIR] Criar diretório de marcadores"
      ansible.builtin.file:
        path: "{{ marker_dir }}"
        state: directory
        mode: "0755"

  tasks:
    - name: "[SIMULATE] Logar o que SERIA instalado para cada módulo"
      ansible.builtin.debug:
        msg: >
          Host {{ inventory_hostname }} -
          módulo={{ item.key }} versão={{ item.value }} -
          marcador={{ marker_dir }}/{{ item.key }}-{{ item.value }}.marker
      loop: "{{ modules | dict2items }}"
      loop_control:
        label: "{{ item.key }}-{{ item.value }}"

    - name: "[TOUCH] Criar arquivo marcador para cada módulo/versão"
      ansible.builtin.file:
        path: "{{ marker_dir }}/{{ item.key }}-{{ item.value }}.marker"
        state: touch
        mode: "0644"
      loop: "{{ modules | dict2items }}"
      loop_control:
        label: "{{ item.key }}-{{ item.value }}"
      register: touch_results

    - name: "[LOG] Resultado da criação dos marcadores"
      ansible.builtin.debug:
        msg: >
          Host {{ inventory_hostname }} -
          marcador {{ item.item.key }}-{{ item.item.value }} -
          changed={{ item.changed | default('unknown') }},
          path={{ item.path | default('n/a') }}
      loop: "{{ touch_results.results }}"

  post_tasks:
    - name: "[SUMMARY] Resumo da simulação no host"
      ansible.builtin.debug:
        msg:
          - "Host {{ inventory_hostname }} processado em modo SIMULAÇÃO."
          - "Marcadores criados em: {{ marker_dir }}"
          - "Módulos declarados: {{ modules }}"

group_apply.yml
---
# item.path = caminho do group-machine (ex.: .../group-machine/carrefour.yaml)
# changed_basenames = lista de nomes de arquivos alterados (basenames) vinda do play principal

- name: "[GROUP] Carregar definição do group-machine"
  ansible.builtin.set_fact:
    group_data: "{{ lookup('ansible.builtin.file', item.path) | from_yaml }}"
    group_name: "{{ item.path | basename | regex_replace('\\.yaml$', '') }}"
    group_filename: "{{ item.path | basename }}"

- name: "[LOG] Detalhes do group-machine"
  ansible.builtin.debug:
    msg:
      - "Processando group '{{ group_name }}' (arquivo: {{ group_filename }})"
      - "Máquinas-alvo (arquivos): {{ group_data.machines | default([]) }}"
      - "Módulos do group: {{ group_data.modules | default({}) }}"

- name: "[WARN] group-machine sem 'machines' ou 'modules'"
  ansible.builtin.debug:
    msg: "Group '{{ group_name }}' ignorado: precisa de 'machines' (lista) e 'modules' (map)."
  when: group_data.machines | default([]) | length == 0 or
        group_data.modules | default({}) | length == 0

- name: "[INFO] group-machine NÃO alterado neste commit - será ignorado"
  ansible.builtin.debug:
    msg: "Group '{{ group_name }}' não foi alterado neste commit. NÃO vai sobrescrever versões das máquinas."
  when:
    - group_data.machines | default([]) | length > 0
    - group_data.modules | default({}) | length > 0
    - group_filename not in changed_basenames

- name: "[GROUP] Aplicar modules do group nas máquinas (upgrade/downgrade em lote)"
  when:
    - group_data.machines | default([]) | length > 0
    - group_data.modules | default({}) | length > 0
    - group_filename in changed_basenames
  block:
    - name: "[GROUP→MACHINE] Processar cada máquina do group"
      ansible.builtin.include_tasks: group_apply_machine.yml
      loop: "{{ group_data.machines }}"
      loop_control:
        label: "{{ item }}"
      vars:
        group_name_local: "{{ group_name }}"
        group_modules: "{{ group_data.modules }}"


group_apply_machine.yml
---
# item = "Ip-10.0.0.1.yaml" (nome do arquivo listado no group)
# group_modules = { bin: "3.0.0", sitef: "2.0.0", ... }
# group_name_local = nome do grupo atual

- name: "[GROUP→MACHINE] Calcular IP da máquina alvo a partir do filename"
  ansible.builtin.set_fact:
    gm_ip: "{{ item | basename
                    | regex_replace('^Ip-', '')
                    | regex_replace('\\.yaml$', '') }}"

- name: "[LOG] Máquina alvo do group"
  ansible.builtin.debug:
    msg:
      - "Group '{{ group_name_local }}' vai aplicar módulos em {{ gm_ip }}"
      - "Módulos do group: {{ group_modules }}"

- name: "[CHECK] Verificar se máquina {{ gm_ip }} existe em 'machines'"
  ansible.builtin.debug:
    msg: "Máquina {{ gm_ip }} não está definida em machines/*.yaml. Ignorando para este group."
  when: machines[gm_ip] is not defined

- name: "[LOAD] Módulos atuais da máquina"
  ansible.builtin.set_fact:
    machine_modules_current: "{{ machines[gm_ip].modules | default({}) }}"
  when: machines[gm_ip] is defined

- name: "[LOG] Módulos atuais da máquina antes do group"
  ansible.builtin.debug:
    msg:
      - "Máquina {{ gm_ip }} - módulos atuais (antes): {{ machine_modules_current }}"
  when: machines[gm_ip] is defined

# Regras:
# - Módulos NÃO listados no group: permanecem como estão.
# - Módulos listados no group: são SOBRESCRITOS (upgrade ou downgrade em lote).
- name: "[MERGE] Sobrescrever módulos da máquina com módulos do group"
  ansible.builtin.set_fact:
    merged_modules: "{{ machine_modules_current | combine(group_modules, recursive=True) }}"
  when: machines[gm_ip] is defined

- name: "[LOG] Módulos finais após aplicar group"
  ansible.builtin.debug:
    msg:
      - "Máquina {{ gm_ip }} - módulos finais após group '{{ group_name_local }}': {{ merged_modules }}"
  when: machines[gm_ip] is defined

- name: "[UPDATE FACT] Atualizar mapa 'machines' em memória com módulos mergeados"
  ansible.builtin.set_fact:
    machines: >-
      {{
        machines |
        combine(
          {
            gm_ip: machines[gm_ip] | combine({'modules': merged_modules}, recursive=True)
          },
          recursive=True
        )
      }}
  when: machines[gm_ip] is defined

- name: "[WRITE] Atualizar arquivo Ip-*.yaml com versões finais (após group)"
  ansible.builtin.copy:
    dest: "{{ machines[gm_ip].path }}"
    content: "{{ merged_modules | to_nice_yaml }}"
    mode: "0644"
  when: machines[gm_ip] is defined

- name: "[LOG] Arquivo Ip-*.yaml atualizado em disco"
  ansible.builtin.debug:
    msg:
      - "Arquivo {{ machines[gm_ip].path }} atualizado com conteúdo:"
      - "{{ merged_modules }}"
  when: machines[gm_ip] is defined
