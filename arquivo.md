# values.dev.yaml

image:
  repository: fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2
  tag: 2.0.0-20251218_174100-eff70050-SNAPSHOT

env: dev

config:
  DB_DRIVER_CLASS_NAME: "net.snowflake.client.jdbc.SnowflakeDriver"
  DB_CONNECTION_TIMEOUT: "30000"
  DB_MINIMUM_IDLE: "10"
  DB_MAXIMUM_POOL_SIZE: "10"
  DB_IDLE_TIMEOUT: "600000"
  DB_MAX_LIFETIME: "1800000"
  DB_AUTO_COMMIT: "true"
  SERVER_PORT: "8083"
  TRACES_EXPORTER: "otlp"
  METRICS_EXPORTER: "otlp"
  LOGS_EXPORTER: "none"
  OTLP_ENDPOINT: "http://opentelemetry-dev-otel-chart.opentelemetry.svc.cluster.local:4317"
  OTLP_PROTOCOL: "grpc"
  HABILITAR_INFO_LOGS: "true"
  HABILITAR_CACHE_POR_DEMANDA: "true"
  HABILITAR_ENVIO_KAFKA: "true"
  HABILITAR_ENVIO_SQS: "true"
  HABILITAR_USO_REDIS: "false"
  PAIS: "BR"
  CAFFEINE_SPEC: "expireAfterWrite=24h"
  SQS_PUBLISH_URLS: "https://sqs.us-east-1.amazonaws.com/909139952102/omnidata-PFAC-queue-out.fifo"
  SQS_LISTENER_ENDPOINT: "https://sqs.us-east-1.amazonaws.com/909139952102/"
  SQS_LISTENER_QUEUE: "omnidata-PFAC-queue-in.fifo"
  SQS_LISTENER_REGION: "us-east-1"
  SQS_LISTENER_MAX_MESSAGES_PER_POLL: "20"
  SQS_LISTENER_MAX_CONCURRENT_MESSAGES: "20"
  SQS_LISTENER_WAIT_TIMEOUT: "0"
  KAFKA_KEYSTORE_LOCATION: "file:/opt/booter/kafka-certs/wvc-ap11753-me-kpc-d.1dc.com.p12"
  KAFKA_TRUSTSTORE_LOCATION: "file:/opt/booter/kafka-certs/kafka-truststore.p12"
  KAFKA_SECURITY_PROTOCOL: "SSL"
  KAFKA_BOOTSTRAP_SERVERS: "hbld2kafapp0009.1dc.com:9094,hbld2kafapp0013.1dc.com:9094,hbld2kafapp0014.1dc.com:9094"
  ENABLE_IDEMPOTENCE: "true"
  SPRING_DATASOURCE_HIKARI_MAX_LIFETIME: "1800000"
  DB_DIALECT: "com.fiserv.expdatarules.adapters.outbound.database.jpa.dialect.DefaultDialect"
  DB_PRIVATE_KEY_PATH: "/opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8"

# 1) ExternalSecrets que GERAM os secrets no cluster
externalSecrets:
  enabled: true
  storeRef:
    name: aws-sm-store
    kind: SecretStore  # ou ClusterSecretStore
  secrets:
    # Secret para variáveis sensíveis (env)
    - name: app-env
      refreshInterval: 1h
      target:
        name: transaction-data-rules-v2-env
      data:
        - secretKey: KAFKA_KEYSTORE_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-data-rules-v2/KAFKA_KEYSTORE_PASSWORD"
        - secretKey: DB_URL
          remoteRef:
            key: "omnidata/dev/transaction-data-rules-v2/DB_URL"
        - secretKey: DB_USERNAME
          remoteRef:
            key: "omnidata/dev/transaction-data-rules-v2/DB_USERNAME"
        - secretKey: DB_PRIVATE_KEY_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-data-rules-v2/DB_PRIVATE_KEY_PASSWORD"
        - secretKey: KAFKA_TRUSTSTORE_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-data-rules-v2/KAFKA_TRUSTSTORE_PASSWORD"

    # Secret para certificados (volume/arquivos)
    - name: application-certs
      refreshInterval: 6h
      target:
        name: application-certs
      data:
        # Se você armazenar no AWS como BASE64 (string),
        # use decodingStrategy Base64 para virar binário no Secret do K8s
        - secretKey: wvcAp11753
          remoteRef:
            key: "omnidata/dev/certs/wvcAp11753_b64"
            decodingStrategy: Base64
        - secretKey: kafka-truststore
          remoteRef:
            key: "omnidata/dev/certs/kafka-truststore_b64"
            decodingStrategy: Base64
        - secretKey: snowflake-cert
          remoteRef:
            key: "omnidata/dev/certs/snowflake-cert_b64"
            decodingStrategy: Base64

