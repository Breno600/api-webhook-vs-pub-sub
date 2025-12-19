~ $ kubectl get crd | grep external-secrets
acraccesstokens.generators.external-secrets.io          2025-12-19T18:46:56Z
clusterexternalsecrets.external-secrets.io              2025-12-19T18:46:56Z
clustersecretstores.external-secrets.io                 2025-12-19T18:46:56Z
ecrauthorizationtokens.generators.external-secrets.io   2025-12-19T18:46:56Z
externalsecrets.external-secrets.io                     2025-12-19T18:46:56Z
fakes.generators.external-secrets.io                    2025-12-19T18:46:56Z
gcraccesstokens.generators.external-secrets.io          2025-12-19T18:46:56Z
githubaccesstokens.generators.external-secrets.io       2025-12-19T18:46:56Z
passwords.generators.external-secrets.io                2025-12-19T18:46:56Z
pushsecrets.external-secrets.io                         2025-12-19T18:46:56Z
secretstores.external-secrets.io                        2025-12-19T18:46:56Z
uuids.generators.external-secrets.io                    2025-12-19T18:46:56Z
vaultdynamicsecrets.generators.external-secrets.io      2025-12-19T18:46:56Z
webhooks.generators.external-secrets.io                 2025-12-19T18:46:56Z
~ $ kubectl get pods -n external-secrets
No resources found in external-secrets namespace.
~ $ kubectl get deploy -n external-secrets
No resources found in external-secrets namespace.
~ $ 
~ $ kubectl get externalsecret -n omnidata
NAME                                                    STORE                                          REFRESH INTERVAL   STATUS              READY
transaction-rules-v2-dev-java-chart-app-env             omnidata-external-secrets-clustersecretstore   1m                 SecretSyncedError   False
transaction-rules-v2-dev-java-chart-application-certs   omnidata-external-secrets-clustersecretstore   1m                 SecretSyncedError   False
~ $ kubectl describe externalsecret -n omnidata transaction-rules-v2-dev-java-chart-application-certs
Name:         transaction-rules-v2-dev-java-chart-application-certs
Namespace:    omnidata
Labels:       app.kubernetes.io/instance=transaction-rules-v2-dev
              app.kubernetes.io/managed-by=Helm
              app.kubernetes.io/name=java-chart
              app.kubernetes.io/version=1.16.0
              argocd.argoproj.io/instance=transaction-rules-v2-dev
              helm.sh/chart=java-chart-0.1.0
Annotations:  <none>
API Version:  external-secrets.io/v1beta1
Kind:         ExternalSecret
Metadata:
  Creation Timestamp:  2025-12-19T18:54:21Z
  Generation:          2
  Resource Version:    11676188
  UID:                 66a58373-355e-4c98-a369-5e26d9c6ab02
Spec:
  Data:
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    Base64
      Key:                  omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64
      Metadata Policy:      None
    Secret Key:             wvcAp11753
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    Base64
      Key:                  omnidata/dev/transaction-rules-v2/env/kafka-truststore_b64
      Metadata Policy:      None
    Secret Key:             kafka-truststore
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    Base64
      Key:                  omnidata/dev/transaction-rules-v2/env/snowflake-cert_b64
      Metadata Policy:      None
    Secret Key:             snowflake-cert
  Refresh Interval:         1m
  Secret Store Ref:
    Kind:  ClusterSecretStore
    Name:  omnidata-external-secrets-clustersecretstore
  Target:
    Creation Policy:  Owner
    Deletion Policy:  Retain
    Name:             application-certs
    Template:
      Engine Version:  v2
      Merge Policy:    Replace
      Type:            Opaque
Status:
  Conditions:
    Last Transition Time:  2025-12-19T18:54:23Z
    Message:               could not get secret data from provider
    Reason:                SecretSyncedError
    Status:                False
    Type:                  Ready
Events:
  Type     Reason        Age                 From              Message
  ----     ------        ----                ----              -------
  Warning  UpdateFailed  12m (x21 over 23m)  external-secrets  error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist
  Warning  UpdateFailed  12m (x21 over 23m)  external-secrets  error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist
~ $ 
~ $ 
