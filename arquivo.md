cd /tmp/tmp.U2u184CkUL/elastic-compute-cloud-sitef || exit 1

grep -RIn --exclude-dir=.git "harness_account_id:" .
grep -RIn --exclude-dir=.git "harness_account_id_resolved" .
grep -RIn --exclude-dir=.git "harness_account_id | default('')" .



- name: "Harness | Definir defaults + normalizações"
  ansible.builtin.set_fact:
    harness_account_id_resolved: "{{ lookup('ansible.builtin.env','HARNESS_ACCOUNT_ID') | default('fgDto6qoTT6ctfZS9eWbEw', true) }}"
    harness_org_id_resolved: "{{ lookup('ansible.builtin.env','HARNESS_ORG_ID') | default('Fiserv', true) }}"
    harness_project_id_resolved: "{{ lookup('ansible.builtin.env','HARNESS_PROJECT_ID') | default('sitef', true) }}"
    harness_filestore_root_identifier_resolved: "{{ harness_filestore_root_identifier | default('Root') }}"
    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    env_lower: "{{ (filestore_env | string | trim | lower) }}"
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"
    stage_raw: "{{ (stage_name | default('predeploy')) | string | trim | lower }}"
    stage_label: "{{ {'predeploy':'PRE-DEPLOY','deploy':'DEPLOY','rollback':'ROLLBACK'}.get(stage_raw, stage_raw | upper) }}"
