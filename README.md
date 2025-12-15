---
# =====================================================================================
# HARNESS FILE STORE - UPLOAD (por máquina)
# =====================================================================================

- name: "Harness | Validar vars mínimas"
  ansible.builtin.assert:
    that:
      - current_machine is defined
      - deployment_ref is defined
      - filestore_env is defined
      - machine_status_file is defined
      - log_content is defined
      - status_tag_value is defined

- name: "Harness | Defaults"
  ansible.builtin.set_fact:
    harness_account_id_resolved: "fgDto6qoTT6ctfZS9eWbEw"
    harness_org_id_resolved: "Fiserv"
    harness_project_id_resolved: "sitef"
    harness_filestore_root_identifier_resolved: "Root"
    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    env_lower: "{{ filestore_env | lower }}"
    deployment_ref_lower: "{{ deployment_ref | lower }}"

- name: "Harness | Identifiers SAFE (HARNESS RULE)"
  ansible.builtin.set_fact:
    env_identifier: "{{ env_lower | regex_replace('[^a-zA-Z0-9_]', '_') }}"
    deployment_ref_identifier: "{{ deployment_ref_lower | regex_replace('[^a-zA-Z0-9_]', '_') }}"
    machine_identifier: "{{ current_machine | lower | regex_replace('[^a-zA-Z0-9_]', '_') }}"

- name: "Harness | Resolver token"
  ansible.builtin.set_fact:
    harness_x_api_key: "{{ lookup('env','HARNESS_X_API_KEY') }}"
  no_log: true

- name: "Harness | Tags"
  ansible.builtin.set_fact:
    harness_tags:
      - { key: "env", value: "{{ env_lower }}" }
      - { key: "deployment", value: "{{ deployment_ref_lower }}" }
      - { key: "machine", value: "{{ current_machine | lower }}" }
      - { key: "tag", value: "{{ status_tag_value }}" }

# =============================================================================
# FOLDER ENV
# =============================================================================
- name: "Harness | Create ENV folder"
  shell: |
    curl -s -o /tmp/env.out -w "%{http_code}" \
      -X POST "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ env_lower }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ harness_filestore_root_identifier_resolved }}" \
      -F "identifier={{ env_identifier }}" \
      -F "tags={{ harness_tags | to_json }}"
  register: env_http
  failed_when: false

# =============================================================================
# FOLDER DEPLOYMENT
# =============================================================================
- name: "Harness | Create DEPLOYMENT folder"
  shell: |
    curl -s -o /tmp/ref.out -w "%{http_code}" \
      -X POST "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ deployment_ref }}" \
      -F "type=FOLDER" \
      -F "parentIdentifier={{ env_identifier }}" \
      -F "identifier={{ deployment_ref_identifier }}" \
      -F "tags={{ harness_tags | to_json }}"
  register: ref_http
  failed_when: false

# =============================================================================
# STATUS FILE
# =============================================================================
- name: "Harness | Status identifiers"
  ansible.builtin.set_fact:
    hfs_status_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.json"
    hfs_status_identifier: "{{ deployment_ref_identifier }}_{{ machine_identifier }}_{{ env_identifier }}_json"

- name: "Harness | Upload STATUS"
  shell: |
    curl -s -o /tmp/status.out -w "%{http_code}" \
      -X POST "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_status_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ deployment_ref_identifier }}" \
      -F "identifier={{ hfs_status_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@{{ machine_status_file }}"
  register: status_http
  failed_when: status_http.stdout not in ['200','201','202','409']

# =============================================================================
# LOG FILE
# =============================================================================
- name: "Harness | Log identifiers"
  ansible.builtin.set_fact:
    hfs_log_name: "{{ deployment_ref_lower }}-{{ current_machine | lower }}-{{ env_lower }}.log"
    hfs_log_identifier: "{{ deployment_ref_identifier }}_{{ machine_identifier }}_{{ env_identifier }}_log"

- name: "Harness | Criar arquivo temporário log"
  copy:
    dest: "/tmp/{{ hfs_log_name }}"
    content: "{{ log_content }}"
    mode: "0600"

- name: "Harness | Upload LOG"
  shell: |
    curl -s -o /tmp/log.out -w "%{http_code}" \
      -X POST "{{ harness_api_base }}?accountIdentifier={{ harness_account_id_resolved }}&orgIdentifier={{ harness_org_id_resolved }}&projectIdentifier={{ harness_project_id_resolved }}" \
      -H "x-api-key: {{ harness_x_api_key }}" \
      -F "name={{ hfs_log_name }}" \
      -F "type=FILE" \
      -F "parentIdentifier={{ deployment_ref_identifier }}" \
      -F "identifier={{ hfs_log_identifier }}" \
      -F "tags={{ harness_tags | to_json }}" \
      -F "content=@/tmp/{{ hfs_log_name }}"
  register: log_http
  failed_when: log_http.stdout not in ['200','201','202','409']

- name: "Cleanup log tmp"
  file:
    path: "/tmp/{{ hfs_log_name }}"
    state: absent
