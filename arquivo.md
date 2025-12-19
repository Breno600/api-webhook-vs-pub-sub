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

secretEnv:
  - name: DB_URL
    secretName: transaction-data-rules-v2-env
    secretKey: DB_URL
  - name: DB_USERNAME
    secretName: transaction-data-rules-v2-env
    secretKey: DB_USERNAME
  - name: DB_PRIVATE_KEY_PASSWORD
    secretName: transaction-data-rules-v2-env
    secretKey: DB_PRIVATE_KEY_PASSWORD
  - name: KAFKA_KEYSTORE_PASSWORD
    secretName: transaction-data-rules-v2-env
    secretKey: KAFKA_KEYSTORE_PASSWORD
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


## External Secrets ##
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



api-webhook-vs-pub-sub $ kubectl logs transaction-rules-v2-dev-java-chart-688b86cc7f-fclcq -f -n omnidata

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.5.8)

timestamp="2025-12-19 17:44:59.781", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="c.f.e.TransactionDataRulesApplication", thread="main", log="Starting TransactionDataRulesApplication v2.0.0-SNAPSHOT using Java 21.0.8 with PID 1 (/opt/booter/transaction-data-rules.jar started by booter in /opt/booter) "
timestamp="2025-12-19 17:44:59.791", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="c.f.e.TransactionDataRulesApplication", thread="main", log="No active profile set, falling back to 1 default profile: "default" "
timestamp="2025-12-19 17:45:13.895", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Multiple Spring Data modules found, entering strict repository configuration mode "
timestamp="2025-12-19 17:45:13.897", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Bootstrapping Spring Data JPA repositories in DEFAULT mode. "
timestamp="2025-12-19 17:45:15.492", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ClienteRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:45:15.494", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvBandeiraRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:45:15.495", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvModoEntradaRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:45:15.496", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvProdutoRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:45:15.591", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvTransacoesRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:45:15.592", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.LojaRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:45:15.594", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.SitRedeRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:45:16.196", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Finished Spring Data repository scanning in 2197 ms. Found 6 JPA repository interfaces. "
timestamp="2025-12-19 17:45:27.087", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.b.w.e.tomcat.TomcatWebServer", thread="main", log="Tomcat initialized with port 8083 (http) "
2025-12-19 17:45:27.293  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
timestamp="2025-12-19 17:45:27.293", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.catalina.core.StandardService", thread="main", log="Starting service [Tomcat] "
2025-12-19 17:45:27.496  INFO 1 --- [           main] o.apache.catalina.core.StandardEngine    : Starting Servlet engine: [Apache Tomcat/10.1.49]
timestamp="2025-12-19 17:45:27.496", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.catalina.core.StandardEngine", thread="main", log="Starting Servlet engine: [Apache Tomcat/10.1.49] "
2025-12-19 17:45:28.494  INFO 1 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
timestamp="2025-12-19 17:45:28.494", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.c.c.C.[Tomcat].[localhost].[/]", thread="main", log="Initializing Spring embedded WebApplicationContext "
timestamp="2025-12-19 17:45:28.495", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.b.w.s.c.ServletWebServerApplicationContext", thread="main", log="Root WebApplicationContext: initialization completed in 28301 ms "
timestamp="2025-12-19 17:45:40.901", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="com.zaxxer.hikari.HikariDataSource", thread="main", log="HikariPool-1 - Starting... "
2025-12-19 17:45:42.802  INFO 1 --- [           main] net.snowflake.client.core.SFSession      : Opening session with server: https://sz68910.sa-east-1.aws.privatelink.snowflakecomputing.com:443/, account: sz68910, user: OMNIDATA_LOWER_SRVACCT, password is not provided, role: null, database: DB_DEV_RAW_APM0011753_OMNIDATA, schema: SCH_OMINIDATA_HOSPEDADO, warehouse: WH_DEV_OMNIDATA_ETL, validate default parameters: null, authenticator: SNOWFLAKE_JWT, ocsp mode: FAIL_OPEN, passcode in password: null, passcode is not provided, private key is not provided, disable socks proxy: null, application: null, app id: JDBC, app version: 3.27.0, login timeout: null, retry timeout: null, network timeout: null, query timeout: null, connection timeout: null, socket timeout: null, tracing: null, private key file: /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8, private key base 64: not provided, private key pwd is provided, enable_diagnostics: null, diagnostics_allowlist_path: null, session parameters: client store temporary credential: null, gzip disabled: null, browser response timeout: null
timestamp="2025-12-19 17:45:42.802", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="net.snowflake.client.core.SFSession", thread="main", log="Opening session with server: https://sz68910.sa-east-1.aws.privatelink.snowflakecomputing.com:443/, account: sz68910, user: OMNIDATA_LOWER_SRVACCT, password is not provided, role: null, database: DB_DEV_RAW_APM0011753_OMNIDATA, schema: SCH_OMINIDATA_HOSPEDADO, warehouse: WH_DEV_OMNIDATA_ETL, validate default parameters: null, authenticator: SNOWFLAKE_JWT, ocsp mode: FAIL_OPEN, passcode in password: null, passcode is not provided, private key is not provided, disable socks proxy: null, application: null, app id: JDBC, app version: 3.27.0, login timeout: null, retry timeout: null, network timeout: null, query timeout: null, connection timeout: null, socket timeout: null, tracing: null, private key file: /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8, private key base 64: not provided, private key pwd is provided, enable_diagnostics: null, diagnostics_allowlist_path: null, session parameters: client store temporary credential: null, gzip disabled: null, browser response timeout: null "
2025-12-19 17:45:42.806  INFO 1 --- [           main] net.snowflake.client.core.SFSession      : Connecting to GLOBAL Snowflake domain
timestamp="2025-12-19 17:45:42.806", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="net.snowflake.client.core.SFSession", thread="main", log="Connecting to GLOBAL Snowflake domain "
api-webhook-vs-pub-sub $ kubectl get PODS -n omnidata
NAME                                                       READY   STATUS    RESTARTS      AGE
transaction-data-parquet-dev-java-chart-8468dfcd8d-jvz79   1/1     Running   0             5h31m
transaction-rules-v2-dev-java-chart-688b86cc7f-fclcq       1/1     Running   1 (11s ago)   73s
api-webhook-vs-pub-sub $ 
