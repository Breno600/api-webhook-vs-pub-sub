predeploy_per_machine.yml

# -----------------------------------------------------------------------------
# 25) Upload SEMPRE + Falhar só DEPOIS
# -----------------------------------------------------------------------------
- name: "Upload File Store (predeploy) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ machine_status_file }}"
    log_content: |
      ---- pipeline pre deploy ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=predeploy
      rc={{ init_parallel_result.rc | default(1) }}

      ---- stdout ----
      {{ init_parallel_result.stdout | default('') }}

      ---- stderr ----
      {{ init_parallel_result.stderr | default('') }}
      ---- pipeline pre deploy ----
    status_tag_value: "{{ (deployment_ref | lower) ~ ':pre-deploy:' ~ ('ok' if init_parallel_result.rc == 0 else 'error') }}"
    extra_tags: "{{ [ (deployment_ref | lower) ~ ':deploy:pending' ] if init_parallel_result.rc == 0 else [] }}"

- name: "Falhar se init_parallel.sh retornou erro (DEPOIS do upload)"
  ansible.builtin.fail:
    msg: "init_parallel.sh falhou na máquina {{ current_machine }} com rc={{ init_parallel_result.rc }}"
  when: init_parallel_result.rc != 0


rollback_per_machine.yml
# -----------------------------------------------------------------------------
# 25) Upload SEMPRE + Falhar só DEPOIS (rollback)
# -----------------------------------------------------------------------------
- name: "Montar conteúdo do log do ROLLBACK"
  ansible.builtin.set_fact:
    rollback_log_content: |
      ---- pipeline rollback ----
      deployment_ref={{ deployment_ref | default('') }}
      machine={{ current_machine }}
      host={{ machine_cfg.host | default(machine_cfg.ip | default('')) }}
      stage=rollback
      rc={{ init_rollback_result.rc | default(1) }}

      ---- stdout ----
      {{ init_rollback_result.stdout | default('') }}

      ---- stderr ----
      {{ init_rollback_result.stderr | default('') }}
      ---- pipeline rollback ----

- name: "Upload JSON + LOG do ROLLBACK para Harness File Store (via Ansible) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    machine_status_file: "{{ machine_status_file }}"
    log_content: "{{ rollback_log_content }}"
    status_tag_value: "{{ (deployment_ref | lower) ~ ':rollback:' ~ ('ok' if init_rollback_result.rc == 0 else 'error') }}"
    extra_tags: []

- name: "Falhar se init_rollback.sh retornou erro (DEPOIS do upload)"
  ansible.builtin.fail:
    msg: "init_rollback.sh falhou na máquina {{ current_machine }} com rc={{ init_rollback_result.rc }}"
  when: init_rollback_result.rc != 0

# harness_filestore_upload.yml
deployment_ref_folder: "{{ deployment_ref | string | trim }}"

-F "name={{ deployment_ref_folder }}" \
-F "type=FOLDER" \
-F "parentIdentifier={{ env_lower }}" \
-F "identifier={{ deployment_ref_folder }}" \

parentIdentifier={{ deployment_ref_folder }}

