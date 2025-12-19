api-webhook-vs-pub-sub $ aws secretsmanager describe-secret \
>   --region us-east-1 \
>   --secret-id 'omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD'

An error occurred (ResourceNotFoundException) when calling the DescribeSecret operation: Secrets Manager can't find the specified secret.
api-webhook-vs-pub-sub $ 
api-webhook-vs-pub-sub $ aws secretsmanager describe-secret \
>   --region us-east-1 \
>   --secret-id 'omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64'

An error occurred (ResourceNotFoundException) when calling the DescribeSecret operation: Secrets Manager can't find the specified secret.
api-webhook-vs-pub-sub $ kubectl -n omnidata get externalsecret transaction-rules-v2-dev-java-chart-app-env -o yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"external-secrets.io/v1beta1","kind":"ExternalSecret","metadata":{"annotations":{},"labels":{"app.kubernetes.io/instance":"transaction-rules-v2-dev","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"java-chart","app.kubernetes.io/version":"1.16.0","argocd.argoproj.io/instance":"transaction-rules-v2-dev","helm.sh/chart":"java-chart-0.1.0"},"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"spec":{"data":[{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD"},"secretKey":"KAFKA_KEYSTORE_PASSWORD"},{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/DB_URL"},"secretKey":"DB_URL"},{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/DB_USERNAME"},"secretKey":"DB_USERNAME"},{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/DB_PRIVATE_KEY_PASSWORD"},"secretKey":"DB_PRIVATE_KEY_PASSWORD"},{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/KAFKA_TRUSTSTORE_PASSWORD"},"secretKey":"KAFKA_TRUSTSTORE_PASSWORD"}],"refreshInterval":"1m","secretStoreRef":{"kind":"ClusterSecretStore","name":"omnidata-external-secrets-clustersecretstore"},"target":{"creationPolicy":"Owner","name":"transaction-data-rules-v2-env","template":{"type":"Opaque"}}}}
  creationTimestamp: "2025-12-19T18:54:21Z"
  generation: 2
  labels:
    app.kubernetes.io/instance: transaction-rules-v2-dev
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: java-chart
    app.kubernetes.io/version: 1.16.0
    argocd.argoproj.io/instance: transaction-rules-v2-dev
    helm.sh/chart: java-chart-0.1.0
  name: transaction-rules-v2-dev-java-chart-app-env
  namespace: omnidata
  resourceVersion: "11676187"
  uid: 93b726e4-fe1e-4e13-ac4b-cb621615488f
spec:
  data:
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD
      metadataPolicy: None
    secretKey: KAFKA_KEYSTORE_PASSWORD
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/DB_URL
      metadataPolicy: None
    secretKey: DB_URL
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/DB_USERNAME
      metadataPolicy: None
    secretKey: DB_USERNAME
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/DB_PRIVATE_KEY_PASSWORD
      metadataPolicy: None
    secretKey: DB_PRIVATE_KEY_PASSWORD
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/KAFKA_TRUSTSTORE_PASSWORD
      metadataPolicy: None
    secretKey: KAFKA_TRUSTSTORE_PASSWORD
  refreshInterval: 1m
  secretStoreRef:
    kind: ClusterSecretStore
    name: omnidata-external-secrets-clustersecretstore
  target:
    creationPolicy: Owner
    deletionPolicy: Retain
    name: transaction-data-rules-v2-env
    template:
      engineVersion: v2
      mergePolicy: Replace
      type: Opaque
status:
  conditions:
  - lastTransitionTime: "2025-12-19T18:54:23Z"
    message: could not get secret data from provider
    reason: SecretSyncedError
    status: "False"
    type: Ready
api-webhook-vs-pub-sub $ kubectl -n omnidata get externalsecret transaction-rules-v2-dev-java-chart-application-certs -o yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"external-secrets.io/v1beta1","kind":"ExternalSecret","metadata":{"annotations":{},"labels":{"app.kubernetes.io/instance":"transaction-rules-v2-dev","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"java-chart","app.kubernetes.io/version":"1.16.0","argocd.argoproj.io/instance":"transaction-rules-v2-dev","helm.sh/chart":"java-chart-0.1.0"},"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"spec":{"data":[{"remoteRef":{"decodingStrategy":"Base64","key":"omnidata/dev/transaction-rules-v2/env/wvcAp11753"},"secretKey":"wvcAp11753"},{"remoteRef":{"decodingStrategy":"Base64","key":"omnidata/dev/transaction-rules-v2/env/kafka-truststore"},"secretKey":"kafka-truststore"},{"remoteRef":{"decodingStrategy":"Base64","key":"omnidata/dev/transaction-rules-v2/env/snowflake-cert"},"secretKey":"snowflake-cert"}],"refreshInterval":"1m","secretStoreRef":{"kind":"ClusterSecretStore","name":"omnidata-external-secrets-clustersecretstore"},"target":{"creationPolicy":"Owner","name":"application-certs","template":{"type":"Opaque"}}}}
  creationTimestamp: "2025-12-19T18:54:21Z"
  generation: 3
  labels:
    app.kubernetes.io/instance: transaction-rules-v2-dev
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: java-chart
    app.kubernetes.io/version: 1.16.0
    argocd.argoproj.io/instance: transaction-rules-v2-dev
    helm.sh/chart: java-chart-0.1.0
  name: transaction-rules-v2-dev-java-chart-application-certs
  namespace: omnidata
  resourceVersion: "11682453"
  uid: 66a58373-355e-4c98-a369-5e26d9c6ab02
spec:
  data:
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: Base64
      key: omnidata/dev/transaction-rules-v2/env/wvcAp11753
      metadataPolicy: None
    secretKey: wvcAp11753
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: Base64
      key: omnidata/dev/transaction-rules-v2/env/kafka-truststore
      metadataPolicy: None
    secretKey: kafka-truststore
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: Base64
      key: omnidata/dev/transaction-rules-v2/env/snowflake-cert
      metadataPolicy: None
    secretKey: snowflake-cert
  refreshInterval: 1m
  secretStoreRef:
    kind: ClusterSecretStore
    name: omnidata-external-secrets-clustersecretstore
  target:
    creationPolicy: Owner
    deletionPolicy: Retain
    name: application-certs
    template:
      engineVersion: v2
      mergePolicy: Replace
      type: Opaque
status:
  conditions:
  - lastTransitionTime: "2025-12-19T18:54:23Z"
    message: could not get secret data from provider
    reason: SecretSyncedError
    status: "False"
    type: Ready
api-webhook-vs-pub-sub $ kubectl get clustersecretstore omnidata-external-secrets-clustersecretstore -o yaml
apiVersion: external-secrets.io/v1beta1
kind: ClusterSecretStore
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"external-secrets.io/v1beta1","kind":"ClusterSecretStore","metadata":{"annotations":{},"labels":{"argocd.argoproj.io/instance":"secret-store"},"name":"omnidata-external-secrets-clustersecretstore"},"spec":{"provider":{"aws":{"auth":{"jwt":{"serviceAccountRef":{"name":"svc-cluster-secret-store-secret-manager","namespace":"omnidata"}}},"region":"us-east-1","service":"SecretsManager"}}}}
  creationTimestamp: "2025-12-19T18:47:43Z"
  generation: 1
  labels:
    argocd.argoproj.io/instance: secret-store
  name: omnidata-external-secrets-clustersecretstore
  resourceVersion: "11671266"
  uid: 40606b8d-4a78-4f5c-9f41-50a54a473774
spec:
  provider:
    aws:
      auth:
        jwt:
          serviceAccountRef:
            name: svc-cluster-secret-store-secret-manager
            namespace: omnidata
      region: us-east-1
      service: SecretsManager
status:
  capabilities: ReadWrite
  conditions:
  - lastTransitionTime: "2025-12-19T18:47:43Z"
    message: store validated
    reason: Valid
    status: "True"
    type: Ready
api-webhook-vs-pub-sub $ kubectl describe clustersecretstore omnidata-external-secrets-clustersecretstore
Name:         omnidata-external-secrets-clustersecretstore
Namespace:    
Labels:       argocd.argoproj.io/instance=secret-store
Annotations:  <none>
API Version:  external-secrets.io/v1beta1
Kind:         ClusterSecretStore
Metadata:
  Creation Timestamp:  2025-12-19T18:47:43Z
  Generation:          1
  Resource Version:    11671266
  UID:                 40606b8d-4a78-4f5c-9f41-50a54a473774
Spec:
  Provider:
    Aws:
      Auth:
        Jwt:
          Service Account Ref:
            Name:       svc-cluster-secret-store-secret-manager
            Namespace:  omnidata
      Region:           us-east-1
      Service:          SecretsManager
Status:
  Capabilities:  ReadWrite
  Conditions:
    Last Transition Time:  2025-12-19T18:47:43Z
    Message:               store validated
    Reason:                Valid
    Status:                True
    Type:                  Ready
Events:
  Type    Reason  Age                   From                  Message
  ----    ------  ----                  ----                  -------
  Normal  Valid   3m13s (x19 over 88m)  cluster-secret-store  store validated
  Normal  Valid   3m13s (x19 over 88m)  cluster-secret-store  store validated
api-webhook-vs-pub-sub $ kubectl -n argocd get sa external-secrets -o yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ServiceAccount","metadata":{"annotations":{},"labels":{"app.kubernetes.io/instance":"cdr-external-secret-operator","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"external-secrets","app.kubernetes.io/version":"v0.10.5","argocd.argoproj.io/instance":"cdr-external-secret-operator","helm.sh/chart":"external-secrets-0.10.5"},"name":"external-secrets","namespace":"argocd"}}
  creationTimestamp: "2025-12-19T18:46:55Z"
  labels:
    app.kubernetes.io/instance: cdr-external-secret-operator
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: external-secrets
    app.kubernetes.io/version: v0.10.5
    argocd.argoproj.io/instance: cdr-external-secret-operator
    helm.sh/chart: external-secrets-0.10.5
  name: external-secrets
  namespace: argocd
  resourceVersion: "11670800"
  uid: 97fcc07a-8a33-44f1-8a18-f42ff5bed579
