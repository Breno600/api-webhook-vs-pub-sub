---
- name: Build dynamic target list from execution + machines folder
  hosts: localhost
  connection: local
  gather_facts: false

  vars:
    execution_file_default: "../execution/execution.yml"
    machines_dir_default: "../machines"

  tasks:
    - name: Resolve paths
      set_fact:
        execution_file_path: "{{ execution_file | default(execution_file_default) }}"
        machines_dir_path: "{{ machines_dir | default(machines_dir_default) }}"

    - name: Load execution file
      include_vars:
        file: "{{ execution_file_path }}"
        name: exec

    - name: Validate execution targets
      assert:
        that:
          - exec.targets is defined
          - exec.targets | length > 0
        fail_msg: "No targets found in {{ execution_file_path }} (targets is empty)."

    - name: Check machine files exist
      stat:
        path: "{{ machines_dir_path }}/{{ item }}.yml"
      register: machine_files
      loop: "{{ exec.targets }}"

    - name: Fail if any machine file is missing
      assert:
        that: item.stat.exists
        fail_msg: "Missing machine definition: {{ machines_dir_path }}/{{ item.item }}.yml"
      loop: "{{ machine_files.results }}"

    - name: Load machine definitions
      include_vars:
        file: "{{ machines_dir_path }}/{{ item }}.yml"
        name: "m_{{ item | replace('-', '_') }}"
      loop: "{{ exec.targets }}"

    - name: Add targets dynamically (per machine vars)
      add_host:
        name: "{{ item }}"
        groups: deliver_targets

        ansible_host: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).ssh.host }}"
        ansible_user: "{{ lookup('vars', 'm_' ~ (item | replace('-', '_'))).ssh.user | default('ec2-user') }}"

        # If you need a specific key on bastion, uncomment:
        # ansible_ssh_private_key_file: "/home/ec2-user/.ssh/sitef_key"

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
          - "Execution file: {{ execution_file_path }}"
          - "Machines dir: {{ machines_dir_path }}"
          - "Selected targets: {{ exec.targets }}"

- name: Deliver to selected machines (download from S3 + run script)
  hosts: deliver_targets
  gather_facts: true
  become: true

  vars:
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
