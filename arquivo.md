SEGUE yaml completo

api-webhook-vs-pub-sub $ kubectl get pods transaction-rules-v2-dev-java-chart-5795b4fbb4-2djb6 -o yaml -n omnidata
apiVersion: v1
kind: Pod
metadata:
  annotations:
    kubectl.kubernetes.io/restartedAt: "2025-12-17T18:25:16Z"
  creationTimestamp: "2025-12-19T20:31:09Z"
  generateName: transaction-rules-v2-dev-java-chart-5795b4fbb4-
  generation: 1
  labels:
    app.kubernetes.io/instance: transaction-rules-v2-dev
    app.kubernetes.io/name: java-chart
    env: dev
    pod-template-hash: 5795b4fbb4
  name: transaction-rules-v2-dev-java-chart-5795b4fbb4-2djb6
  namespace: omnidata
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: transaction-rules-v2-dev-java-chart-5795b4fbb4
    uid: e4ecf112-af35-4386-a3d4-42ec88ae8ae7
  resourceVersion: "11706978"
  uid: fe5406ae-4990-4350-93f0-823164c44d1a
spec:
  affinity:
    podAntiAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - java-chart
        topologyKey: kubernetes.io/hostname
  containers:
  - env:
    - name: CAFFEINE_SPEC
      value: expireAfterWrite=24h
    - name: DB_AUTO_COMMIT
      value: "true"
    - name: DB_CONNECTION_TIMEOUT
      value: "30000"
    - name: DB_DIALECT
      value: com.fiserv.expdatarules.adapters.outbound.database.jpa.dialect.DefaultDialect
    - name: DB_DRIVER_CLASS_NAME
      value: net.snowflake.client.jdbc.SnowflakeDriver
    - name: DB_IDLE_TIMEOUT
      value: "600000"
    - name: DB_MAXIMUM_POOL_SIZE
      value: "10"
    - name: DB_MAX_LIFETIME
      value: "1800000"
    - name: DB_MINIMUM_IDLE
      value: "10"
    - name: DB_PRIVATE_KEY_PATH
      value: /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8
    - name: ENABLE_IDEMPOTENCE
      value: "true"
    - name: HABILITAR_CACHE_POR_DEMANDA
      value: "true"
    - name: HABILITAR_ENVIO_KAFKA
      value: "true"
    - name: HABILITAR_ENVIO_SQS
      value: "true"
    - name: HABILITAR_INFO_LOGS
      value: "true"
    - name: HABILITAR_USO_REDIS
      value: "false"
    - name: KAFKA_BOOTSTRAP_SERVERS
      value: hbld2kafapp0009.1dc.com:9094,hbld2kafapp0013.1dc.com:9094,hbld2kafapp0014.1dc.com:9094
    - name: KAFKA_KEYSTORE_LOCATION
      value: file:/opt/booter/kafka-certs/wvc-ap11753-me-kpc-d.1dc.com.p12
    - name: KAFKA_SECURITY_PROTOCOL
      value: SSL
    - name: KAFKA_TRUSTSTORE_LOCATION
      value: file:/opt/booter/kafka-certs/kafka-truststore.p12
    - name: LOGS_EXPORTER
      value: none
    - name: METRICS_EXPORTER
      value: otlp
    - name: OTLP_ENDPOINT
      value: http://opentelemetry-dev-otel-chart.opentelemetry.svc.cluster.local:4317
    - name: OTLP_PROTOCOL
      value: grpc
    - name: PAIS
      value: BR
    - name: SERVER_PORT
      value: "8083"
    - name: SPRING_DATASOURCE_HIKARI_MAX_LIFETIME
      value: "1800000"
    - name: SQS_LISTENER_ENDPOINT
      value: https://sqs.us-east-1.amazonaws.com/909139952102/
    - name: SQS_LISTENER_MAX_CONCURRENT_MESSAGES
      value: "20"
    - name: SQS_LISTENER_MAX_MESSAGES_PER_POLL
      value: "20"
    - name: SQS_LISTENER_QUEUE
      value: omnidata-PFAC-queue-in.fifo
    - name: SQS_LISTENER_REGION
      value: us-east-1
    - name: SQS_LISTENER_WAIT_TIMEOUT
      value: "0"
    - name: SQS_PUBLISH_URLS
      value: https://sqs.us-east-1.amazonaws.com/909139952102/omnidata-PFAC-queue-out.fifo
    - name: TRACES_EXPORTER
      value: otlp
    - name: AWS_STS_REGIONAL_ENDPOINTS
      value: regional
    - name: AWS_DEFAULT_REGION
      value: us-east-1
    - name: AWS_REGION
      value: us-east-1
    - name: AWS_ROLE_ARN
      value: arn:aws:iam::909139952102:role/omnidata-irsa-sqs-full-access
    - name: AWS_WEB_IDENTITY_TOKEN_FILE
      value: /var/run/secrets/eks.amazonaws.com/serviceaccount/token
    image: fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2:2.0.0-20251218_174100-eff70050-SNAPSHOT
    imagePullPolicy: IfNotPresent
    name: java-chart
    resources:
      limits:
        cpu: 200m
        memory: 256Mi
      requests:
        cpu: 100m
        memory: 128Mi
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /opt/booter/kafka-certs
      name: kafka-certs
      readOnly: true
    - mountPath: /opt/booter/snowflake-certs
      name: snowflake-certs
      readOnly: true
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-rmk2r
      readOnly: true
    - mountPath: /var/run/secrets/eks.amazonaws.com/serviceaccount
      name: aws-iam-token
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: i-0de7b4307ef02f19f
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: transaction-rules-v2-dev-java-chart
  serviceAccountName: transaction-rules-v2-dev-java-chart
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  topologySpreadConstraints:
  - labelSelector:
      matchLabels:
        app: java-chart
    maxSkew: 1
    topologyKey: topology.kubernetes.io/zone
    whenUnsatisfiable: DoNotSchedule
  - labelSelector:
      matchLabels:
        app: java-chart
    maxSkew: 1
    topologyKey: kubernetes.io/hostname
    whenUnsatisfiable: DoNotSchedule
  volumes:
  - name: aws-iam-token
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          audience: sts.amazonaws.com
          expirationSeconds: 86400
          path: token
  - name: kafka-certs
    secret:
      defaultMode: 420
      items:
      - key: wvcAp11753
        path: wvc-ap11753-me-kpc-d.1dc.com.p12
      - key: kafka-truststore
        path: kafka-truststore.p12
      secretName: application-certs
  - name: snowflake-certs
    secret:
      defaultMode: 420
      items:
      - key: snowflake-cert
        path: OMNIDATA_LOWER_SRVACCT_rsa_key.p8
      secretName: application-certs
  - name: kube-api-access-rmk2r
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2025-12-19T20:31:14Z"
    observedGeneration: 1
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-12-19T20:31:09Z"
    observedGeneration: 1
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-12-19T20:35:45Z"
    message: 'containers with unready status: [java-chart]'
    observedGeneration: 1
    reason: ContainersNotReady
    status: "False"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-12-19T20:35:45Z"
    message: 'containers with unready status: [java-chart]'
    observedGeneration: 1
    reason: ContainersNotReady
    status: "False"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-12-19T20:31:09Z"
    observedGeneration: 1
    status: "True"
    type: PodScheduled
  containerStatuses:
  - allocatedResources:
      cpu: 100m
      memory: 128Mi
    containerID: containerd://47de656dabc4edad2380f7cc8a057f9b0cba5dd10fd16eb8be29a64be75f4c6d
    image: fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2:2.0.0-20251218_174100-eff70050-SNAPSHOT
    imageID: fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2@sha256:6d067ae26974fcf0277c0aa206d6610af0e668ded32d2c2e167188bbd5863e1d
    lastState:
      terminated:
        containerID: containerd://47de656dabc4edad2380f7cc8a057f9b0cba5dd10fd16eb8be29a64be75f4c6d
        exitCode: 1
        finishedAt: "2025-12-19T20:35:45Z"
        reason: Error
        startedAt: "2025-12-19T20:34:47Z"
    name: java-chart
    ready: false
    resources:
      limits:
        cpu: 200m
        memory: 256Mi
      requests:
        cpu: 100m
        memory: 128Mi
    restartCount: 3
    started: false
    state:
      waiting:
        message: back-off 40s restarting failed container=java-chart pod=transaction-rules-v2-dev-java-chart-5795b4fbb4-2djb6_omnidata(fe5406ae-4990-4350-93f0-823164c44d1a)
        reason: CrashLoopBackOff
    user:
      linux:
        gid: 1001
        supplementalGroups:
        - 1000
        - 1001
        uid: 1000
    volumeMounts:
    - mountPath: /opt/booter/kafka-certs
      name: kafka-certs
      readOnly: true
      recursiveReadOnly: Disabled
    - mountPath: /opt/booter/snowflake-certs
      name: snowflake-certs
      readOnly: true
      recursiveReadOnly: Disabled
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-rmk2r
      readOnly: true
      recursiveReadOnly: Disabled
    - mountPath: /var/run/secrets/eks.amazonaws.com/serviceaccount
      name: aws-iam-token
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 100.99.70.93
  hostIPs:
  - ip: 100.99.70.93
  observedGeneration: 1
  phase: Running
  podIP: 100.99.101.225
  podIPs:
  - ip: 100.99.101.225
  qosClass: Burstable
  startTime: "2025-12-19T20:31:09Z"
api-webhook-vs-pub-sub $


# values
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
            key: "omnidata/dev/transaction-rules-v2/env"
            property: "KAFKA_KEYSTORE_PASSWORD"
        - secretKey: DB_URL
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env"
            property: "DB_URL"
        - secretKey: DB_USERNAME
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env"
            property: "DB_USERNAME"
        - secretKey: DB_PRIVATE_KEY_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env"
            property: "DB_PRIVATE_KEY_PASSWORD"
        - secretKey: KAFKA_TRUSTSTORE_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env"
            property: "KAFKA_TRUSTSTORE_PASSWORD"

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
            key: "omnidata/dev/transaction-rules-v2/env"
            property: "wvcAp11753"
            decodingStrategy: Base64
        - secretKey: kafka-truststore
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env"
            property: "kafka-truststore"
            decodingStrategy: Base64
        - secretKey: snowflake-cert
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/env"
            property: "snowflake-cert"
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
