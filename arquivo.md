- name: Harness | Calcular nomes/identifiers
  when: hf_can_upload
  ansible.builtin.set_fact:
    # base (pode ter '-' etc)
    _ident_log_raw:   "{{ hf_deploy_ref | lower }}_{{ hf_machine }}_{{ hf_env }}_{{ hf_stage }}_{{ hf_run_id }}_log"
    _ident_stat_raw:  "{{ hf_deploy_ref | lower }}_{{ hf_machine }}_{{ hf_env }}_{{ hf_stage }}_{{ hf_run_id }}_status"

    # sanitiza: mantém só [a-z0-9_$] e troca resto por _
    _ident_log_clean:  "{{ _ident_log_raw  | regex_replace('[^a-z0-9_$]', '_') }}"
    _ident_stat_clean: "{{ _ident_stat_raw | regex_replace('[^a-z0-9_$]', '_') }}"

    # prefixo garante que nunca começa com número/$
    _ident_log_pref:  "{{ 'id_' ~ _ident_log_clean }}"
    _ident_stat_pref: "{{ 'id_' ~ _ident_stat_clean }}"

    # aplica limite 128 com hash se precisar
    hf_ident_log: >-
      {{
        (_ident_log_pref[0:110] ~ '_' ~ (_ident_log_pref | hash('sha1'))[0:16])
        if (_ident_log_pref | length) > 128 else _ident_log_pref
      }}
    hf_ident_status: >-
      {{
        (_ident_stat_pref[0:110] ~ '_' ~ (_ident_stat_pref | hash('sha1'))[0:16])
        if (_ident_stat_pref | length) > 128 else _ident_stat_pref
      }}
