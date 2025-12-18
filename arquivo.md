# apps/omnidata/external-secrets/dev/secrets-values.dev.yaml

secretStore:
  # IMPORTANTE: esse nome tem que bater com o metadata.name do ClusterSecretStore gerado:
  # metadata.name: {{ include "chart.fullname" . }}-clustersecretstore
  #
  # Se seu release/chart fullname for "omnidata-external-secrets", então ficaria:
  name: omnidata-external-secrets-clustersecretstore
  kind: ClusterSecretStore

secrets:
  enabled: true

  # Ex: 15m, 30m, 1h
  refreshInterval: 1h

  # Para AWS Secrets Manager:
  service: SecretsManager
  # Se fosse SSM Parameter Store seria: ParameterStore
  region: sa-east-1

  # ServiceAccount que o ClusterSecretStore vai referenciar (IRSA normalmente)
  serviceaccount:
    name: external-secrets-sa
    namespace: external-secrets

  # Nome do Secret que vai ser criado no namespace da sua app
  k8sSecretName: omnidata-app-secrets

  # Cada item vira uma key dentro do Secret do Kubernetes
  # remoteRef.key = nome/ARN do secret no Secrets Manager (ou path no Parameter Store)
  mapping:
    - secretKey: DB_PASSWORD
      ssm:
        key: omnidata/dev/db_password
        # version: 1   # opcional (SSM costuma ser número; SM pode variar)

    - secretKey: API_KEY
      ssm:
        key: omnidata/dev/api_key

    - secretKey: REDIS_PASSWORD
      ssm:
        key: omnidata/dev/redis_password