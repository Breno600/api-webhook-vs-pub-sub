# values.dev.yaml
image:
  repository: fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2
  tag: 2.0.0-20251218_174100-eff70050-SNAPSHOT

env: dev

config:
  DB_DRIVER_CLASS_NAME: "net.snowflake.client.jdbc.SnowflakeDriver"
  DB_CONNECTION_TIMEOUT: 30000
  DB_MINIMUM_IDLE: 10
  DB_MAXIMUM_POOL_SIZE: 10
  DB_IDLE_TIMEOUT: 600000
  DB_MAX_LIFETIME: 1800000
  DB_AUTO_COMMIT: true
  SERVER_PORT: 8083
  TRACES_EXPORTER: "otlp"
  METRICS_EXPORTER: "otlp"
  LOGS_EXPORTER: "none"
  OTLP_ENDPOINT: "http://opentelemetry-dev-otel-chart.opentelemetry.svc.cluster.local:4317"
  OTLP_PROTOCOL: "grpc"
  HABILITAR_INFO_LOGS: true
  HABILITAR_CACHE_POR_DEMANDA: true
  HABILITAR_ENVIO_KAFKA: true
  HABILITAR_ENVIO_SQS: true
  HABILITAR_USO_REDIS: false
  PAIS: "BR"
  CAFFEINE_SPEC: expireAfterWrite=24h
  SQS_PUBLISH_URLS: "https://sqs.us-east-1.amazonaws.com/909139952102/omnidata-PFAC-queue-out.fifo"
  SQS_LISTENER_ENDPOINT: "https://sqs.us-east-1.amazonaws.com/909139952102/"
  SQS_LISTENER_QUEUE: "omnidata-PFAC-queue-in.fifo"
  SQS_LISTENER_REGION: "us-east-1"
  SQS_LISTENER_MAX_MESSAGES_PER_POLL: 20
  SQS_LISTENER_MAX_CONCURRENT_MESSAGES: 20
  SQS_LISTENER_WAIT_TIMEOUT: 0
  KAFKA_KEYSTORE_LOCATION: "file:/opt/booter/kafka-certs/wvc-ap11753-me-kpc-d.1dc.com.p12"
  KAFKA_TRUSTSTORE_LOCATION: "file:/opt/booter/kafka-certs/kafka-truststore.p12"
  KAFKA_SECURITY_PROTOCOL: "SSL"
  KAFKA_BOOTSTRAP_SERVERS: "hbld2kafapp0009.1dc.com:9094,hbld2kafapp0013.1dc.com:9094,hbld2kafapp0014.1dc.com:9094"
  ENABLE_IDEMPOTENCE: true
  SPRING_DATASOURCE_HIKARI_MAX_LIFETIME: "1800000"
  DB_MAX_LIFETIME: "1800000"
  DB_DIALECT: "com.fiserv.expdatarules.adapters.outbound.database.jpa.dialect.DefaultDialect"
  DB_PRIVATE_KEY_PATH: "/opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8"

secretValues:
  KAFKA_KEYSTORE_PASSWORD:  "xxx"
  DB_URL: "xxx"
  DB_USERNAME: "xxx"
  DB_PRIVATE_KEY_PASSWORD: "xxx"
  KAFKA_TRUSTSTORE_PASSWORD: "xxx"

ingress:
  enabled: false

service:
  enabled: false

serviceAccount:
  create: true
  automount: true
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::909139952102:role/omnidata-irsa-sqs-full-access

resource:
  limits:
    cpu: 200m
    memory: 256Mi
  requests:
    cpu: 100m
    memory: 128Mi

autoscaling:
  enabled: false
  minReplicas: 1
  maxReplicas: 1

applicationCerts:
  name: application-certs
  enabled: true
  datas:
    wvcAp11753: ""
    kafka-truststore: ""
    snowflake-cert: ""

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
    readonly: true

  - name: snowflake-certs
    mountPath: /opt/booter/snowflake-certs
    readonly: true


# \amazon-elastic-kubernetes-service\common\charts\java-chart\templates\application-certs-secret.yaml
{{- if .Values.applicationCerts.enabled }}
apiVersion: v1
kind: Secret
metadata:
  name: {{ $.Values.applicationCerts.name }}
type: Opaque
data:
  {{- range $k, $v := .Values.applicationCerts.datas }}
  {{ $k }}: {{ $v | quote }}
  {{- end }}
{{- end }}

# common\charts\java-chart\templates\deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "java-chart.fullname" . }}
  labels:
    {{- include "java-chart.labels" . | nindent 4 }}
spec:
  {{- if not .Values.autoscaling.enabled }}
  replicas: {{ .Values.replicaCount }}
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
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            {{- range $k, $v := .Values.config }}
            - name: {{ $k }}
              value: {{ $v | quote }}
            {{- end }}

            {{- if .Values.externalSecrets.enabled }}
            envfrom:
              - secretRef:
                  name: {{ .Values.externalSecrets.target.name }}
            {{- end}}

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


