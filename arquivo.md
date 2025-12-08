#deploy_per_machine

- name: "Definir caminhos para {{ machine_name }}"
  ansible.builtin.set_fact:
    machine_file: "{{ repo_root }}/machine/{{ machine_name }}.yml"
    status_file: "{{ status_dir }}/deploy-{{ machine_name }}.json"
    host_base_dir: "/opt/SoftwareExpress/sitef"
    host_pkg_dir: "/opt/SoftwareExpress/sitef/package/linux"
    host_scripts_dir: "/opt/SoftwareExpress/sitef/scripts"

- name: "Carregar config da máquina {{ machine_name }}"
  ansible.builtin.include_vars:
    file: "{{ machine_file }}"
    name: machine_cfg

- name: "Carregar config do pacote {{ machine_cfg.package }}"
  ansible.builtin.include_vars:
    file: "{{ repo_root }}/package/{{ machine_cfg.package }}.yml"
    name: package_cfg

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

- name: "Registrar host dinâmico para {{ machine_name }}"
  ansible.builtin.add_host:
    name: "{{ machine_name }}"
    ansible_host: "{{ machine_cfg.host }}"
    ansible_user: "{{ target_user }}"
    ansible_connection: ssh
    ansible_ssh_common_args: "{{ target_ssh_common_args }}"

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

# Opcional: validar se o predeploy ocorreu antes
- name: "Verificar se status de predeploy existe"
  ansible.builtin.stat:
    path: "{{ status_dir }}/predeploy-{{ machine_name }}.json"
  register: predeploy_status_stat

- name: "Avisar se predeploy não foi encontrado (não bloqueante)"
  ansible.builtin.debug:
    msg: "ATENÇÃO: predeploy-{{ machine_name }}.json não encontrado. Deploy continuará mesmo assim."
  when: not predeploy_status_stat.stat.exists

# Garantir diretório scripts do pacote no host alvo
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

# Executar init.sh no host alvo
- name: "Executar script de deploy (init.sh) em {{ machine_name }}"
  become: true
  ansible.builtin.shell: |
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

# Ler deploy.txt do host alvo
- name: "Ler deploy.txt do host alvo"
  become: true
  ansible.builtin.shell: |
    cat "{{ host_scripts_dir }}/{{ package_cfg.script }}/deploy.txt" || echo "deploy.txt nao existe"
  delegate_to: "{{ machine_name }}"
  register: deploy_log
  changed_when: false

- name: "DEBUG - conteúdo do deploy.txt"
  ansible.builtin.debug:
    var: deploy_log.stdout_lines

# Atualizar status final
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
        "stdout": {{ deploy_result.stdout | to_json }},
        "stderr": {{ deploy_result.stderr | to_json }},
        "deploy_log": {{ deploy_log.stdout | to_json }}
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
        "stdout": {{ deploy_result.stdout | to_json }},
        "stderr": {{ deploy_result.stderr | to_json }},
        "deploy_log": {{ deploy_log.stdout | to_json }}
      }
  when: deploy_result is failed

# Se quiser falhar o pipeline quando der erro:
- name: "Falhar pipeline se init.sh retornou erro"
  ansible.builtin.fail:
    msg: "Deploy falhou em {{ machine_name }} (host {{ machine_cfg.host }}). Verifique deploy.txt e status JSON."
  when: deploy_result is failed