# 2) Injeção específica por variável (SEM envFrom)
secretEnv:
  - name: KAFKA_KEYSTORE_PASSWORD
    secretName: transaction-data-rules-v2-env
    secretKey: KAFKA_KEYSTORE_PASSWORD
  - name: DB_URL
    secretName: transaction-data-rules-v2-env
    secretKey: DB_URL
  - name: DB_USERNAME
    secretName: transaction-data-rules-v2-env
    secretKey: DB_USERNAME
  - name: DB_PRIVATE_KEY_PASSWORD
    secretName: transaction-data-rules-v2-env
    secretKey: DB_PRIVATE_KEY_PASSWORD
  - name: KAFKA_TRUSTSTORE_PASSWORD
    secretName: transaction-data-rules-v2-env
    secretKey: KAFKA_TRUSTSTORE_PASSWORD

# Mantém seu volume/volumeMount (agora o Secret "application-certs" vem do ExternalSecret)
volumes:
  - name: kafka-certs
    secret:
      secretName: application-certs
      items:
        - key: wvcAp11753
          path: wvc-ap11753-me-kpc-d.1dc.com.p12
        - key: kafka-truststore
          path: kafka-truststore.p12
  - name: snowflake-certs
    secret:
      secretName: application-certs
      items:
        - key: snowflake-cert
          path: OMNIDATA_LOWER_SRVACCT_rsa_key.p8

volumeMounts:
  - name: kafka-certs
    mountPath: /opt/booter/kafka-certs
    readOnly: true
  - name: snowflake-certs
    mountPath: /opt/booter/snowflake-certs
    readOnly: true

serviceAccount:
  create: true
  automount: true
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::909139952102:role/omnidata-irsa-sqs-full-access

resources:
  limits:
    cpu: 200m
    memory: 256Mi
  requests:
    cpu: 100m
    memory: 128Mi


# common/charts/java-chart/templates/externalsecrets.yaml
{{- if .Values.externalSecrets.enabled }}
{{- $storeName := .Values.externalSecrets.storeRef.name }}
{{- $storeKind := .Values.externalSecrets.storeRef.kind | default "SecretStore" }}

{{- range $es := .Values.externalSecrets.secrets }}
---
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  name: {{ include "java-chart.fullname" $ }}-{{ $es.name }}
  labels:
    {{- include "java-chart.labels" $ | nindent 4 }}
spec:
  refreshInterval: {{ $es.refreshInterval | default "1h" }}
  secretStoreRef:
    name: {{ $storeName }}
    kind: {{ $storeKind }}
  target:
    name: {{ $es.target.name }}
    creationPolicy: Owner
    template:
      type: Opaque
  data:
    {{- range $es.data }}
    - secretKey: {{ .secretKey }}
      remoteRef:
        key: {{ .remoteRef.key | quote }}
        {{- if .remoteRef.property }}
        property: {{ .remoteRef.property | quote }}
        {{- end }}
        {{- if .remoteRef.version }}
        version: {{ .remoteRef.version | quote }}
        {{- end }}
        {{- if .remoteRef.decodingStrategy }}
        decodingStrategy: {{ .remoteRef.decodingStrategy }}
        {{- end }}
    {{- end }}
{{- end }}
{{- end }}

# common/charts/java-chart/templates/deployment.yaml
          env:
            {{- range $k, $v := .Values.config }}
            - name: {{ $k }}
              value: {{ $v | quote }}
            {{- end }}

            {{- range .Values.secretEnv }}
            - name: {{ .name }}
              valueFrom:
                secretKeyRef:
                  name: {{ .secretName | quote }}
                  key: {{ .secretKey | quote }}
                  {{- if hasKey . "optional" }}
                  optional: {{ .optional }}
                  {{- end }}
            {{- end }}