api-webhook-vs-pub-sub $ kubectl get clustersecretstore omnidata-external-secrets-clustersecretstore -o yaml
apiVersion: external-secrets.io/v1beta1
kind: ClusterSecretStore
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"external-secrets.io/v1beta1","kind":"ClusterSecretStore","metadata":{"annotations":{},"labels":{"argocd.argoproj.io/instance":"secret-store"},"name":"omnidata-external-secrets-clustersecretstore"},"spec":{"provider":{"aws":{"auth":{"jwt":{"serviceAccountRef":{"name":"svc-cluster-secret-store-secret-manager","namespace":"omnidata"}}},"region":"us-east-1","service":"SecretsManager"}}}}
  creationTimestamp: "2025-12-19T18:47:43Z"
  generation: 1
  labels:
    argocd.argoproj.io/instance: secret-store
  name: omnidata-external-secrets-clustersecretstore
  resourceVersion: "11671266"
  uid: 40606b8d-4a78-4f5c-9f41-50a54a473774
spec:
  provider:
    aws:
      auth:
        jwt:
          serviceAccountRef:
            name: svc-cluster-secret-store-secret-manager
            namespace: omnidata
      region: us-east-1
      service: SecretsManager
status:
  capabilities: ReadWrite
  conditions:
  - lastTransitionTime: "2025-12-19T18:47:43Z"
    message: store validated
    reason: Valid
    status: "True"
    type: Ready
