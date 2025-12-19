#!/usr/bin/env bash
set -Eeuo pipefail

REPORT="${REPORT:-eso_report.txt}"
APP_NS="${APP_NS:-omnidata}"
CSS_NAME="${CSS_NAME:-omnidata-external-secrets-clustersecretstore}"

# -------- helpers ----------
sep() { echo -e "\n\n==================== $* ====================\n"; }

run() {
  local title="$1"; shift
  sep "${title}"
  echo "+ $*"
  "$@" 2>&1 || true
}

run_sh() {
  local title="$1"; shift
  sep "${title}"
  echo "+ $*"
  bash -lc "$*" 2>&1 || true
}

redact_secret_yaml() {
  # Redige values em `.data:` e `.stringData:` (sem yq/jq)
  awk '
    BEGIN{inData=0}
    /^[^[:space:]]/ {inData=0}
    /^[[:space:]]*(data|stringData):[[:space:]]*$/ {print; inData=1; next}
    inData==1 && match($0, /^[[:space:]]+[A-Za-z0-9_.-]+:[[:space:]]*/) {
      split($0,a,":"); key=a[1];
      sub(/^[[:space:]]+/,"",key);
      sub(/[[:space:]]+$/,"",key);
      indent=match($0,/^[[:space:]]+/)?substr($0,RSTART,RLENGTH):"  ";
      print indent key ": <redacted>";
      next
    }
    {print}
  '
}

# -------- start ----------
: > "${REPORT}"
exec > >(tee -a "${REPORT}") 2>&1

echo "Report generated at: ${REPORT}"
echo "Date: $(date -Is)"
echo "APP_NS=${APP_NS}"
echo "CSS_NAME=${CSS_NAME}"

# -------- 0) Context / cluster ----------
run "00-current-context" kubectl config current-context
run "01-cluster-info" kubectl cluster-info
run "02-kubectl-version" kubectl version --short

# -------- 1) CRDs / API resources ----------
run_sh "10-crds-eso" "kubectl get crd | egrep -i 'external-secrets|externalsecret|secretstore|clustersecretstore|clusterexternalsecret' || true"
run_sh "11-api-resources-eso" "kubectl api-resources | egrep -i 'externalsecret|secretstore|clustersecretstore|application' || true"

# -------- 2) Where is the operator running? ----------
run_sh "20-find-deploy-eso" "kubectl get deploy -A | egrep -i 'external-secrets|externalsecrets|\\beso\\b' || true"
run_sh "21-find-pods-eso"  "kubectl get pods  -A | egrep -i 'external-secrets|externalsecrets|\\beso\\b' || true"
run_sh "22-find-svc-eso"   "kubectl get svc   -A | egrep -i 'external-secrets|externalsecrets|\\beso\\b' || true"

