# initpara
- name: "Executar init_parallel.sh no host (com log + stdout)"
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ deploy_scripts_dir }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_parallel.sh 2>&1 | tee -a "{{ deploy_scripts_dir }}/init_parallel.log"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  become: true
  delegate_to: "{{ current_machine }}"
  register: init_parallel_result
  changed_when: true

# initrollback
- name: "Executar init_rollback.sh no host (com log + stdout)"
  ansible.builtin.shell: |
    set -o pipefail
    cd "{{ rollback_scripts_dir }}"
    /usr/bin/stdbuf -oL -eL /bin/bash -x ./init_rollback.sh 2>&1 | tee -a "{{ rollback_scripts_dir }}/init_rollback.log"
    exit ${PIPESTATUS[0]}
  args:
    executable: /bin/bash
  environment: "{{ merged_env_vars }}"
  become: true
  delegate_to: "{{ current_machine }}"
  register: init_rollback_result
  changed_when: true


----------------

## harness_filestore_upload.yml
# harness_filestore_upload.yml
- name: "Compute File Store naming"
  ansible.builtin.set_fact:
    fs_env: "{{ filestore_env | lower }}"
    fs_ref_lower: "{{ deployment_ref | lower }}"
    fs_ref_upper: "{{ deployment_ref | upper }}"
    fs_machine: "{{ current_machine | lower }}"
    fs_folder_name: "{{ (filestore_env | lower) ~ '/' ~ (deployment_ref | upper) }}"
    fs_json_name: "{{ (deployment_ref | lower) ~ '-' ~ (current_machine | lower) ~ '-' ~ (filestore_env | lower) ~ '.json' }}"
    fs_log_name: "{{ (deployment_ref | lower) ~ '-' ~ (current_machine | lower) ~ '-' ~ (filestore_env | lower) ~ '.log' }}"

# OBS: identifiers precisam ser "unique" se a API não deixar sobrescrever.
# Eu coloco um suffix com timestamp pra não quebrar (histórico).
- name: "Compute unique identifiers"
  ansible.builtin.set_fact:
    fs_suffix: "{{ ansible_date_time.epoch }}"
    fs_json_identifier: "{{ (deployment_ref | lower) ~ '-' ~ (current_machine | lower) ~ '-' ~ (filestore_env | lower) ~ '-json-' ~ ansible_date_time.epoch }}"
    fs_log_identifier: "{{ (deployment_ref | lower) ~ '-' ~ (current_machine | lower) ~ '-' ~ (filestore_env | lower) ~ '-log-' ~ ansible_date_time.epoch }}"

- name: "Create temp log file locally (controller)"
  ansible.builtin.copy:
    dest: "{{ status_dir }}/{{ current_machine }}/pipeline.log"
    mode: "0644"
    content: "{{ log_content | default('') }}"

# 1) Garante pasta raiz do deployment (opcional; se já existe, ignore falha)
- name: "Ensure folder exists in File Store (best effort)"
  ansible.builtin.shell: |
    set -euo pipefail
    curl -sS -X POST \
      "{{ harness_base_url }}/ng/api/file-store?accountIdentifier={{ harness_account }}&orgIdentifier={{ harness_org }}&projectIdentifier={{ harness_project }}" \
      -H "x-api-key: {{ harness_pat }}" \
      -F "name={{ fs_folder_name.split('/')[-1] }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier=Root" \
      -F "identifier={{ (filestore_env | lower) ~ '-' ~ (deployment_ref | lower) }}"
  args:
    executable: /bin/bash
  no_log: true
  changed_when: false
  failed_when: false

