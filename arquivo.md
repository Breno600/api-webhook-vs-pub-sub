#!/usr/bin/env bash
set -Eeuo pipefail

TS="$(date +%Y%m%d_%H%M%S)"
OUT_DIR="${OUT_DIR:-eso-debug-${TS}}"
APP_NS="${APP_NS:-omnidata}"
CSS_NAME="${CSS_NAME:-omnidata-external-secrets-clustersecretstore}"

mkdir -p "${OUT_DIR}"
LOG="${OUT_DIR}/run.log"

# -------- helpers ----------
run() {
  local name="$1"; shift
  {
    echo
    echo "==================== ${name} ===================="
    echo "+ $*"
    "$@"
  } |& tee -a "${LOG}" > "${OUT_DIR}/${name}.txt" || true
}

run_sh() {
  local name="$1"; shift
  {
    echo
    echo "==================== ${name} ===================="
    echo "+ $*"
    bash -lc "$*"
  } |& tee -a "${LOG}" > "${OUT_DIR}/${name}.txt" || true
}

redact_secret_yaml() {
  # Redige values em `.data:` e `.stringData:` (sem precisar de yq/jq)
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

echo "Output dir: ${OUT_DIR}" | tee "${LOG}"
echo "App namespace: ${APP_NS}" | tee -a "${LOG}"
echo "ClusterSecretStore: ${CSS_NAME}" | tee -a "${LOG}"

# -------- 0) contexto ----------
run "00-context-current" kubectl config current-context
run "01-context-cluster-info" kubectl cluster-info
run "02-version" kubectl version --short

# -------- 1) CRDs / API resources ----------
run_sh "10-crds-eso" "kubectl get crd | egrep -i 'external-secrets|externalsecret|secretstore|clustersecretstore|clusterexternalsecret' || true"
run_sh "11-api-resources-eso" "kubectl api-resources | egrep -i 'externalsecret|secretstore|clustersecretstore|application' || true"

# -------- 2) descobrir onde o operator roda (deploy/pods/svc) ----------
run_sh "20-deploy-find-eso" "kubectl get deploy -A | egrep -i 'external-secrets|externalsecrets|\\beso\\b' || true"
run_sh "21-pods-find-eso"  "kubectl get pods  -A | egrep -i 'external-secrets|externalsecrets|\\beso\\b' || true"
run_sh "22-svc-find-eso"   "kubectl get svc   -A | egrep -i 'external-secrets|externalsecrets|\\beso\\b' || true"

# tenta inferir namespace(s) e deploy(s) do controller
mapfile -t ESO_DEPLOYS < <(kubectl get deploy -A --no-headers 2>/dev/null | awk '
  tolower($0) ~ /external-secrets|externalsecrets|(^|[[:space:]])eso([[:space:]]|$)/ {print $1 "/" $2}
' || true)

# fallback: procurar por label padrão
if [[ ${#ESO_DEPLOYS[@]} -eq 0 ]]; then
  mapfile -t ESO_DEPLOYS < <(kubectl get deploy -A -l app.kubernetes.io/name=external-secrets --no-headers 2>/dev/null | awk '{print $1 "/" $2}' || true)
fi

printf "%s\n" "${ESO_DEPLOYS[@]:-}" > "${OUT_DIR}/23-eso-deploys-detected.txt" || true

# -------- 3) coletar detalhes e logs do operator ----------
if [[ ${#ESO_DEPLOYS[@]} -gt 0 ]]; then
  i=0
  for ns_name in "${ESO_DEPLOYS[@]}"; do
    i=$((i+1))
    NS="${ns_name%%/*}"
    DEP="${ns_name##*/}"

    run "30-${i}-eso-all-${NS}" kubectl get all -n "${NS}"
    run "31-${i}-eso-deploy-${NS}-${DEP}" kubectl describe deploy -n "${NS}" "${DEP}"

    # logs do deploy (tail e since)
    run "32-${i}-eso-logs-tail-${NS}-${DEP}" kubectl logs -n "${NS}" "deploy/${DEP}" --tail=300
    run "33-${i}-eso-logs-since-${NS}-${DEP}" kubectl logs -n "${NS}" "deploy/${DEP}" --since=60m

    run "34-${i}-eso-events-${NS}" kubectl get events -n "${NS}" --sort-by=.lastTimestamp
  done
else
  echo "Nenhum deploy do external-secrets detectado automaticamente." | tee -a "${LOG}"
fi

# -------- 4) ClusterSecretStore / SecretStore ----------
run "40-clustersecretstore-list" kubectl get clustersecretstore -o wide
run "41-clustersecretstore-describe" kubectl describe clustersecretstore "${CSS_NAME}"
run_sh "42-clustersecretstore-status-grep" "kubectl get clustersecretstore ${CSS_NAME} -o yaml | egrep -n 'status:|conditions:|message:|reason:|type:|status:' || true"

# -------- 5) ExternalSecrets da aplicação ----------
run "50-externalsecret-list-${APP_NS}" kubectl get externalsecret -n "${APP_NS}" -o wide
run "51-externalsecret-yaml-${APP_NS}" kubectl get externalsecret -n "${APP_NS}" -o yaml
run "52-events-${APP_NS}" kubectl get events -n "${APP_NS}" --sort-by=.lastTimestamp

# descreve todos os ExternalSecrets do namespace (um por arquivo)
mapfile -t ES_NAMES < <(kubectl get externalsecret -n "${APP_NS}" --no-headers 2>/dev/null | awk '{print $1}' || true)
j=0
for es in "${ES_NAMES[@]:-}"; do
  j=$((j+1))
  run "53-${j}-externalsecret-describe-${APP_NS}-${es}" kubectl describe externalsecret -n "${APP_NS}" "${es}"
done

# -------- 6) Secrets gerados (redigidos) ----------
# principais (podem não existir ainda)
for sec in application-certs transaction-data-rules-v2-env; do
  if kubectl get secret -n "${APP_NS}" "${sec}" >/dev/null 2>&1; then
    kubectl get secret -n "${APP_NS}" "${sec}" -o yaml | redact_secret_yaml > "${OUT_DIR}/60-secret-${APP_NS}-${sec}-redacted.yaml" || true
    kubectl describe secret -n "${APP_NS}" "${sec}" > "${OUT_DIR}/61-secret-${APP_NS}-${sec}-describe.txt" || true
  else
    echo "Secret ${APP_NS}/${sec} não existe." > "${OUT_DIR}/60-secret-${APP_NS}-${sec}-missing.txt"
  fi
done

run "62-secrets-list-${APP_NS}" kubectl get secret -n "${APP_NS}" -o wide

# -------- 7) Ver Apps (Argo CD / Harness GitOps) se existir CRD Application ----------
if kubectl api-resources 2>/dev/null | awk '{print $1}' | grep -qE '^applications$'; then
  run "70-argocd-apps-all" kubectl get applications -A
  # tenta achar apps relacionadas a external secret/operator
  run_sh "71-argocd-apps-filter" "kubectl get applications -A | egrep -i 'external-secret|external-secrets|cdr' || true"
else
  echo "CRD 'applications' não encontrada (Argo Application). Pulando seção 7." > "${OUT_DIR}/70-argocd-apps-skip.txt"
fi

# -------- 8) salvar um resumo rápido ----------
{
  echo "OUT_DIR=${OUT_DIR}"
  echo "APP_NS=${APP_NS}"
  echo "CSS_NAME=${CSS_NAME}"
  echo "Detected ESO deploys:"
  printf " - %s\n" "${ESO_DEPLOYS[@]:-<none>}"
  echo
  echo "Tip: compacte e envie a pasta:"
  echo "  tar -czf ${OUT_DIR}.tar.gz ${OUT_DIR}"
} > "${OUT_DIR}/_SUMMARY.txt"

echo
echo "✅ Coleta finalizada."
echo "📁 Pasta: ${OUT_DIR}"
echo "📌 Resumo: ${OUT_DIR}/_SUMMARY.txt"
echo
echo "Para compactar:"
echo "  tar -czf ${OUT_DIR}.tar.gz ${OUT_DIR}"
