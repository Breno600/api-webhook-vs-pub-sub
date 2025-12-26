# -------------------------------------------------------------------
# Branch de ROLLBACK
# -------------------------------------------------------------------
- name: "Incluir rollback por máquina"
  ansible.builtin.include_tasks: rollback_per_machine.yml
  when: deploy_action_resolved == 'rollback'
  vars:
    deployment_ref: "{{ deployment_ref_resolved }}"
    machine_name: "{{ machine_name_resolved }}"
    repo_root: "{{ repo_root_resolved }}"
    status_dir: "{{ status_dir_resolved }}"
    filestore_env: "{{ filestore_env_resolved }}"
    filestore_base_dir: "{{ filestore_base_dir_resolved }}"

    # ✅ IMPORTANTE (rollback baixa componentes do Nexus)
    nexus_base_url: "{{ nexus_base_url_resolved }}"
    nexus_user: "{{ nexus_user_resolved }}"
    nexus_password: "{{ nexus_password_resolved }}"

    # ✅ IMPORTANTE (p/ upload no Harness cair em ROLLBACK)
    stage_name: "rollback"



- name: "Upload JSON + LOG do ROLLBACK para Harness File Store (via Ansible) - SEMPRE"
  ansible.builtin.include_tasks: harness_filestore_upload.yml
  vars:
    current_machine: "{{ current_machine }}"
    deployment_ref: "{{ deployment_ref }}"
    filestore_env: "{{ filestore_env }}"
    stage_name: "rollback"

    machine_status_file: "{{ machine_status_file }}"
    log_content: "{{ rollback_log_content }}"
    status_tag_value: "{{ (deployment_ref | lower) ~ ':rollback:' ~ ('ok' if init_rollback_result.rc == 0 else 'error') }}"
    extra_tags: []



ansible-playbook \
  -i localhost, \
  ansible/deploy_from_status.yml \
  -e "machine_name=${m}" \
  -e "deployment_ref=${GIT_TAG}" \
  -e "deploy_action=${STRATEGY}" \
  -e "filestore_env=${FILESTORE_ENV}" \
  -e "filestore_base_dir=${FILESTORE_BASE_DIR}" \
  -e "harness_api_key_override=${HARNESS_API_KEY}" \
  -e "nexus_base_url=${NEXUS_BASE_URL}" \
  -e "nexus_user=${NEXUS_USER}" \
  -e "nexus_password=${NEXUS_PASSWORD}"
