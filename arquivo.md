# cluster/dev/external-secrets-operator/values.yaml

# ESSENCIAL: instala os CRDs (ExternalSecret, SecretStore, ClusterSecretStore, etc)
installCRDs: true

# Boa prática: 2 réplicas do controller
replicaCount: 2

# ServiceAccount do operator (NÃO é a mesma SA usada no jwt.serviceAccountRef do SecretStore)
serviceAccount:
  create: true
  name: external-secrets
  annotations: {}

# Webhook / Cert controller (recomendado deixar ligado)
webhook:
  create: true

certController:
  create: true

# RBAC cluster-wide (precisa pra ClusterSecretStore)
rbac:
  create: true

# (opcional) recursos
resources:
  requests:
    cpu: 100m
    memory: 128Mi
  limits:
    cpu: 300m
    memory: 256Mi

# (opcional) métricas
serviceMonitor:
  enabled: false
