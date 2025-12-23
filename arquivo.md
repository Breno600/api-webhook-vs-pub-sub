bash-5.2# kubectl logs transaction-rules-v2-dev-java-chart-688b86cc7f-wsngw -f -n omnidata

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/

 :: Spring Boot ::                (v3.5.8)

timestamp="2025-12-22 22:00:51.639", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="c.f.e.TransactionDataRulesApplication", thread="main", log="Starting TransactionDataRulesApplication v2.0.0-SNAPSHOT using Java 21.0.8 with PID 1 (/opt/booter/transaction-data-rules.jar started by booter in /opt/booter) "
timestamp="2025-12-22 22:00:51.641", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="c.f.e.TransactionDataRulesApplication", thread="main", log="No active profile set, falling back to 1 default profile: "default" "
timestamp="2025-12-22 22:01:07.138", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Multiple Spring Data modules found, entering strict repository configuration mode "
timestamp="2025-12-22 22:01:07.140", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Bootstrapping Spring Data JPA repositories in DEFAULT mode. "
timestamp="2025-12-22 22:01:08.837", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ClienteRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-22 22:01:08.838", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvBandeiraRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-22 22:01:08.839", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvModoEntradaRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-22 22:01:08.840", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvProdutoRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-22 22:01:08.841", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvTransacoesRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-22 22:01:08.842", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.LojaRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-22 22:01:08.843", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.SitRedeRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-22 22:01:09.380", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Finished Spring Data repository scanning in 2139 ms. Found 6 JPA repository interfaces. "
timestamp="2025-12-22 22:01:19.542", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.b.w.e.tomcat.TomcatWebServer", thread="main", log="Tomcat initialized with port 8083 (http) "
2025-12-22 22:01:19.781  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
timestamp="2025-12-22 22:01:19.781", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.catalina.core.StandardService", thread="main", log="Starting service [Tomcat] "
2025-12-22 22:01:19.978  INFO 1 --- [           main] o.apache.catalina.core.StandardEngine    : Starting Servlet engine: [Apache Tomcat/10.1.49]
timestamp="2025-12-22 22:01:19.978", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.catalina.core.StandardEngine", thread="main", log="Starting Servlet engine: [Apache Tomcat/10.1.49] "
2025-12-22 22:01:20.678  INFO 1 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
timestamp="2025-12-22 22:01:20.678", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.c.c.C.[Tomcat].[localhost].[/]", thread="main", log="Initializing Spring embedded WebApplicationContext "
timestamp="2025-12-22 22:01:20.679", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.b.w.s.c.ServletWebServerApplicationContext", thread="main", log="Root WebApplicationContext: initialization completed in 28595 ms "
timestamp="2025-12-22 22:01:32.742", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="com.zaxxer.hikari.HikariDataSource", thread="main", log="HikariPool-1 - Starting... "
2025-12-22 22:01:34.539  INFO 1 --- [           main] net.snowflake.client.core.SFSession      : Opening session with server: https://sz68910.sa-east-1.aws.privatelink.snowflakecomputing.com:443/, account: sz68910, user: OMNIDATA_USER_LOWER, password is not provided, role: null, database: DB_DEV_RAW_APM0011753_OMNIDATA, schema: SCH_OMINIDATA_HOSPEDADO, warehouse: WH_DEV_OMNIDATA_ETL, validate default parameters: null, authenticator: SNOWFLAKE_JWT, ocsp mode: FAIL_OPEN, passcode in password: null, passcode is not provided, private key is not provided, disable socks proxy: null, application: null, app id: JDBC, app version: 3.27.0, login timeout: null, retry timeout: null, network timeout: null, query timeout: null, connection timeout: null, socket timeout: null, tracing: null, private key file: /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8, private key base 64: not provided, private key pwd is provided, enable_diagnostics: null, diagnostics_allowlist_path: null, session parameters: client store temporary credential: null, gzip disabled: null, browser response timeout: null
timestamp="2025-12-22 22:01:34.539", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="net.snowflake.client.core.SFSession", thread="main", log="Opening session with server: https://sz68910.sa-east-1.aws.privatelink.snowflakecomputing.com:443/, account: sz68910, user: OMNIDATA_USER_LOWER, password is not provided, role: null, database: DB_DEV_RAW_APM0011753_OMNIDATA, schema: SCH_OMINIDATA_HOSPEDADO, warehouse: WH_DEV_OMNIDATA_ETL, validate default parameters: null, authenticator: SNOWFLAKE_JWT, ocsp mode: FAIL_OPEN, passcode in password: null, passcode is not provided, private key is not provided, disable socks proxy: null, application: null, app id: JDBC, app version: 3.27.0, login timeout: null, retry timeout: null, network timeout: null, query timeout: null, connection timeout: null, socket timeout: null, tracing: null, private key file: /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8, private key base 64: not provided, private key pwd is provided, enable_diagnostics: null, diagnostics_allowlist_path: null, session parameters: client store temporary credential: null, gzip disabled: null, browser response timeout: null "
2025-12-22 22:01:34.542  INFO 1 --- [           main] net.snowflake.client.core.SFSession      : Connecting to GLOBAL Snowflake domain
timestamp="2025-12-22 22:01:34.542", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="net.snowflake.client.core.SFSession", thread="main", log="Connecting to GLOBAL Snowflake domain "
2025-12-22 22:01:37.240  WARN 1 --- [           main] net.snowflake.client.core.FileUtil       : Extract private key from file: File /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8 is accessible by others to: read
timestamp="2025-12-22 22:01:37.240", apm="0011753", appname="TransactionDataRulesV2", level="WARN", logger="net.snowflake.client.core.FileUtil", thread="main", log="Extract private key from file: File /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8 is accessible by others to: read "
bash-5.2# 




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
            key: "omnidata/dev/transaction-rules-v2/envs"
            property: "KAFKA_KEYSTORE_PASSWORD"
        - secretKey: DB_URL
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/envs"
            property: "DB_URL"
        - secretKey: DB_USERNAME
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/envs"
            property: "DB_USERNAME"
        - secretKey: DB_PRIVATE_KEY_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/envs"
            property: "DB_PRIVATE_KEY_PASSWORD"
        - secretKey: KAFKA_TRUSTSTORE_PASSWORD
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/envs"
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
            key: "omnidata/dev/transaction-rules-v2/envs"
            property: "wvcAp11753"
            decodingStrategy: Base64
        - secretKey: kafka-truststore
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/envs"
            property: "kafka-truststore"
            decodingStrategy: Base64
        - secretKey: snowflake-cert
          remoteRef:
            key: "omnidata/dev/transaction-rules-v2/envs"
            property: "snowflake-cert"
            decodingStrategy: Base64
