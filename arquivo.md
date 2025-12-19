#  clustersecretstore.yaml
apiVersion: external-secrets.io/v1beta1
kind: ClusterSecretStore
metadata:
  name: {{ include "chart.fullname" . }}-clustersecretstore
spec:
  provider:
    aws:
      service: {{ .Values.secrets.service }}
      region: {{ .Values.secrets.region }}
      auth:
        jwt:
          serviceAccountRef:
            name:  {{ .Values.secrets.serviceaccount.name }}
            namespace: {{ .Values.secrets.serviceaccount.namespace }} 

# secrets-values.dev.yaml
# apps/omnidata/external-secrets/dev/secrets-values.dev.yaml

secretStore:
  name: omnidata-external-secrets-clustersecretstore
  kind: ClusterSecretStore

secrets:
  enabled: true
  refreshInterval: 1h

  service: SecretsManager

  region: us-east-1

  serviceaccount:
    name: external-secrets-us
    namespace: external-secrets

  k8sSecretName: omnidata-app-secrets
  
  mapping:
    - secretKey: DB_PASSWORD
      ssm:
        key: omnidata/dev/db_password
    - secretKey: API_KEY
      ssm:
        key: omnidata/dev/api_key
    - secretKey: REDIS_PASSWORD
      ssm:
        key: omnidata/dev/redis_password