api-webhook-vs-pub-sub $ 
api-webhook-vs-pub-sub $ kubectl -n omnidata get externalsecret transaction-rules-v2-dev-java-chart-app-env -o yaml
apiVersion: external-secrets.io/v1beta1
kind: ExternalSecret
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"external-secrets.io/v1beta1","kind":"ExternalSecret","metadata":{"annotations":{},"labels":{"app.kubernetes.io/instance":"transaction-rules-v2-dev","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"java-chart","app.kubernetes.io/version":"1.16.0","argocd.argoproj.io/instance":"transaction-rules-v2-dev","helm.sh/chart":"java-chart-0.1.0"},"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"spec":{"data":[{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD"},"secretKey":"KAFKA_KEYSTORE_PASSWORD"},{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/DB_URL"},"secretKey":"DB_URL"},{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/DB_USERNAME"},"secretKey":"DB_USERNAME"},{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/DB_PRIVATE_KEY_PASSWORD"},"secretKey":"DB_PRIVATE_KEY_PASSWORD"},{"remoteRef":{"key":"omnidata/dev/transaction-rules-v2/env/KAFKA_TRUSTSTORE_PASSWORD"},"secretKey":"KAFKA_TRUSTSTORE_PASSWORD"}],"refreshInterval":"1m","secretStoreRef":{"kind":"ClusterSecretStore","name":"omnidata-external-secrets-clustersecretstore"},"target":{"creationPolicy":"Owner","name":"transaction-data-rules-v2-env","template":{"type":"Opaque"}}}}
  creationTimestamp: "2025-12-19T18:54:21Z"
  generation: 2
  labels:
    app.kubernetes.io/instance: transaction-rules-v2-dev
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: java-chart
    app.kubernetes.io/version: 1.16.0
    argocd.argoproj.io/instance: transaction-rules-v2-dev
    helm.sh/chart: java-chart-0.1.0
  name: transaction-rules-v2-dev-java-chart-app-env
  namespace: omnidata
  resourceVersion: "11676187"
  uid: 93b726e4-fe1e-4e13-ac4b-cb621615488f
