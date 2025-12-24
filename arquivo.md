- name: Harness | Garantir run_id e URL
  when: hf_can_upload
  ansible.builtin.set_fact:
    hf_run_id: "{{ hf_run_id | default(run_id | default(lookup('pipe','date +%s'), true), true) }}"
    hf_url: "{{ hf_endpoint }}/ng/api/file-store?accountIdentifier={{ hf_account }}&orgIdentifier={{ hf_org }}&projectIdentifier={{ hf_project }}"


- name: Harness | Calcular nomes/identifiers (raw + names)
  when: hf_can_upload
  ansible.builtin.set_fact:
    hf_log_name: "{{ hf_env }}__{{ hf_deploy_ref }}__{{ hf_machine }}__{{ hf_stage }}__{{ hf_run_id }}.log"
    hf_status_name: "{{ hf_env }}__{{ hf_deploy_ref }}__{{ hf_machine }}__{{ hf_stage }}__{{ hf_run_id }}.json"
    _ident_log_raw: "{{ (hf_deploy_ref | lower) ~ '_' ~ (hf_machine | lower) ~ '_' ~ (hf_env | lower) ~ '_' ~ (hf_stage | lower) ~ '_' ~ (hf_run_id | string) ~ '_log' }}"
    _ident_stat_raw: "{{ (hf_deploy_ref | lower) ~ '_' ~ (hf_machine | lower) ~ '_' ~ (hf_env | lower) ~ '_' ~ (hf_stage | lower) ~ '_' ~ (hf_run_id | string) ~ '_status' }}"

- name: Harness | Sanitizar identifiers (prefix + chars permitidos)
  when: hf_can_upload
  ansible.builtin.set_fact:
    _ident_log_pref: "{{ 'id_' ~ (_ident_log_raw  | regex_replace('[^a-z0-9_$]', '_')) }}"
    _ident_stat_pref: "{{ 'id_' ~ (_ident_stat_raw | regex_replace('[^a-z0-9_$]', '_')) }}"

- name: Harness | Definir identifiers finais (limite 128)
  when: hf_can_upload
  ansible.builtin.set_fact:
    hf_log_identifier: >-
      {{
        (_ident_log_pref[0:110] ~ '_' ~ (_ident_log_pref | hash('sha1'))[0:16])
        if (_ident_log_pref | length) > 128 else _ident_log_pref
      }}
    hf_status_identifier: >-
      {{
        (_ident_stat_pref[0:110] ~ '_' ~ (_ident_stat_pref | hash('sha1'))[0:16])
        if (_ident_stat_pref | length) > 128 else _ident_stat_pref
      }}
