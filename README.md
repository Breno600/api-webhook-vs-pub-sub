[2025-09-02 07:42:40] Montando JSON final: auditoria_gitlab.json
jq: error (at /tmp/tmp.WfgntaawLl/projects.json:30548): Cannot index array with string "apm_id"
[ERRO] rc=5 | linha 0 | cmd: jq -s '
  def known: ["success","failed","running","canceled","skipped","manual","pending","created","scheduled","none"];
  def empty_counts: {success:0, failed:0, running:0, canceled:0, skipped:0, manual:0, pending:0, created:0, scheduled:0, none:0, other:0};
  def norm(s): (known | index(s)) as $i | if $i==null then "other" else s end;

  sort_by(.apm_id)
  | group_by(.apm_id)
  | map({
      apm_id: (.[0].apm_id),
      total_projects: (length),
      with_general_pipeline: ([.[] | select(.pipelines.has_general_pipeline==true)] | length),
      with_APP_NAME_or_APP_VERSION: ([.[] | select(.pipelines.has_APP_NAME_or_APP_VERSION==true)] | length),
      status_counts:
        (reduce (.[] | (.pipelines.latest?.status // "none") | norm(.)) as $s
          (empty_counts; .[$s] += 1))
    })
' "$PROJECTS_JSON" > "$APM_SUMMARY_JSON"
