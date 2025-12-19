api-webhook-vs-pub-sub $ kubectl logs transaction-rules-v2-dev-java-chart-5795b4fbb4-2djb6 -f -n omnidata
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v3.5.8)
timestamp="2025-12-19 17:31:27.133", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="c.f.e.TransactionDataRulesApplication", thread="main", log="Starting TransactionDataRulesApplication v2.0.0-SNAPSHOT using Java 21.0.8 with PID 1 (/opt/booter/transaction-data-rules.jar started by booter in /opt/booter) "
timestamp="2025-12-19 17:31:27.135", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="c.f.e.TransactionDataRulesApplication", thread="main", log="No active profile set, falling back to 1 default profile: "default" "
timestamp="2025-12-19 17:31:42.329", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Multiple Spring Data modules found, entering strict repository configuration mode "
timestamp="2025-12-19 17:31:42.331", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Bootstrapping Spring Data JPA repositories in DEFAULT mode. "
timestamp="2025-12-19 17:31:43.931", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ClienteRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:31:43.932", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvBandeiraRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:31:43.933", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvModoEntradaRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:31:43.935", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvProdutoRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:31:44.028", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.ConvTransacoesRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:31:44.029", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.LojaRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:31:44.031", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationExtensionSupport", thread="main", log="Spring Data JPA - Could not safely identify store assignment for repository candidate interface com.fiserv.expdatarules.adapters.outbound.database.redis.repositories.SitRedeRedisRepository; If you want this repository to be a JPA repository, consider annotating your entities with one of these annotations: jakarta.persistence.Entity, jakarta.persistence.MappedSuperclass (preferred), or consider extending one of the following types with your repository: org.springframework.data.jpa.repository.JpaRepository "
timestamp="2025-12-19 17:31:44.731", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.d.r.c.RepositoryConfigurationDelegate", thread="main", log="Finished Spring Data repository scanning in 2298 ms. Found 6 JPA repository interfaces. "
timestamp="2025-12-19 17:31:54.831", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.b.w.e.tomcat.TomcatWebServer", thread="main", log="Tomcat initialized with port 8083 (http) "
2025-12-19 17:31:55.033  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
timestamp="2025-12-19 17:31:55.033", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.catalina.core.StandardService", thread="main", log="Starting service [Tomcat] "
2025-12-19 17:31:55.131  INFO 1 --- [           main] o.apache.catalina.core.StandardEngine    : Starting Servlet engine: [Apache Tomcat/10.1.49]
timestamp="2025-12-19 17:31:55.131", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.catalina.core.StandardEngine", thread="main", log="Starting Servlet engine: [Apache Tomcat/10.1.49] "
2025-12-19 17:31:55.831  INFO 1 --- [           main] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext
timestamp="2025-12-19 17:31:55.831", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.c.c.C.[Tomcat].[localhost].[/]", thread="main", log="Initializing Spring embedded WebApplicationContext "
timestamp="2025-12-19 17:31:55.832", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.b.w.s.c.ServletWebServerApplicationContext", thread="main", log="Root WebApplicationContext: initialization completed in 28396 ms "
timestamp="2025-12-19 17:32:07.432", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="com.zaxxer.hikari.HikariDataSource", thread="main", log="HikariPool-1 - Starting... "
timestamp="2025-12-19 17:32:08.928", apm="0011753", appname="TransactionDataRulesV2", level="WARN", logger="o.s.b.w.s.c.AnnotationConfigServletWebServerApplicationContext", thread="main", log="Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Failed to instantiate [org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean]: Factory method 'entityManagerFactory' threw exception with message: Driver net.snowflake.client.jdbc.SnowflakeDriver claims to not accept jdbcUrl, ${DB_URL} "
2025-12-19 17:32:09.936  INFO 1 --- [           main] o.apache.catalina.core.StandardService   : Stopping service [Tomcat]
timestamp="2025-12-19 17:32:09.936", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.a.catalina.core.StandardService", thread="main", log="Stopping service [Tomcat] "
2025-12-19 17:32:10.029  WARN 1 --- [           main] o.a.c.loader.WebappClassLoaderBase       : The web application [ROOT] appears to have started a thread named [BatchSpanProcessor_WorkerThread-1] but has failed to stop it. This is very likely to create a memory leak. Stack trace of thread:
 java.base/jdk.internal.misc.Unsafe.park(Native Method)
 java.base/java.util.concurrent.locks.LockSupport.parkNanos(LockSupport.java:269)
 java.base/java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.awaitNanos(AbstractQueuedSynchronizer.java:1763)
 java.base/java.util.concurrent.ArrayBlockingQueue.poll(ArrayBlockingQueue.java:435)
 io.opentelemetry.sdk.trace.export.BatchSpanProcessor$Worker.run(BatchSpanProcessor.java:266)
 java.base/java.lang.Thread.run(Thread.java:1583)