spec:
  data:
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD
      metadataPolicy: None
    secretKey: KAFKA_KEYSTORE_PASSWORD
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/DB_URL
      metadataPolicy: None
    secretKey: DB_URL
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/DB_USERNAME
      metadataPolicy: None
    secretKey: DB_USERNAME
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/DB_PRIVATE_KEY_PASSWORD
      metadataPolicy: None
    secretKey: DB_PRIVATE_KEY_PASSWORD
  - remoteRef:
      conversionStrategy: Default
      decodingStrategy: None
      key: omnidata/dev/transaction-rules-v2/env/KAFKA_TRUSTSTORE_PASSWORD
      metadataPolicy: None
    secretKey: KAFKA_TRUSTSTORE_PASSWORD
  refreshInterval: 1m
  secretStoreRef:
    kind: ClusterSecretStore
    name: omnidata-external-secrets-clustersecretstore
  target:
    creationPolicy: Owner
    deletionPolicy: Retain
    name: transaction-data-rules-v2-env
    template:
      engineVersion: v2
      mergePolicy: Replace
      type: Opaque
status:
  conditions:
  - lastTransitionTime: "2025-12-19T18:54:23Z"
    message: could not get secret data from provider
    reason: SecretSyncedError
    status: "False"
    type: Ready
api-webhook-vs-pub-sub $ 
api-webhook-vs-pub-sub $ kubectl -n argocd get sa external-secrets -o yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","kind":"ServiceAccount","metadata":{"annotations":{},"labels":{"app.kubernetes.io/instance":"cdr-external-secret-operator","app.kubernetes.io/managed-by":"Helm","app.kubernetes.io/name":"external-secrets","app.kubernetes.io/version":"v0.10.5","argocd.argoproj.io/instance":"cdr-external-secret-operator","helm.sh/chart":"external-secrets-0.10.5"},"name":"external-secrets","namespace":"argocd"}}
  creationTimestamp: "2025-12-19T18:46:55Z"
  labels:
    app.kubernetes.io/instance: cdr-external-secret-operator
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: external-secrets
    app.kubernetes.io/version: v0.10.5
    argocd.argoproj.io/instance: cdr-external-secret-operator
    helm.sh/chart: external-secrets-0.10.5
  name: external-secrets
  namespace: argocd
  resourceVersion: "11670800"
  uid: 97fcc07a-8a33-44f1-8a18-f42ff5bed579
api-webhook-vs-pub-sub $ 
