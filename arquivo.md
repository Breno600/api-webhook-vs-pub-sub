^[[A^[[D^C~ $ kubectl describe pods transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn -n omnidata 
Name:             transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn
Namespace:        omnidata
Priority:         0
Service Account:  transaction-rules-v2-dev-java-chart
Node:             i-0de7b4307ef02f19f/100.99.70.93
Start Time:       Fri, 19 Dec 2025 19:08:24 +0000
Labels:           app.kubernetes.io/instance=transaction-rules-v2-dev
                  app.kubernetes.io/name=java-chart
                  env=dev
                  pod-template-hash=5795b4fbb4
Annotations:      kubectl.kubernetes.io/restartedAt: 2025-12-17T18:25:16Z
Status:           Pending
IP:               
IPs:              <none>
Controlled By:    ReplicaSet/transaction-rules-v2-dev-java-chart-5795b4fbb4
Containers:
  java-chart:
    Container ID:   
    Image:          fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2:2.0.0-20251218_174100-eff70050-SNAPSHOT
    Image ID:       
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Limits:
      cpu:     200m
      memory:  256Mi
    Requests:
      cpu:     100m
      memory:  128Mi
    Environment:
      CAFFEINE_SPEC:                          expireAfterWrite=24h
      DB_AUTO_COMMIT:                         true
      DB_CONNECTION_TIMEOUT:                  30000
      DB_DIALECT:                             com.fiserv.expdatarules.adapters.outbound.database.jpa.dialect.DefaultDialect
      DB_DRIVER_CLASS_NAME:                   net.snowflake.client.jdbc.SnowflakeDriver
      DB_IDLE_TIMEOUT:                        600000
      DB_MAXIMUM_POOL_SIZE:                   10
      DB_MAX_LIFETIME:                        1800000
      DB_MINIMUM_IDLE:                        10
      DB_PRIVATE_KEY_PATH:                    /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8
      ENABLE_IDEMPOTENCE:                     true
      HABILITAR_CACHE_POR_DEMANDA:            true
      HABILITAR_ENVIO_KAFKA:                  true
      HABILITAR_ENVIO_SQS:                    true
      HABILITAR_INFO_LOGS:                    true
      HABILITAR_USO_REDIS:                    false
      KAFKA_BOOTSTRAP_SERVERS:                hbld2kafapp0009.1dc.com:9094,hbld2kafapp0013.1dc.com:9094,hbld2kafapp0014.1dc.com:9094
      KAFKA_KEYSTORE_LOCATION:                file:/opt/booter/kafka-certs/wvc-ap11753-me-kpc-d.1dc.com.p12
      KAFKA_SECURITY_PROTOCOL:                SSL
      KAFKA_TRUSTSTORE_LOCATION:              file:/opt/booter/kafka-certs/kafka-truststore.p12
      LOGS_EXPORTER:                          none
      METRICS_EXPORTER:                       otlp
      OTLP_ENDPOINT:                          http://opentelemetry-dev-otel-chart.opentelemetry.svc.cluster.local:4317
      OTLP_PROTOCOL:                          grpc
      PAIS:                                   BR
      SERVER_PORT:                            8083
      SPRING_DATASOURCE_HIKARI_MAX_LIFETIME:  1800000
      SQS_LISTENER_ENDPOINT:                  https://sqs.us-east-1.amazonaws.com/909139952102/
      SQS_LISTENER_MAX_CONCURRENT_MESSAGES:   20
      SQS_LISTENER_MAX_MESSAGES_PER_POLL:     20
      SQS_LISTENER_QUEUE:                     omnidata-PFAC-queue-in.fifo
      SQS_LISTENER_REGION:                    us-east-1
      SQS_LISTENER_WAIT_TIMEOUT:              0
      SQS_PUBLISH_URLS:                       https://sqs.us-east-1.amazonaws.com/909139952102/omnidata-PFAC-queue-out.fifo
      TRACES_EXPORTER:                        otlp
      AWS_STS_REGIONAL_ENDPOINTS:             regional
      AWS_DEFAULT_REGION:                     us-east-1
      AWS_REGION:                             us-east-1
      AWS_ROLE_ARN:                           arn:aws:iam::909139952102:role/omnidata-irsa-sqs-full-access
      AWS_WEB_IDENTITY_TOKEN_FILE:            /var/run/secrets/eks.amazonaws.com/serviceaccount/token
    Mounts:
      /opt/booter/kafka-certs from kafka-certs (ro)
      /opt/booter/snowflake-certs from snowflake-certs (ro)
      /var/run/secrets/eks.amazonaws.com/serviceaccount from aws-iam-token (ro)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-5xzl7 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   False 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  aws-iam-token:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  86400
  kafka-certs:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  application-certs
    Optional:    false
  snowflake-certs:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  application-certs
    Optional:    false
  kube-api-access-5xzl7:
    Type:                     Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:   3607
    ConfigMapName:            kube-root-ca.crt
    Optional:                 false
    DownwardAPI:              true
QoS Class:                    Burstable
Node-Selectors:               <none>
Tolerations:                  node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                              node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Topology Spread Constraints:  kubernetes.io/hostname:DoNotSchedule when max skew 1 is exceeded for selector app=java-chart
                              topology.kubernetes.io/zone:DoNotSchedule when max skew 1 is exceeded for selector app=java-chart
Events:
  Type     Reason       Age                 From               Message
  ----     ------       ----                ----               -------
  Normal   Scheduled    2m6s                default-scheduler  Successfully assigned omnidata/transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn to i-0de7b4307ef02f19f
  Warning  FailedMount  63s (x8 over 2m6s)  kubelet            MountVolume.SetUp failed for volume "kafka-certs" : secret "application-certs" not found
  Warning  FailedMount  63s (x8 over 2m6s)  kubelet            MountVolume.SetUp failed for volume "snowflake-certs" : secret "application-certs" not found
~ $ 


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
    name: omnidata-external-secrets-clustersecretstore
    kind: ClusterSecretStore  # ou ClusterSecretStore
  secrets:
    # Secret para variáveis sensíveis (env)
    - name: app-env
      refreshInterval: 1m
      target:
        name: transaction-data-rules-v2-env
      data:
        - secretKey: KAFKA_KEYSTORE_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD"
        - secretKey: DB_URL
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env/DB_URL"
        - secretKey: DB_USERNAME
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env/DB_USERNAME"
        - secretKey: DB_PRIVATE_KEY_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env/DB_PRIVATE_KEY_PASSWORD"
        - secretKey: KAFKA_TRUSTSTORE_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env/KAFKA_TRUSTSTORE_PASSWORD"

    # Secret para certificados (volume/arquivos)
    - name: application-certs
      refreshInterval: 1m
      target:
        name: application-certs
      data:
        # Se você armazenar no AWS como BASE64 (string),
        # use decodingStrategy Base64 para virar binário no Secret do K8s
        - secretKey: wvcAp11753
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64"
            decodingStrategy: Base64
        - secretKey: kafka-truststore
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env/kafka-truststore_b64"
            decodingStrategy: Base64
        - secretKey: snowflake-cert
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env/snowflake-cert_b64"
            decodingStrategy: Base64

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



apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "java-chart.fullname" . }}
  labels:
    {{- include "java-chart.labels" . | nindent 4 }}
spec:
  {{- if not .Values.autoscaling.enabled }}
  replicas: {{ .Values.replicaCount | default 1 }}
  {{- end }}
  selector:
    matchLabels:
      {{- include "java-chart.selectorLabels" . | nindent 6 }}
  template:
    metadata:
      {{- with .Values.podAnnotations }}
      annotations:
        {{- toYaml . | nindent 8 }}
      {{- end }}
      labels:
        {{- include "java-chart.selectorLabels" . | nindent 8 }}
        env: {{ .Values.env | quote }}
        {{- with .Values.podLabels }}
        {{- toYaml . | nindent 8 }}
        {{- end }}
    spec:
      {{- with .Values.imagePullSecrets }}
      imagePullSecrets:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      serviceAccountName: {{ include "java-chart.serviceAccountName" . }}

      {{- with .Values.podSecurityContext }}
      securityContext:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      containers:
        - name: {{ .Chart.Name }}

          {{- with .Values.securityContext }}
          securityContext:
            {{- toYaml . | nindent 12 }}
          {{- end }}

          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
          imagePullPolicy: {{ .Values.image.pullPolicy | default "IfNotPresent" }}

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

          {{- with .Values.resources }}
          resources:
            {{- toYaml . | nindent 12 }}
          {{- end }}

          {{- with .Values.volumeMounts }}
          volumeMounts:
            {{- toYaml . | nindent 12 }}
          {{- end }}

      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: app
                    operator: In
                    values:
                      - {{ .Chart.Name }}
              topologyKey: "kubernetes.io/hostname"

      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: "topology.kubernetes.io/zone"
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: {{ .Chart.Name }}
        - maxSkew: 1
          topologyKey: "kubernetes.io/hostname"
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: {{ .Chart.Name }}

      {{- with .Values.volumes }}
      volumes:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      {{- with .Values.nodeSelector }}
      nodeSelector:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      {{- with .Values.affinity }}
      affinity:
        {{- toYaml . | nindent 8 }}
      {{- end }}

      {{- with .Values.tolerations }}
      tolerations:
        {{- toYaml . | nindent 8 }}
      {{- end }}