timestamp="2025-12-19 17:32:10.029", apm="0011753", appname="TransactionDataRulesV2", level="WARN", logger="o.a.c.loader.WebappClassLoaderBase", thread="main", log="The web application [ROOT] appears to have started a thread named [BatchSpanProcessor_WorkerThread-1] but has failed to stop it. This is very likely to create a memory leak. Stack trace of thread:
 java.base/jdk.internal.misc.Unsafe.park(Native Method)
 java.base/java.util.concurrent.locks.LockSupport.parkNanos(LockSupport.java:269)
 java.base/java.util.concurrent.locks.AbstractQueuedSynchronizer$ConditionObject.awaitNanos(AbstractQueuedSynchronizer.java:1763)
 java.base/java.util.concurrent.ArrayBlockingQueue.poll(ArrayBlockingQueue.java:435)
 io.opentelemetry.sdk.trace.export.BatchSpanProcessor$Worker.run(BatchSpanProcessor.java:266)
 java.base/java.lang.Thread.run(Thread.java:1583) "
timestamp="2025-12-19 17:32:10.231", apm="0011753", appname="TransactionDataRulesV2", level="INFO", logger="o.s.b.a.l.ConditionEvaluationReportLogger", thread="main", log="

Error starting ApplicationContext. To display the condition evaluation report re-run your application with 'debug' enabled. "
timestamp="2025-12-19 17:32:10.336", apm="0011753", appname="TransactionDataRulesV2", level="ERROR", logger="o.s.boot.SpringApplication", thread="main", log="Application run failed org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'entityManagerFactory' defined in class path resource [org/springframework/boot/autoconfigure/orm/jpa/HibernateJpaConfiguration.class]: Failed to instantiate [org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean]: Factory method 'entityManagerFactory' threw exception with message: Driver net.snowflake.client.jdbc.SnowflakeDriver claims to not accept jdbcUrl, ${DB_URL}
        at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:657)
        at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:645)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(AbstractAutowireCapableBeanFactory.java:1375)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1205)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:569)
        at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:529)
        at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:339)
        at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:373)
        at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:337)
        at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:207)
        at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:973)
        at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:627)
        at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146)
        at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:752)
        at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439)
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:318)
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:1361)
        at org.springframework.boot.SpringApplication.run(SpringApplication.java:1350)
        at com.fiserv.expdatarules.TransactionDataRulesApplication.main(TransactionDataRulesApplication.java:12)
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:103)
        at java.base/java.lang.reflect.Method.invoke(Method.java:580)
        at org.springframework.boot.loader.launch.Launcher.launch(Launcher.java:106)
        at org.springframework.boot.loader.launch.Launcher.launch(Launcher.java:64)
        at org.springframework.boot.loader.launch.JarLauncher.main(JarLauncher.java:40)
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean]: Factory method 'entityManagerFactory' threw exception with message: Driver net.snowflake.client.jdbc.SnowflakeDriver claims to not accept jdbcUrl, ${DB_URL}
        at org.springframework.beans.factory.support.SimpleInstantiationStrategy.lambda$instantiate$0(SimpleInstantiationStrategy.java:200)
        at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiateWithFactoryMethod(SimpleInstantiationStrategy.java:89)
        at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:169)
        at org.springframework.beans.factory.support.ConstructorResolver.instantiate(ConstructorResolver.java:653)
        ... 23 common frames omitted
