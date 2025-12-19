apiVersion: v2
name: external-secrets-operator-chart
description: Wrapper chart to install External Secrets Operator
type: application
version: 0.1.0

dependencies:
  - name: external-secrets
    version: 0.10.5
    repository: https://charts.external-secrets.io

###############

apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: external-secrets-operator
  namespace: argocd
spec:
  project: default
  destination:
    server: https://kubernetes.default.svc
    namespace: external-secrets
  source:
    repoURL: https://gitlab.onefiserv.net/latam/latam/merchant-latam/LAC/aws-cd-configuration/amazon-elastic-kubernetes-service.git
    targetRevision: main
    path: common/charts/external-secrets-operator-chart
    helm:
      valueFiles:
        - cluster/dev/external-secrets-operator/values.yaml
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true


##########

external-secrets:
  installCRDs: true

  replicaCount: 2

  serviceAccount:
    create: true
    name: external-secrets

  webhook:
    create: true

  certController:
    create: true

  rbac:
    create: true

  resources:
    requests:
      cpu: 100m
      memory: 128Mi
    limits:
      cpu: 300m
      memory: 256Mi
