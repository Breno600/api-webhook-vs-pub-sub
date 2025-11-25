---
# ============================================================
# PLAY 1 - CONTROL NODE (jumper/ansible)
# Lê o YAML da máquina e o YAML do pacote e exporta como facts
# para o próximo play.
# ============================================================

- name: "[CONTROL] Carregar config da máquina e do pacote"
  hosts: localhost
  gather_facts: false

  vars:
    # arquivo da máquina (pode virar parâmetro depois)
    machine_file_name: "{{ machine_file_name | default('ip-100.99.41.58.yaml') }}"
    machine_file_path: "{{ playbook_dir }}/machine/{{ machine_file_name }}"

  tasks:
    - name: "Carregar YAML da máquina"
      ansible.builtin.include_vars:
        file: "{{ machine_file_path }}"
        name: machine_cfg

    - name: "Definir nome do pacote"
      ansible.builtin.set_fact:
        selected_package: "{{ machine_cfg.package }}"

    - name: "Carregar YAML do pacote"
      ansible.builtin.include_vars:
        file: "{{ playbook_dir }}/package/{{ selected_package }}.yaml"
        name: package_cfg

    - name: "Exportar facts para o próximo play"
      ansible.builtin.set_fact:
        pkg_before_script: "{{ package_cfg.before_script  | default([]) }}"
        pkg_after_script:  "{{ package_cfg.after_script   | default([]) }}"
        pkg_install_module: "{{ package_cfg.install_module | default([]) }}"

# ============================================================
# PLAY 2 - TARGET NODE (máquina 100.99.41.58)
# Executa before_script -> instala módulos do Nexus -> after_script
# ============================================================

- name: "[TARGET] Aplicar pacote na máquina"
  hosts: "{{ target_host | default('100.99.41.58') }}"
  become: yes
  gather_facts: false

  vars:
    # Puxa os facts criados no localhost no play anterior
    pkg_before_script: "{{ hostvars['localhost']['pkg_before_script'] }}"
    pkg_after_script:  "{{ hostvars['localhost']['pkg_after_script'] }}"
    pkg_install_module: "{{ hostvars['localhost']['pkg_install_module'] }}"

    # Ajustar essa URL pro caminho real do seu repositório no Nexus
    nexus_base_url: "https://nexus-ci.onefiserv.net/REPO/PATH"

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

    - name: "[AFTER] Executar after_script"
      ansible.builtin.shell: "{{ item }}"
      loop: "{{ pkg_after_script }}"
      when: pkg_after_script | length > 0