# detect ESO deploys
mapfile -t ESO_DEPLOYS < <(kubectl get deploy -A --no-headers 2>/dev/null | awk '
  tolower($0) ~ /external-secrets|externalsecrets|(^|[[:space:]])eso([[:space:]]|$)/ {print $1 "/" $2}
' || true)

if [[ ${#ESO_DEPLOYS[@]} -eq 0 ]]; then
  mapfile -t ESO_DEPLOYS < <(kubectl get deploy -A -l app.kubernetes.io/name=external-secrets --no-headers 2>/dev/null | awk '{print $1 "/" $2}' || true)
fi

sep "23-eso-deploys-detected"
if [[ ${#ESO_DEPLOYS[@]} -gt 0 ]]; then
  printf "%s\n" "${ESO_DEPLOYS[@]}"
else
  echo "<none detected>"
fi

# Describe + logs for each detected deploy
if [[ ${#ESO_DEPLOYS[@]} -gt 0 ]]; then
  i=0
  for ns_name in "${ESO_DEPLOYS[@]}"; do
    i=$((i+1))
    NS="${ns_name%%/*}"
    DEP="${ns_name##*/}"

    run "30-${i}-eso-all-in-ns-${NS}" kubectl get all -n "${NS}"
    run "31-${i}-eso-deploy-describe-${NS}/${DEP}" kubectl describe deploy -n "${NS}" "${DEP}"
    run "32-${i}-eso-logs-tail-${NS}/${DEP}" kubectl logs -n "${NS}" "deploy/${DEP}" --tail=400
    run "33-${i}-eso-logs-since-60m-${NS}/${DEP}" kubectl logs -n "${NS}" "deploy/${DEP}" --since=60m
    run "34-${i}-eso-events-${NS}" kubectl get events -n "${NS}" --sort-by=.lastTimestamp
  done
else
  sep "30-eso-logs"
  echo "Nenhum deploy do external-secrets detectado automaticamente."
  echo "Se você souber o namespace, rode:"
  echo "  kubectl get deploy -n <ns>"
  echo "  kubectl logs -n <ns> deploy/<nome>"
fi

# -------- 3) ClusterSecretStore / SecretStore ----------
run "40-clustersecretstore-list" kubectl get clustersecretstore -o wide
run "41-clustersecretstore-describe" kubectl describe clustersecretstore "${CSS_NAME}"
run_sh "42-clustersecretstore-status-grep" "kubectl get clustersecretstore ${CSS_NAME} -o yaml | egrep -n 'status:|conditions:|message:|reason:|type:|status:' || true"

# -------- 4) App namespace objects ----------
run "50-externalsecret-list-${APP_NS}" kubectl get externalsecret -n "${APP_NS}" -o wide
run "51-externalsecret-yaml-${APP_NS}" kubectl get externalsecret -n "${APP_NS}" -o yaml
run "52-events-${APP_NS}" kubectl get events -n "${APP_NS}" --sort-by=.lastTimestamp
run "53-secrets-list-${APP_NS}" kubectl get secret -n "${APP_NS}" -o wide

# Describe each ExternalSecret
mapfile -t ES_NAMES < <(kubectl get externalsecret -n "${APP_NS}" --no-headers 2>/dev/null | awk '{print $1}' || true)
if [[ ${#ES_NAMES[@]} -gt 0 ]]; then
  j=0
  for es in "${ES_NAMES[@]}"; do
    j=$((j+1))
    run "54-${j}-externalsecret-describe-${APP_NS}/${es}" kubectl describe externalsecret -n "${APP_NS}" "${es}"
  done
else
  sep "54-externalsecret-describe"
  echo "Nenhum ExternalSecret encontrado em ${APP_NS}."
fi

# Secrets expected (adjust as needed)
EXPECTED_SECRETS=(application-certs transaction-data-rules-v2-env)

sep "60-expected-secrets-details-${APP_NS}"
for sec in "${EXPECTED_SECRETS[@]}"; do
  echo
  echo "---- Secret: ${APP_NS}/${sec} ----"
  if kubectl get secret -n "${APP_NS}" "${sec}" >/dev/null 2>&1; then
    echo "[OK] exists"
    echo "-- describe --"
    kubectl describe secret -n "${APP_NS}" "${sec}" 2>&1 || true
    echo
    echo "-- yaml (redacted) --"
    kubectl get secret -n "${APP_NS}" "${sec}" -o yaml 2>&1 | redact_secret_yaml || true
  else
    echo "[MISSING] does not exist"
  fi
done

# -------- 5) Pods that are failing because secret not found (helpful) ----------
run_sh "70-pods-failedmount-secret-not-found-${APP_NS}" \
  "kubectl get events -n ${APP_NS} --sort-by=.lastTimestamp | egrep -i 'FailedMount|secret.*not found|MountVolume|application-certs' || true"

# -------- 6) Argo Application objects (if CRD exists) ----------
if kubectl api-resources 2>/dev/null | awk '{print $1}' | grep -qE '^applications$'; then
  run "80-argocd-apps-all" kubectl get applications -A
  run_sh "81-argocd-apps-filter-eso" "kubectl get applications -A | egrep -i 'external-secret|external-secrets|cdr|operator' || true"
else
  sep "80-argocd-apps"
  echo "CRD 'applications' não encontrada (Argo Application). Pulando."
fi

# -------- Summary ----------
sep "_SUMMARY"
echo "Se quiser colar aqui no chat, cole o conteúdo do arquivo: ${REPORT}"
echo "Comando:"
echo "  cat ${REPORT}"

echo
echo "✅ Pronto. Arquivo gerado: ${REPORT}"
echo "Agora rode: cat ${REPORT}  (e cole aqui)"
