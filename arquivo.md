- name: "Harness | Definir defaults + normalizações (parte 1)"
  ansible.builtin.set_fact:
    # Overrides seguros (não use harness_account_id/harness_org_id/harness_project_id aqui)
    harness_account_id_resolved: >-
      {{
        (harness_account_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_ACCOUNT_ID') | default('', true) | string | trim), true)
          | default('fgDto6qoTT6ctfZS9eWbEw', true)
      }}
    harness_org_id_resolved: >-
      {{
        (harness_org_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_ORG_ID') | default('', true) | string | trim), true)
          | default('Fiserv', true)
      }}
    harness_project_id_resolved: >-
      {{
        (harness_project_id_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_PROJECT_ID') | default('', true) | string | trim), true)
          | default('sitef', true)
      }}
    harness_filestore_root_identifier_resolved: >-
      {{
        (harness_filestore_root_identifier_override | default('', true) | string | trim)
          | default((lookup('ansible.builtin.env','HARNESS_FILESTORE_ROOT_IDENTIFIER') | default('', true) | string | trim), true)
          | default('Root', true)
      }}

    harness_api_base: "https://harness.onefiserv.net/ng/api/file-store"

    env_lower: "{{ (filestore_env | string | trim | lower) }}"
    deployment_ref_folder: "{{ (deployment_ref_folder | default(deployment_ref)) | string | trim }}"
    deployment_ref_lower: "{{ (deployment_ref | string | trim | lower) }}"
    stage_raw: "{{ (stage_name | default('predeploy')) | string | trim | lower }}"
