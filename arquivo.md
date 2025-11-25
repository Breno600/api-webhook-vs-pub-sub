---
# ============================================================
# PLAY 1 - CONTROLLER
# Lê o YAML do grupo, descobre as máquinas e o package,
# carrega o package e cria hosts dinâmicos para o PLAY 2.
# ============================================================

- name: "[CONTROL] Carregar grupo e pacote"
  hosts: localhost
  gather_facts: false

  vars:
    repo_root: "{{ playbook_dir }}/.."
    group_file_name: "{{ group_file_name | default('teste-module.yaml') }}"
    group_file_path: "{{ repo_root }}/group-machine/{{ group_file_name }}"

  tasks:
    - name: "Carregar YAML do grupo"
      ansible.builtin.include_vars:
        file: "{{ group_file_path }}"
        name: group_cfg

    - name: "Carregar YAML do package do grupo"
      ansible.builtin.include_vars:
        file: "{{ repo_root }}/package/{{ group_cfg.package }}.yaml"
        name: package_cfg

    - name: "Exportar facts para o próximo play"
      ansible.builtin.set_fact:
        pkg_before_script:  "{{ package_cfg.before_script  | default([]) }}"
        pkg_after_script:   "{{ package_cfg.after_script   | default([]) }}"
        pkg_install_module: "{{ package_cfg.install_module | default([]) }}"
        pkg_remove_module:  "{{ package_cfg.remove_module  | default([]) }}"

    # Aqui a gente NÃO usa o "package" do machine/*.yaml
    # Só usa a lista de arquivos para descobrir o IP

    - name: "Registrar hosts do grupo (dinâmico)"
      ansible.builtin.add_host:
        name: "{{ host_ip }}"
        ansible_host: "{{ host_ip }}"
        ansible_user: ansible
        ansible_ssh_private_key_file: "~/.ssh/id_rsa"
        groups: dynamic_group
      loop: "{{ group_cfg.machines }}"
      loop_control:
        loop_var: machine_file
      vars:
        host_ip: >-
          {{
            machine_file
            | regex_replace('^ip-', '')
            | regex_replace('\\.ya?ml$', '')
            | regex_replace('-', '.')
          }}

# ============================================================
# PLAY 2 - TARGETS (todas as máquinas do grupo)
# Executa before_script -> instala módulos do Nexus -> after_script
# para cada máquina do grupo.
# ============================================================

- name: "[TARGET-GROUP] Aplicar pacote nas máquinas do grupo"
  hosts: dynamic_group
  become: yes
  gather_facts: false

  vars:
    pkg_before_script:  "{{ hostvars['localhost']['pkg_before_script'] }}"
    pkg_after_script:   "{{ hostvars['localhost']['pkg_after_script'] }}"
    pkg_install_module: "{{ hostvars['localhost']['pkg_install_module'] }}"
    pkg_remove_module:  "{{ hostvars['localhost']['pkg_remove_module'] }}"

    nexus_base_url: "https://nexus-ci.onefiserv.net/repository/raw-apm00054846-dev/packages/Linux"
    nexus_user: "{{ lookup('env', 'NEXUS_USER') }}"
    nexus_password: "{{ lookup('env', 'NEXUS_PASSWORD') }}"

  tasks:
    - name: "[BEFORE] Executar before_script"
      ansible.builtin.shell: "{{ item }}"
      loop: "{{ pkg_before_script }}"
      when: pkg_before_script | length > 0

    - name: "[NEXUS] Baixar módulos (RPM)"
      ansible.builtin.get_url:
        url: "{{ nexus_base_url }}/{{ item }}.x86_64.rpm"
        dest: "/tmp/{{ item }}.x86_64.rpm"
        mode: "0644"
        url_username: "{{ nexus_user }}"
        url_password: "{{ nexus_password }}"
        force_basic_auth: true
      loop: "{{ pkg_install_module }}"
      when: pkg_install_module | length > 0

    - name: "[RPM] Instalar módulos"
      ansible.builtin.shell: "rpm -Uvh --force /tmp/{{ item }}.x86_64.rpm"
      loop: "{{ pkg_install_module }}"
      when: pkg_install_module | length > 0

    - name: "[REMOVE] Remover módulos (RPM)"
      ansible.builtin.shell: "rpm -e --nodeps {{ item }}"
      loop: "{{ pkg_remove_module }}"
      when: pkg_remove_module | length > 0

    - name: "[AFTER] Executar after_script"
      ansible.builtin.shell: "{{ item }}"
      loop: "{{ pkg_after_script }}"
      when: pkg_after_script | length > 0