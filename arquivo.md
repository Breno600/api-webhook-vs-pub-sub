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

    - name: "Filtrar máquinas com predeploy success"
      ansible.builtin.set_fact:
        machines_to_deploy: >-
          {{
            predeploy_jsons
            | selectattr('status', 'defined')
            | selectattr('status', 'equalto', 'success')
            | map(attribute='machine_name')
            | list
          }}

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
        loop_var: machine_name
      vars:
        deployment_ref: "{{ deployment_ref }}"
        repo_root: "{{ repo_root }}"
        status_dir: "{{ status_dir }}"
        package_name: "{{ package_name_eff }}"
        package_version: "{{ package_version_eff }}"
