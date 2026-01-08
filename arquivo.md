## ansible/ansible.cfg

[defaults]
host_key_checking = False
retry_files_enabled = False
stdout_callback = yaml
timeout = 30
interpreter_python = auto_silent
forks = 20

[ssh_connection]
pipelining = True
ssh_args = -o ControlMaster=auto -o ControlPersist=60s -o ServerAliveInterval=30 -o ServerAliveCountMax=4

## execution/execution.yml
targets:
  - sitef-01
  - sitef-02
  - sitef-03

## machines/sitef-01.yml
ssh:
  host: 10.0.0.1
  user: ec2-user

s3:
  region: sa-east-1
  bucket: my-bucket
  prefix: sitef/sitef-01/artifacts/

deploy:
  download_dir: /opt/sitef/downloads
  scripts_dir: /opt/sitef/scripts
  init_script: init.sh
  init_args: "--machine sitef-01"


## ansible/playbooks/deploy_from_execution.yml
---
- name: Build dynamic target list from execution + machines folder
  hosts: localhost
  connection: local
  gather_facts: false

  vars:
    # default paths (playbook runs from ansible/)
    execution_file: "{{ execution_file | default('../execution/execution.yml') }}"
    machines_dir: "{{ machines_dir | default('../machines') }}"

  tasks:
    - name: Load execution file
      include_vars:
        file: "{{ execution_file }}"
        name: exec

    - name: Validate execution targets
      assert:
        that:
          - exec.targets is defined
          - exec.targets | length > 0
        fail_msg: "No targets found in {{ execution_file }} (targets is empty)."

    - name: Check machine files exist
      stat:
        path: "{{ machines_dir }}/{{ item }}.yml"
      register: machine_files
      loop: "{{ exec.targets }}"

    - name: Fail if any machine file is missing
      assert:
        that: item.stat.exists
        fail_msg: "Missing machine definition: {{ machines_dir }}/{{ item.item }}.yml"
      loop: "{{ machine_files.results }}"

    - name: Load machine definitions
      include_vars:
        file: "{{ machines_dir }}/{{ item }}.yml"
        name: "m_{{ item | replace('-', '_') }}"
      loop: "{{ exec.targets }}"

    - name: Add targets dynamically (per machine vars)
      add_host:
        name: "{{ item }}"
        groups: deliver_targets

        ansible_host: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).ssh.host }}"
        ansible_user: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).ssh.user | default('ec2-user') }}"

        # If you want to force a specific key on bastion, uncomment and set it here:
        # ansible_ssh_private_key_file: "/home/ansible/.ssh/sitef_key"

        s3_region: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).s3.region }}"
        s3_bucket: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).s3.bucket }}"
        s3_prefix: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).s3.prefix }}"

        download_dir: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).deploy.download_dir | default('/opt/sitef/downloads') }}"
        scripts_dir: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).deploy.scripts_dir | default('/opt/sitef/scripts') }}"
        init_script: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).deploy.init_script | default('init.sh') }}"
        init_args: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).deploy.init_args | default('') }}"
      loop: "{{ exec.targets }}"

    - name: Show selected targets
      debug:
        msg:
          - "Selected targets: {{ exec.targets }}"
          - "Machines dir: {{ machines_dir }}"
          - "Execution file: {{ execution_file }}"

- name: Deliver to selected machines (download from S3 + run script)
  hosts: deliver_targets
  gather_facts: true
  become: true

  vars:
    # you can set to false if you don't want sudo on targets
    # become: true is declared in play, so override by: -e ansible_become=false
    aws_cli_cmd: aws

  tasks:
    - name: Ensure directories exist
      file:
        path: "{{ item }}"
        state: directory
        mode: "0755"
      loop:
        - "{{ scripts_dir }}"
        - "{{ download_dir }}"

    - name: Copy scripts to target
      copy:
        src: "../../../scripts/"
        dest: "{{ scripts_dir }}/"
        mode: "0755"

    - name: Check AWS CLI exists on target
      command: "{{ aws_cli_cmd }} --version"
      register: awscli_check
      changed_when: false

    - name: Download artifacts from S3 to target folder
      command: >
        {{ aws_cli_cmd }} s3 sync
        s3://{{ s3_bucket }}/{{ s3_prefix }}
        {{ download_dir }}/
        --region {{ s3_region }}
      register: s3sync
      changed_when: >
        ('download:' in s3sync.stdout) or
        ('copy:' in s3sync.stdout) or
        ('update:' in s3sync.stdout)

    - name: Execute init script
      command: "{{ scripts_dir }}/{{ init_script }} {{ init_args }}"
      args:
        chdir: "{{ scripts_dir }}"
      register: init_out

    - name: Show init output
      debug:
        var: init_out.stdout_lines

## Harness script
ansible-playbook playbooks/deploy_from_execution.yml \
  -e execution_file=../execution/execution.yml \
  -e machines_dir=../machines
