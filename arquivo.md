- name: "Harness | Definir defaults + normalizações (parte 1)"
  ansible.builtin.set_fact:
    harness_account_id_resolved: "{{ harness_account_id | default('fgDto6qoTT6ctfZS9eWbEw') }}"
    harness_org_id_resolved: "{{ harness_org_id | default('Fiserv') }}"
    harness_project_id_resolved: "{{ harness_project_id | default('sitef') }}"
    harness_filestore_root_identifier_resolved: "{{ harness_filestore_root_identifier | default('Root') }}"
    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    env_lower: "{{ (filestore_env | string | trim | lower) }}"
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"
    stage_raw: "{{ (stage_name | default('predeploy')) | string | trim | lower }}"

- name: "Harness | Definir stage_label (parte 2)"
  ansible.builtin.set_fact:
    stage_label: "{{ {'predeploy':'PRE-DEPLOY','deploy':'DEPLOY','rollback':'ROLLBACK'}.get(stage_raw, stage_raw | upper) }}"
