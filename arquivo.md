# -----------------------------------------------------------------------------
# Identifiers SIMPLES: usar identifier = name (isso evita lookup e bate com o que já existe)
# Regras Harness: [A-Za-z][A-Za-z0-9_]{0,127}
# -----------------------------------------------------------------------------

- name: "Harness | Identifiers (RAW)"
  ansible.builtin.set_fact:
    env_identifier_final: "{{ env_lower }}"

    deployment_ref_identifier_raw: "{{ deployment_ref_folder | regex_replace('[^A-Za-z0-9_]', '_') }}"

    leaf_identifier_raw: "{{ (machine_stage_name | lower) | regex_replace('[^A-Za-z0-9_]', '_') }}"

- name: "Harness | Identifiers (FINAL a partir do RAW)"
  ansible.builtin.set_fact:
    deployment_ref_identifier_final: >-
      {{
        deployment_ref_identifier_raw
        if (deployment_ref_identifier_raw | regex_search('^[A-Za-z]'))
        else ('d_' ~ deployment_ref_identifier_raw)
      }}

    leaf_identifier_raw2: >-
      {{
        leaf_identifier_raw
        if (leaf_identifier_raw | regex_search('^[A-Za-z]'))
        else ('p_' ~ leaf_identifier_raw)
      }}

- name: "Harness | Limitar leaf_identifier a 128 (se precisar)"
  ansible.builtin.set_fact:
    leaf_identifier_final: >-
      {{
        (leaf_identifier_raw2[0:110] ~ '_' ~ (leaf_identifier_raw2 | hash('sha1'))[0:16])
        if (leaf_identifier_raw2 | length) > 128 else leaf_identifier_raw2
      }}