Caused by: java.lang.RuntimeException: Driver net.snowflake.client.jdbc.SnowflakeDriver claims to not accept jdbcUrl, ${DB_URL}
        at com.zaxxer.hikari.util.DriverDataSource.<init>(DriverDataSource.java:116)
        at com.zaxxer.hikari.pool.PoolBase.initializeDataSource(PoolBase.java:333)
        at com.zaxxer.hikari.pool.PoolBase.<init>(PoolBase.java:118)
        at com.zaxxer.hikari.pool.HikariPool.<init>(HikariPool.java:90)
        at com.zaxxer.hikari.HikariDataSource.getConnection(HikariDataSource.java:111)
        at io.opentelemetry.instrumentation.jdbc.datasource.OpenTelemetryDataSource.wrapCall(OpenTelemetryDataSource.java:156)
        at io.opentelemetry.instrumentation.jdbc.datasource.OpenTelemetryDataSource.getConnection(OpenTelemetryDataSource.java:94)
        at org.springframework.boot.jdbc.EmbeddedDatabaseConnection.isEmbedded(EmbeddedDatabaseConnection.java:166)
        at org.springframework.boot.autoconfigure.orm.jpa.HibernateDefaultDdlAutoProvider.getDefaultDdlAuto(HibernateDefaultDdlAutoProvider.java:42)
        at org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaConfiguration.lambda$getVendorProperties$1(HibernateJpaConfiguration.java:149)
        at org.springframework.boot.autoconfigure.orm.jpa.HibernateSettings.getDdlAuto(HibernateSettings.java:41)
        at org.springframework.boot.autoconfigure.orm.jpa.HibernateProperties.determineDdlAuto(HibernateProperties.java:120)
        at org.springframework.boot.autoconfigure.orm.jpa.HibernateProperties.getAdditionalProperties(HibernateProperties.java:89)
        at org.springframework.boot.autoconfigure.orm.jpa.HibernateProperties.determineHibernateProperties(HibernateProperties.java:82)
        at org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaConfiguration.getVendorProperties(HibernateJpaConfiguration.java:150)
        at org.springframework.boot.autoconfigure.orm.jpa.JpaBaseConfiguration.buildJpaProperties(JpaBaseConfiguration.java:131)
        at org.springframework.boot.orm.jpa.EntityManagerFactoryBuilder$Builder.build(EntityManagerFactoryBuilder.java:304)
        at org.springframework.boot.autoconfigure.orm.jpa.JpaBaseConfiguration.entityManagerFactory(JpaBaseConfiguration.java:146)
        at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:103)
        at java.base/java.lang.reflect.Method.invoke(Method.java:580)
        at org.springframework.beans.factory.support.SimpleInstantiationStrategy.lambda$instantiate$0(SimpleInstantiationStrategy.java:172)
        ... 26 common frames omitted
"
api-webhook-vs-pub-sub $ kubectl get secrets -n omnidata
NAME                            TYPE     DATA   AGE
application-certs               Opaque   3      114s
transaction-data-rules-v2-env   Opaque   5      114s
api-webhook-vs-pub-sub $ kubectl get externalsecrets -n omnidata
NAME                                                    STORE                                          REFRESH INTERVAL   STATUS         READY
transaction-rules-v2-dev-java-chart-app-env             omnidata-external-secrets-clustersecretstore   1m                 SecretSynced   True
transaction-rules-v2-dev-java-chart-application-certs   omnidata-external-secrets-clustersecretstore   1m                 SecretSynced   True
api-webhook-vs-pub-sub $ kubectl get ^Cn omnidata
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
  resourceVersion: "11705722"
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
    lastTransitionTime: "2025-12-19T20:32:12Z"
    observedGeneration: 1
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-12-19T20:32:12Z"
    observedGeneration: 1
    status: "True"
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
    containerID: containerd://2ae9442a76d278d1a1ee785493006ead875f601235eb38bb4ecf94e7be9dc340
    image: fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2:2.0.0-20251218_174100-eff70050-SNAPSHOT
    imageID: fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2@sha256:6d067ae26974fcf0277c0aa206d6610af0e668ded32d2c2e167188bbd5863e1d
    lastState:
      terminated:
        containerID: containerd://6d245e0649fb5473a821477ddc80494c70e80a5754dfdcf9d55f98fab58a712e
        exitCode: 1
        finishedAt: "2025-12-19T20:32:10Z"
        reason: Error
        startedAt: "2025-12-19T20:31:13Z"
    name: java-chart
    ready: true
    resources:
      limits:
        cpu: 200m
        memory: 256Mi
      requests:
        cpu: 100m
        memory: 128Mi
    restartCount: 1
    started: true
    state:
      running:
        startedAt: "2025-12-19T20:32:11Z"
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