# 2) Upload JSON (histórico)
- name: "Upload JSON (history) to File Store"
  ansible.builtin.shell: |
    set -euo pipefail
    curl -sS -X POST \
      "{{ harness_base_url }}/ng/api/file-store?accountIdentifier={{ harness_account }}&orgIdentifier={{ harness_org }}&projectIdentifier={{ harness_project }}" \
      -H "x-api-key: {{ harness_pat }}" \
      -F "name={{ fs_json_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier=Root" \
      -F "identifier={{ fs_json_identifier }}" \
      -F 'tags=[{"key":"status","value":"{{ status_tag_value }}"}{% if extra_tags is defined and extra_tags|length>0 %}{% for t in extra_tags %},{"key":"status","value":"{{ t }}"}{% endfor %}{% endif %}]' \
      -F "content=@{{ machine_status_file }}"
  args:
    executable: /bin/bash
  no_log: true
  changed_when: false
  failed_when: false

# 3) Upload LOG (histórico)
- name: "Upload LOG (history) to File Store"
  ansible.builtin.shell: |
    set -euo pipefail
    curl -sS -X POST \
      "{{ harness_base_url }}/ng/api/file-store?accountIdentifier={{ harness_account }}&orgIdentifier={{ harness_org }}&projectIdentifier={{ harness_project }}" \
      -H "x-api-key: {{ harness_pat }}" \
      -F "name={{ fs_log_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier=Root" \
      -F "identifier={{ fs_log_identifier }}" \
      -F 'tags=[{"key":"status","value":"{{ status_tag_value }}"}{% if extra_tags is defined and extra_tags|length>0 %}{% for t in extra_tags %},{"key":"status","value":"{{ t }}"}{% endfor %}{% endif %}]' \
      -F "content=@{{ status_dir }}/{{ current_machine }}/pipeline.log"
  args:
    executable: /bin/bash
  no_log: true
  changed_when: false
  failed_when: false

# 4) (Opcional mas recomendado) Upload "LATEST POINTER" fixo (sempre sobrescreve via identifier diferente)
# Como não dá overwrite garantido, a gente cria sempre um novo e o nome diz "latest".
- name: "Upload LATEST pointer JSON"
  ansible.builtin.shell: |
    set -euo pipefail
    cat > "{{ status_dir }}/{{ current_machine }}/latest-pointer.json" <<EOF
    {
      "deployment_ref": "{{ deployment_ref }}",
      "machine": "{{ current_machine }}",
      "env": "{{ filestore_env }}",
      "status_tag": "{{ status_tag_value }}",
      "json_identifier": "{{ fs_json_identifier }}",
      "log_identifier": "{{ fs_log_identifier }}",
      "timestamp": "{{ ansible_date_time.iso8601 }}"
    }
    EOF

    curl -sS -X POST \
      "{{ harness_base_url }}/ng/api/file-store?accountIdentifier={{ harness_account }}&orgIdentifier={{ harness_org }}&projectIdentifier={{ harness_project }}" \
      -H "x-api-key: {{ harness_pat }}" \
      -F "name={{ (deployment_ref | lower) ~ '-' ~ (current_machine | lower) ~ '-' ~ (filestore_env | lower) ~ '.latest.json' }}" \
      -F "type=FILE" \
      -F "parentIdentifier=Root" \
      -F "identifier={{ (deployment_ref | lower) ~ '-' ~ (current_machine | lower) ~ '-' ~ (filestore_env | lower) ~ '-latest-' ~ ansible_date_time.epoch }}" \
      -F 'tags=[{"key":"status","value":"{{ status_tag_value }}"}]' \
      -F "content=@{{ status_dir }}/{{ current_machine }}/latest-pointer.json"
  args:
    executable: /bin/bash
  no_log: true
  changed_when: false
  failed_when: false

## per machines

- name: "Upload File Store (predeploy)"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ machine_status_file }}"
    log_content: |
      ---- pipeline pre deploy ----
      rc={{ init_parallel_result.rc }}
      {{ init_parallel_result.stdout | default('') }}
      {{ init_parallel_result.stderr | default('') }}
      ---- pipeline pre deploy ----
    status_tag_value: "{{ (deployment_ref | lower) ~ ':pre-deploy:' ~ ('ok' if init_parallel_result.rc == 0 else 'error') }}"
    extra_tags: "{{ [ (deployment_ref | lower) ~ ':deploy:pending' ] if init_parallel_result.rc == 0 else [] }}"
