- name: Check AWS CLI exists on target
  command: aws --version
  register: awscli_check
  changed_when: false
  failed_when: false

- name: Install AWS CLI (Amazon Linux / RHEL family)
  when:
    - awscli_check.rc != 0
    - ansible_facts.os_family in ["RedHat"]
  become: true
  package:
    name: awscli
    state: present

- name: Install AWS CLI (Debian/Ubuntu)
  when:
    - awscli_check.rc != 0
    - ansible_facts.os_family == "Debian"
  become: true
  apt:
    name: awscli
    state: present
    update_cache: true

# Fallback: AWS CLI v2 installer (works almost everywhere)
- name: Re-check AWS CLI after package install
  when: awscli_check.rc != 0
  command: aws --version
  register: awscli_check2
  changed_when: false
  failed_when: false

- name: Install AWS CLI v2 (fallback)
  when: awscli_check.rc != 0 and awscli_check2.rc != 0
  become: true
  block:
    - name: Ensure unzip and curl are present
      package:
        name:
          - unzip
          - curl
        state: present

    - name: Download AWS CLI v2
      get_url:
        url: "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip"
        dest: "/tmp/awscliv2.zip"
        mode: "0644"

    - name: Unzip AWS CLI v2
      unarchive:
        src: "/tmp/awscliv2.zip"
        dest: "/tmp"
        remote_src: true

    - name: Install AWS CLI v2
      command: "/tmp/aws/install --update"

- name: Final AWS CLI check
  command: aws --version
  register: awscli_final
  changed_when: false
