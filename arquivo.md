Report generated at: eso_report.txt
Date: 2025-12-19T19:44:11+00:00
APP_NS=omnidata
CSS_NAME=omnidata-external-secrets-clustersecretstore


==================== 00-current-context ====================
Report generated at: eso_report.txt
Date: 2025-12-19T19:44:11+00:00
APP_NS=omnidata
CSS_NAME=omnidata-external-secrets-clustersecretstore


==================== 00-current-context ====================

+ kubectl config current-context
arn:aws:eks:us-east-1:909139952102:cluster/sit-omnidata-apm0011753-eks


==================== 01-cluster-info ====================

+ kubectl cluster-info
Kubernetes control plane is running at https://EB5DD51801CB3D0100E96F2126345668.gr7.us-east-1.eks.amazonaws.com

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.


==================== 02-kubectl-version ====================

+ kubectl version --short
error: unknown flag: --short
See 'kubectl version --help' for usage.


==================== 10-crds-eso ====================

+ kubectl get crd | egrep -i 'external-secrets|externalsecret|secretstore|clustersecretstore|clusterexternalsecret' || true
acraccesstokens.generators.external-secrets.io          2025-12-19T18:46:56Z
clusterexternalsecrets.external-secrets.io              2025-12-19T18:46:56Z
clustersecretstores.external-secrets.io                 2025-12-19T18:46:56Z
ecrauthorizationtokens.generators.external-secrets.io   2025-12-19T18:46:56Z
externalsecrets.external-secrets.io                     2025-12-19T18:46:56Z
fakes.generators.external-secrets.io                    2025-12-19T18:46:56Z
gcraccesstokens.generators.external-secrets.io          2025-12-19T18:46:56Z
Report generated at: eso_report.txt
Date: 2025-12-19T19:44:11+00:00
APP_NS=omnidata
CSS_NAME=omnidata-external-secrets-clustersecretstore


==================== 00-current-context ====================

+ kubectl config current-context
arn:aws:eks:us-east-1:909139952102:cluster/sit-omnidata-apm0011753-eks


==================== 01-cluster-info ====================

+ kubectl cluster-info
Kubernetes control plane is running at https://EB5DD51801CB3D0100E96F2126345668.gr7.us-east-1.eks.amazonaws.com

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.


==================== 02-kubectl-version ====================

+ kubectl version --short
error: unknown flag: --short
See 'kubectl version --help' for usage.


==================== 10-crds-eso ====================

+ kubectl get crd | egrep -i 'external-secrets|externalsecret|secretstore|clustersecretstore|clusterexternalsecret' || true
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


==================== 11-api-resources-eso ====================
==================== 11-api-resources-eso ====================

+ kubectl api-resources | egrep -i 'externalsecret|secretstore|clustersecretstore|application' || true
applications                        app,apps                argoproj.io/v1alpha1                      true         Application
applicationsets                     appset,appsets          argoproj.io/v1alpha1                      true         ApplicationSet
clusterexternalsecrets              ces                     external-secrets.io/v1beta1               false        ClusterExternalSecret
clustersecretstores                 css                     external-secrets.io/v1beta1               false        ClusterSecretStore
externalsecrets                     es                      external-secrets.io/v1beta1               true         ExternalSecret
secretstores                        ss                      external-secrets.io/v1beta1               true         SecretStore
applicationnetworkpolicies          anp                     networking.k8s.aws/v1alpha1               true         ApplicationNetworkPolicy


==================== 20-find-deploy-eso ====================

+ kubectl get deploy -A | egrep -i 'external-secrets|externalsecrets|\beso\b' || true
argocd          cdr-external-secret-operator-external-secrets                   2/2     2            2           57m
argocd          cdr-external-secret-operator-external-secrets-cert-controller   1/1     1            1           57m
argocd          cdr-external-secret-operator-external-secrets-webhook           1/1     1            1           57m


==================== 21-find-pods-eso ====================

+ kubectl get pods  -A | egrep -i 'external-secrets|externalsecrets|\beso\b' || true
argocd             cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h     1/1     Running             0                57m
argocd             cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs     1/1     Running             0                57m
argocd             cdr-external-secret-operator-external-secrets-cert-controllmwm9   1/1     Running             0                57m
argocd             cdr-external-secret-operator-external-secrets-webhook-6b696qk4j   1/1     Running             0                57m


==================== 22-find-svc-eso ====================

+ kubectl get svc   -A | egrep -i 'external-secrets|externalsecrets|\beso\b' || true
argocd          cdr-external-secret-operator-external-secrets-webhook          ClusterIP   172.20.238.226   <none>        443/TCP          57m


==================== 23-eso-deploys-detected ====================

argocd/cdr-external-secret-operator-external-secrets
argocd/cdr-external-secret-operator-external-secrets-cert-controller
argocd/cdr-external-secret-operator-external-secrets-webhook

==================== 30-1-eso-all-in-ns-argocd ====================

+ kubectl get all -n argocd
NAME                                                                  READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0                                   1/1     Running   0          2d20h
pod/argocd-applicationset-controller-788d477d88-9bqcm                 1/1     Running   0          2d20h
pod/argocd-redis-77fb75bcd9-rngdx                                     1/1     Running   0          2d20h
pod/argocd-repo-server-67b754fcff-x5nrv                               1/1     Running   0          2d20h
pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h     1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs     1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9   1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j   1/1     Running   0          57m
pod/gitops-agent-5fc75b4fd9-h82tw                                     1/1     Running   0          2d20h
pod/gitops-agent-upgrader-29436000-7q8kw                              0/1     Error     0          3h38m
pod/gitops-agent-upgrader-29436000-dlc22                              0/1     Error     0          3h42m
pod/gitops-agent-upgrader-29436000-nbjbj                              0/1     Error     0          3h33m
pod/gitops-agent-upgrader-29436000-qv5ns                              0/1     Error     0          3h41m
pod/gitops-agent-upgrader-29436000-tk62s                              0/1     Error     0          3h43m
pod/gitops-agent-upgrader-29436000-vjdwk                              0/1     Error     0          3h44m
pod/gitops-agent-upgrader-29436000-x9zfk                              0/1     Error     0          3h44m

NAME                                                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/argocd-applicationset-controller                        ClusterIP   172.20.86.17     <none>        7000/TCP         23d
service/argocd-applicationset-controller-metrics                ClusterIP   172.20.153.140   <none>        8080/TCP         23d
service/argocd-redis                                            ClusterIP   172.20.26.33     <none>        6379/TCP         23d
service/argocd-repo-server                                      ClusterIP   172.20.33.21     <none>        8081/TCP         23d
service/argocd-repo-server-metrics                              ClusterIP   172.20.137.34    <none>        8084/TCP         23d
service/argocd-server                                           ClusterIP   172.20.33.91     <none>        80/TCP,443/TCP   23d
service/cdr-external-secret-operator-external-secrets-webhook   ClusterIP   172.20.238.226   <none>        443/TCP          57m
service/gitops-agent-metrics                                    ClusterIP   172.20.250.144   <none>        2112/TCP         23d

NAME                                                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-applicationset-controller                                1/1     1            1           23d
deployment.apps/argocd-redis                                                    1/1     1            1           23d
deployment.apps/argocd-repo-server                                              1/1     1            1           23d
deployment.apps/argocd-server                                                   0/0     0            0           23d
deployment.apps/cdr-external-secret-operator-external-secrets                   2/2     2            2           57m
deployment.apps/cdr-external-secret-operator-external-secrets-cert-controller   1/1     1            1           57m
deployment.apps/cdr-external-secret-operator-external-secrets-webhook           1/1     1            1           57m
deployment.apps/gitops-agent                                                    1/1     1            1           23d

NAME                                                                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-applicationset-controller-788d477d88                               1         1         1       23d
replicaset.apps/argocd-redis-77fb75bcd9                                                   1         1         1       23d
replicaset.apps/argocd-repo-server-67b754fcff                                             1         1         1       23d
replicaset.apps/argocd-server-69dc89948c                                                  0         0         0       23d
replicaset.apps/cdr-external-secret-operator-external-secrets-9c858c9c8                   2         2         2       57m
replicaset.apps/cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95   1         1         1       57m
replicaset.apps/cdr-external-secret-operator-external-secrets-webhook-6b69586684          1         1         1       57m
replicaset.apps/gitops-agent-5fc75b4fd9                                                   1         1         1       23d

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     23d

NAME                                  SCHEDULE      TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/gitops-agent-upgrader   0 */4 * * *   <none>     False     0        3h44m           23d

NAME                                       STATUS   COMPLETIONS   DURATION   AGE
job.batch/gitops-agent-upgrader-29436000   Failed   0/1           3h44m      3h44m


==================== 31-1-eso-deploy-describe-argocd/cdr-external-secret-operator-external-secrets ====================

+ kubectl describe deploy -n argocd cdr-external-secret-operator-external-secrets
Name:                   cdr-external-secret-operator-external-secrets
Namespace:              argocd
CreationTimestamp:      Fri, 19 Dec 2025 18:47:01 +0000
Labels:                 app.kubernetes.io/instance=cdr-external-secret-operator
                        app.kubernetes.io/managed-by=Helm
                        app.kubernetes.io/name=external-secrets
                        app.kubernetes.io/version=v0.10.5
                        argocd.argoproj.io/instance=cdr-external-secret-operator
                        helm.sh/chart=external-secrets-0.10.5
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app.kubernetes.io/instance=cdr-external-secret-operator,app.kubernetes.io/name=external-secrets
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:           app.kubernetes.io/instance=cdr-external-secret-operator
                    app.kubernetes.io/managed-by=Helm
                    app.kubernetes.io/name=external-secrets
                    app.kubernetes.io/version=v0.10.5
                    helm.sh/chart=external-secrets-0.10.5
  Service Account:  external-secrets
  Containers:
   external-secrets:
    Image:           oci.external-secrets.io/external-secrets/external-secrets:v0.10.5
    Port:            8080/TCP (metrics)
    Host Port:       0/TCP (metrics)
    SeccompProfile:  RuntimeDefault
    Args:
      --concurrent=1
      --metrics-addr=:8080
      --loglevel=info
      --zap-time-encoding=epoch
    Limits:
      cpu:     300m
      memory:  256Mi
    Requests:
      cpu:         100m
      memory:      128Mi
    Limits:
      cpu:     300m
      memory:  256Mi
    Requests:
      cpu:         100m
      memory:      128Mi
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   cdr-external-secret-operator-external-secrets-9c858c9c8 (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  57m   deployment-controller  Scaled up replica set cdr-external-secret-operator-external-secrets-9c858c9c8 from 0 to 2


==================== 32-1-eso-logs-tail-argocd/cdr-external-secret-operator-external-secrets ====================

+ kubectl logs -n argocd deploy/cdr-external-secret-operator-external-secrets --tail=400
Found 2 pods, using pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h
{"level":"info","ts":1766170035.9490266,"logger":"provider.vault","msg":"initializing vault cache","size":262144}
{"level":"info","ts":1766170035.949063,"logger":"setup","msg":"starting manager"}
{"level":"info","ts":1766170035.9492862,"logger":"controller-runtime.metrics","msg":"Starting metrics server"}
{"level":"info","ts":1766170035.9496806,"logger":"controller-runtime.metrics","msg":"Serving metrics server","bindAddress":":8080","secure":false}
{"level":"info","ts":1766170036.0497086,"msg":"Starting EventSource","controller":"secretstore","controllerGroup":"external-secrets.io","controllerKind":"SecretStore","source":"kind source: *v1beta1.SecretStore"}
{"level":"info","ts":1766170036.0497472,"msg":"Starting Controller","controller":"secretstore","controllerGroup":"external-secrets.io","controllerKind":"SecretStore"}
{"level":"info","ts":1766170036.049778,"msg":"Starting EventSource","controller":"pushsecret","controllerGroup":"external-secrets.io","controllerKind":"PushSecret","source":"kind source: *v1alpha1.PushSecret"}
{"level":"info","ts":1766170036.0497816,"msg":"Starting EventSource","controller":"clustersecretstore","controllerGroup":"external-secrets.io","controllerKind":"ClusterSecretStore","source":"kind source: *v1beta1.ClusterSecretStore"}
{"level":"info","ts":1766170036.0497963,"msg":"Starting Controller","controller":"pushsecret","controllerGroup":"external-secrets.io","controllerKind":"PushSecret"}
{"level":"info","ts":1766170036.0498006,"msg":"Starting Controller","controller":"clustersecretstore","controllerGroup":"external-secrets.io","controllerKind":"ClusterSecretStore"}
{"level":"info","ts":1766170036.049889,"msg":"Starting EventSource","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret","source":"kind source: *v1beta1.ClusterExtern:
{"level":"info","ts":1766170036.0499175,"msg":"Starting EventSource","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret","source":"kind source: *v1beta1.ExternalSecret"}
{"level":"info","ts":1766170036.0499275,"msg":"Starting EventSource","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret","source":"kind source: *v1.Namespace"}
{"level":"info","ts":1766170036.049944,"msg":"Starting Controller","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret"}
{"level":"info","ts":1766170036.049971,"msg":"Starting EventSource","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","source":"kind source: *v1beta1.ExternalSecret"}
{"level":"info","ts":1766170036.0499842,"msg":"Starting EventSource","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","source":"kind source: *v1.PartialObjectMetadata"}
{"level":"info","ts":1766170036.0499887,"msg":"Starting Controller","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret"}
{"level":"info","ts":1766170036.1510606,"msg":"Starting workers","controller":"secretstore","controllerGroup":"external-secrets.io","controllerKind":"SecretStore","worker count":1}
{"level":"info","ts":1766170036.1560056,"msg":"Starting workers","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","worker count":1}
{"level":"info","ts":1766170036.1560507,"msg":"Starting workers","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret","worker count":1}
{"level":"info","ts":1766170036.1561027,"msg":"Starting workers","controller":"clustersecretstore","controllerGroup":"external-secrets.io","controllerKind":"ClusterSecretStore","worker count":1}
{"level":"info","ts":1766170036.1561563,"msg":"Starting workers","controller":"pushsecret","controllerGroup":"external-secrets.io","controllerKind":"PushSecret","worker count":1}
{"level":"info","ts":1766170063.4394076,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170063.8895025,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170363.8908007,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170462.0712838,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170462.0713205,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766170462.854969,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766170462.8669548,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-app-env","reconcileID":"3c89f43d-5eb7-410f-b7b0-dd5fa2a76424","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766170462.8823464,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170462.882399,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766170463.3106272,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Recon:
{"level":"error","ts":1766170463.3106272,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766170463.3254461,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-application-certs","reconcileID":"af8aec4f-f3d5-448a-b294-fea4db3591c0","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766170463.339693,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170463.339737,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766170463.788403,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766170463.8108432,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-app-env","reconcileID":"9ae5f775-573b-4240-884e-3fd61f26160d","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766170463.8281755,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170463.828219,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766170464.277838,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766170464.2884085,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-application-certs","reconcileID":"c1f79790-00aa-4d5f-8a01-10e524f70bbd","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766170464.3024924,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766170464.7777355,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-app-env","reconcileID":"81712c1d-57d0-44d7-ac11-1d74ac8c5dd8","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766170464.7919765,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170464.7920103,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766170465.2456722,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766170465.257083,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-application-certs","reconcileID":"9f24d2c2-a926-4920-b2bf-c319f1908681","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766170465.2695606,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170465.2696116,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766170465.694692,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766170465.7061589,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-app-env","reconcileID":"a1236114-64d7-48c6-8a41-4d636b89925d","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766170465.7206702,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170465.7207012,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766170466.1495426,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go:
y: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/contro
cile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg
/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-run
time@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/co
ntroller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766172131.5744882,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart
-application-certs","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-application-certs","reconcileID":"ce5c5aff-6d1d-47bc-bc28-caa71220cf30","error":"error retrieving secret at .data[0], ke
y: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/contro
ller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/int
ernal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224
"}
{"level":"info","ts":1766172165.9708085,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766172466.10887,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766172766.5420706,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766173067.0222478,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766173131.1090615,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766173131.109105,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766173131.8975465,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766173131.911145,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-app-env","reconcileID":"192bc2e1-ba39-4ebd-a3b7-93161481331c","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766173131.9247768,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766173131.924819,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/wvcAp11753","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766173132.3427713,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766173132.3609753,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-application-certs","reconcileID":"b790d0cb-63aa-48dd-a607-669e83133f7f","error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224:
==================== 33-1-eso-logs-since-60m-argocd/cdr-external-secret-operator-external-secrets ====================

+ kubectl logs -n argocd deploy/cdr-external-secret-operator-external-secrets --since=60m
Found 2 pods, using pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h
{"level":"info","ts":1766170035.9490266,"logger":"provider.vault","msg":"initializing vault cache","size":262144}
{"level":"info","ts":1766170035.949063,"logger":"setup","msg":"starting manager"}
{"level":"info","ts":1766170035.9492862,"logger":"controller-runtime.metrics","msg":"Starting metrics server"}
{"level":"info","ts":1766170035.9496806,"logger":"controller-runtime.metrics","msg":"Serving metrics server","bindAddress":":8080","secure":false}
{"level":"info","ts":1766170036.0497086,"msg":"Starting EventSource","controller":"secretstore","controllerGroup":"external-secrets.io","controllerKind":"SecretStore","source":"kind source: *v1beta1.SecretStore"}
{"level":"info","ts":1766170036.0497472,"msg":"Starting Controller","controller":"secretstore","controllerGroup":"external-secrets.io","controllerKind":"SecretStore"}
{"level":"info","ts":1766170036.049778,"msg":"Starting EventSource","controller":"pushsecret","controllerGroup":"external-secrets.io","controllerKind":"PushSecret","source":"kind source: *v1alpha1.PushSecret"}
{"level":"info","ts":1766170036.0497816,"msg":"Starting EventSource","controller":"clustersecretstore","controllerGroup":"external-secrets.io","controllerKind":"ClusterSecretStore","source":"kind source: *v1beta1.ClusterSecretStore"}
{"level":"info","ts":1766170036.0497963,"msg":"Starting Controller","controller":"pushsecret","controllerGroup":"external-secrets.io","controllerKind":"PushSecret"}
{"level":"info","ts":1766170036.0498006,"msg":"Starting Controller","controller":"clustersecretstore","controllerGroup":"external-secrets.io","controllerKind":"ClusterSecretStore"}
{"level":"info","ts":1766170036.049889,"msg":"Starting EventSource","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret","source":"kind source: *v1beta1.ClusterExternalSecret"}
{"level":"info","ts":1766170036.0499175,"msg":"Starting EventSource","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret","source":"kind source: *v1beta1.ExternalSecret"}
{"level":"info","ts":1766170036.0499275,"msg":"Starting EventSource","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret","source":"kind source: *v1.Namespace"}
{"level":"info","ts":1766170036.049944,"msg":"Starting Controller","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret"}
{"level":"info","ts":1766170036.049971,"msg":"Starting EventSource","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","source":"kind source: *v1beta1.ExternalSecret"}
{"level":"info","ts":1766170036.0499842,"msg":"Starting EventSource","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","source":"kind source: *v1.PartialObjectMetadata"}
{"level":"info","ts":1766170036.0499887,"msg":"Starting Controller","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret"}
{"level":"info","ts":1766170036.1510606,"msg":"Starting workers","controller":"secretstore","controllerGroup":"external-secrets.io","controllerKind":"SecretStore","worker count":1}
{"level":"info","ts":1766170036.1560056,"msg":"Starting workers","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","worker count":1}
{"level":"info","ts":1766170036.1560507,"msg":"Starting workers","controller":"clusterexternalsecret","controllerGroup":"external-secrets.io","controllerKind":"ClusterExternalSecret","worker count":1}
{"level":"info","ts":1766170036.1561027,"msg":"Starting workers","controller":"clustersecretstore","controllerGroup":"external-secrets.io","controllerKind":"ClusterSecretStore","worker count":1}
{"level":"info","ts":1766170036.1561563,"msg":"Starting workers","controller":"pushsecret","controllerGroup":"external-secrets.io","controllerKind":"PushSecret","worker count":1}
{"level":"info","ts":1766170063.4394076,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170063.8895025,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170363.8908007,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170462.0712838,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766170462.0713205,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766170462.854969,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Reconcile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}

{"level":"info","ts":1766172165.9708085,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766172466.10887,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766172766.5420706,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766173067.0222478,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766173131.1090615,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766173131.109105,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766173131.8975465,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-app-env","namespace":"omnidata"},"error":"
error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconc
iler).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Re
concile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/
pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-
runtime@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal
/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766173131.911145,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-
app-env","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-app-env","reconcileID":"192bc2e1-ba39-4ebd-a3b7-93161481331c","error":"error retrieving secret at .data[0], key: omnidata/dev/trans
action-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-run
time@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/co
ntroller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"info","ts":1766173131.9247768,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}
{"level":"info","ts":1766173131.924819,"logger":"provider.aws.secretsmanager","msg":"fetching secret value","key":"omnidata/dev/transaction-rules-v2/env/wvcAp11753","version":"AWSCURRENT","value":"SECRET"}
{"level":"error","ts":1766173132.3427713,"logger":"controllers.ExternalSecret","msg":"could not get secret data from provider","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart-application-certs","namespace":"omnidata"}
,"error":"error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist","stacktrace":"github.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconcile
r).markAsFailed\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:360\ngithub.com/external-secrets/external-secrets/pkg/controllers/externalsecret.(*Reconciler).Recon
cile\n\t/home/runner/work/external-secrets/external-secrets/pkg/controllers/externalsecret/externalsecret_controller.go:229\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Reconcile\n\t/home/runner/go/pkg
/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:116\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-run
time@v0.19.0/pkg/internal/controller/controller.go:303\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/co
ntroller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224"}
{"level":"error","ts":1766173132.3609753,"msg":"Reconciler error","controller":"externalsecret","controllerGroup":"external-secrets.io","controllerKind":"ExternalSecret","ExternalSecret":{"name":"transaction-rules-v2-dev-java-chart
-application-certs","namespace":"omnidata"},"namespace":"omnidata","name":"transaction-rules-v2-dev-java-chart-application-certs","reconcileID":"b790d0cb-63aa-48dd-a607-669e83133f7f","error":"error retrieving secret at .data[0], ke
y: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist","stacktrace":"sigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).reconcileHandler\n\t/home/runner/go/pkg/mod/sigs.k8s.io/contro
ller-runtime@v0.19.0/pkg/internal/controller/controller.go:316\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).processNextWorkItem\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/int
ernal/controller/controller.go:263\nsigs.k8s.io/controller-runtime/pkg/internal/controller.(*Controller[...]).Start.func2.2\n\t/home/runner/go/pkg/mod/sigs.k8s.io/controller-runtime@v0.19.0/pkg/internal/controller/controller.go:224
"}
{"level":"info","ts":1766173367.5121572,"logger":"provider.aws","msg":"using aws session","region":"us-east-1","external id":"","credentials":{}}


==================== 34-1-eso-events-argocd ====================

+ kubectl get events -n argocd --sort-by=.lastTimestamp
LAST SEEN   TYPE      REASON               OBJECT                                                                               MESSAGE
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Successfully assigned argocd/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j to i-0de7b
4307ef02f19f

57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Successfully assigned argocd/cdr-external-secret-operator-external-secrets-cert-controllmwm9 to i-0de7b4307ef02f19f
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Successfully assigned argocd/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs to i-0de7b4307ef02f19f
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Successfully assigned argocd/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h to i-0de7b4307ef02f19f
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Healthy -> Missing
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Synced -> OutOfSync
57m         Normal    OperationStarted     application/cdr-external-secret-operator                                             Initiated automated sync to 'da5c7489781d26ebfccfdd760679c6f275bef484'
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets                             Scaled up replica set cdr-external-secret-operator-external-secrets-9c858c9c8 from 0 to 2
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets-webhook                     Scaled up replica set cdr-external-secret-operator-external-secrets-webhook-6b69586684 from 0 to 1
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-webhook-6b69586684          Created pod: cdr-external-secret-operator-external-secrets-webhook-6b696qk4j
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-9c858c9c8                   Created pod: cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-9c858c9c8                   Created pod: cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95   Created pod: cdr-external-secret-operator-external-secrets-cert-controllmwm9
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets-cert-controller             Scaled up replica set cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95 from 0 to 1
57m         Normal    OperationCompleted   application/cdr-external-secret-operator                                             Sync operation to da5c7489781d26ebfccfdd760679c6f275bef484 succeeded
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Started container webhook
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Started container cert-controller
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Started container external-secrets
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.438s (8.438s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Created container: webhook
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.435s (8.435s including waiting). Image size: 65934511 bytes.
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.404s (8.404s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Created container: external-secrets
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Started container external-secrets
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Created container: external-secrets
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.381s (8.381s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Created container: cert-controller
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: OutOfSync -> Synced
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Started container external-secrets
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Created container: external-secrets
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.381s (8.381s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Created container: cert-controller
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: OutOfSync -> Synced
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Missing -> Progressing
56m         Normal    OperationStarted     application/secret-store                                                             Initiated automated sync to 'da5c7489781d26ebfccfdd760679c6f275bef484'
56m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Progressing -> Healthy
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated health status: Healthy -> Progressing
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated sync status: OutOfSync -> Synced
56m         Normal    OperationCompleted   application/secret-store                                                             Sync operation to da5c7489781d26ebfccfdd760679c6f275bef484 succeeded
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated health status: Progressing -> Healthy
55m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Synced -> Unknown
54m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Unknown -> Synced
49m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to '70dfc2f1a709bb12f3e433305615efd8797a20bb'
49m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Unknown -> OutOfSync
49m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated health status: Healthy -> Progressing
49m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated health status: Progressing -> Degraded
44m         Warning   OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to 70dfc2f1a709bb12f3e433305615efd8797a20bb failed: one or more synchronization tasks completed unsuccessfully (retried 5 times).
42m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to '841f56749a9e03dc0be2fdae89b429b18920a781'
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
42m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to 841f56749a9e03dc0be2fdae89b429b18920a781 succeeded
41m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to 'a75c2320be733ae8be1b663016fc51b57ead9545'
41m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
36m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
36m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to a75c2320be733ae8be1b663016fc51b57ead9545 succeeded
23m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to 'db725726cc199cbb64d62bf668169a7d53be62a3'
23m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
23m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
23m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to db725726cc199cbb64d62bf668169a7d53be62a3 succeeded
16m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> Unknown


==================== 30-2-eso-all-in-ns-argocd ====================

+ kubectl get all -n argocd
NAME                                                                  READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0                                   1/1     Running   0          2d20h
pod/argocd-applicationset-controller-788d477d88-9bqcm                 1/1     Running   0          2d20h
pod/argocd-redis-77fb75bcd9-rngdx                                     1/1     Running   0          2d20h
pod/argocd-repo-server-67b754fcff-x5nrv                               1/1     Running   0          2d20h
pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h     1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs     1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9   1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j   1/1     Running   0          57m
pod/gitops-agent-5fc75b4fd9-h82tw                                     1/1     Running   0          2d20h
pod/gitops-agent-upgrader-29436000-7q8kw                              0/1     Error     0          3h38m
pod/gitops-agent-upgrader-29436000-dlc22                              0/1     Error     0          3h42m
pod/gitops-agent-upgrader-29436000-nbjbj                              0/1     Error     0          3h33m
pod/gitops-agent-upgrader-29436000-qv5ns                              0/1     Error     0          3h41m
pod/gitops-agent-upgrader-29436000-tk62s                              0/1     Error     0          3h43m
pod/gitops-agent-upgrader-29436000-vjdwk                              0/1     Error     0          3h44m
pod/gitops-agent-upgrader-29436000-x9zfk                              0/1     Error     0          3h44m

NAME                                                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/argocd-applicationset-controller                        ClusterIP   172.20.86.17     <none>        7000/TCP         23d
service/argocd-applicationset-controller-metrics                ClusterIP   172.20.153.140   <none>        8080/TCP         23d
service/argocd-redis                                            ClusterIP   172.20.26.33     <none>        6379/TCP         23d
service/argocd-repo-server                                      ClusterIP   172.20.33.21     <none>        8081/TCP         23d
service/argocd-repo-server-metrics                              ClusterIP   172.20.137.34    <none>        8084/TCP         23d
service/argocd-server                                           ClusterIP   172.20.33.91     <none>        80/TCP,443/TCP   23d
service/cdr-external-secret-operator-external-secrets-webhook   ClusterIP   172.20.238.226   <none>        443/TCP          57m
service/gitops-agent-metrics                                    ClusterIP   172.20.250.144   <none>        2112/TCP         23d

NAME                                                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-applicationset-controller                                1/1     1            1           23d
deployment.apps/argocd-redis                                                    1/1     1            1           23d
deployment.apps/argocd-repo-server                                              1/1     1            1           23d
deployment.apps/argocd-server                                                   0/0     0            0           23d
deployment.apps/cdr-external-secret-operator-external-secrets                   2/2     2            2           57m
deployment.apps/cdr-external-secret-operator-external-secrets-cert-controller   1/1     1            1           57m
deployment.apps/cdr-external-secret-operator-external-secrets-webhook           1/1     1            1           57m
deployment.apps/gitops-agent                                                    1/1     1            1           23d

NAME                                                                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-applicationset-controller-788d477d88                               1         1         1       23d
replicaset.apps/argocd-redis-77fb75bcd9                                                   1         1         1       23d
replicaset.apps/argocd-repo-server-67b754fcff                                             1         1         1       23d
:
replicaset.apps/argocd-applicationset-controller-788d477d88                               1         1         1       23d
replicaset.apps/argocd-redis-77fb75bcd9                                                   1         1         1       23d
replicaset.apps/argocd-repo-server-67b754fcff                                             1         1         1       23d
replicaset.apps/argocd-server-69dc89948c                                                  0         0         0       23d
replicaset.apps/cdr-external-secret-operator-external-secrets-9c858c9c8                   2         2         2       57m
replicaset.apps/cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95   1         1         1       57m
replicaset.apps/cdr-external-secret-operator-external-secrets-webhook-6b69586684          1         1         1       57m
replicaset.apps/gitops-agent-5fc75b4fd9                                                   1         1         1       23d

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     23d

NAME                                  SCHEDULE      TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/gitops-agent-upgrader   0 */4 * * *   <none>     False     0        3h44m           23d

NAME                                       STATUS   COMPLETIONS   DURATION   AGE
job.batch/gitops-agent-upgrader-29436000   Failed   0/1           3h44m      3h44m


==================== 31-2-eso-deploy-describe-argocd/cdr-external-secret-operator-external-secrets-cert-controller ====================

+ kubectl describe deploy -n argocd cdr-external-secret-operator-external-secrets-cert-controller
Name:                   cdr-external-secret-operator-external-secrets-cert-controller
Namespace:              argocd
CreationTimestamp:      Fri, 19 Dec 2025 18:47:01 +0000
Labels:                 app.kubernetes.io/instance=cdr-external-secret-operator
                        app.kubernetes.io/managed-by=Helm
                        app.kubernetes.io/name=external-secrets-cert-controller
                        app.kubernetes.io/version=v0.10.5
                        argocd.argoproj.io/instance=cdr-external-secret-operator
                        helm.sh/chart=external-secrets-0.10.5
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app.kubernetes.io/instance=cdr-external-secret-operator,app.kubernetes.io/name=external-secrets-cert-controller
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:           app.kubernetes.io/instance=cdr-external-secret-operator
                    app.kubernetes.io/managed-by=Helm
                    app.kubernetes.io/name=external-secrets-cert-controller
                    app.kubernetes.io/version=v0.10.5
                    helm.sh/chart=external-secrets-0.10.5

  Service Account:  external-secrets-cert-controller
  Containers:
   cert-controller:
    Image:           oci.external-secrets.io/external-secrets/external-secrets:v0.10.5
    Port:            8080/TCP (metrics)
    Host Port:       0/TCP (metrics)
    SeccompProfile:  RuntimeDefault
    Args:
      certcontroller
      --crd-requeue-interval=5m
      --service-name=cdr-external-secret-operator-external-secrets-webhook
      --service-namespace=argocd
      --secret-name=cdr-external-secret-operator-external-secrets-webhook
      --secret-namespace=argocd
      --metrics-addr=:8080
      --healthz-addr=:8081
      --loglevel=info
      --zap-time-encoding=epoch
      --enable-partial-cache=true
    Readiness:     http-get http://:8081/readyz delay=20s timeout=1s period=5s #success=1 #failure=3
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  57m   deployment-controller  Scaled up replica set cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95 from 0 to 1


==================== 32-2-eso-logs-tail-argocd/cdr-external-secret-operator-external-secrets-cert-controller ====================
{"level":"info","ts":1766173036.471456,"logger":"controllers.webhook-certs-updater","msg":"injecting ca certificate and service names","cacrt":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURSakNDQWk2Z0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREEyTVJrd0Z3WURWUVFLRXhCbGVIUmwKY201aGJDMXpaV055WlhSek1Sa3dGd1lEVlFRREV4QmxlSFJsY201aGJDMXpaV055WlhSek1CNFhEVEkxTVRJeApPVEUzTkRjeE5Wb1hEVE0xTVRJeE56RTRORGN4TlZvd05qRVpNQmNHQTFVRUNoTVFaWGgwWlhKdVlXd3RjMlZqCmNtVjBjekVaTUJjR0ExVUVBeE1RWlhoMFpYSnVZV3d0YzJWamNtVjBjekNDQVNJd0RRWUpLb1pJaHZjTkFRRUIKQlFBRGdnRVBBRENDQVFvQ2dnRUJBTTNLMlNQUjB5bEl6NE1LbXM4ZUV5Z20wWVM4V04zY2U1VDdBYTRkMUU5aQo1T1dxSytnYmpNQ00wclpZSGNwYnBRU2JkbVdxUXlhM1ZKZ2htNGE4Q045ZGtNc1V5Tm1xMnpPcldZOVBaaDBwCnhtUytLMjRJS1pIaTdKN1BZM09lWlQ1UlFaNUF3ZC8wb2tqSHhLb3o4YXliR1NMc255Sm9CUXlsU1Q0dGJRcGwKUGRWdUhZYWd6SktCQUl6QVI3V1JOdTJ3alFXQ2lWSVlOdXF0L2ZlcXU4Y0VMZHBvUW1iUjFiM0xuK0lna2NaZgpKTTJnbXVPakp4NStQaVkvOFl3Y2l1cHBlMndCbW54T05aY2FHQVBxVHlHNHhaYytFU0srMnFldHQ2TlNhVmx0Ci9VbHhkUzJ4TjZuYVRCdXBPdXhTRVlROGp4SzBjTlBhQkx0Rnk5Y291ZUVDQXdFQUFhTmZNRjB3RGdZRFZSMFAKQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCL3dRRk1BTUJBZjh3SFFZRFZSME9CQllFRkRVa21ic3QyMzVydVc2bQorRlduL09KeDk3a3VNQnNHQTFVZEVRUVVNQktDRUdWNGRHVnlibUZzTFhObFkzSmxkSE13RFFZSktvWklodmNOCkFRRUxCUUFEZ2dFQkFMVVdSRjdaZCt2emdFUHZ5dlFSb04zVzRYZFN0dll1dnZSWm9EakltaWNnZHJ2NFp0VTAKMlgyYUk5YVo1WUZ1WjBBVmY1QjdyTjd3a1NVSjg2UzdvMThyb1M3VkxHUXM1ZmZWY3hhcy9SV09INXQ1WmRFTQplWk1TY1lXZGM1REd5T2xPTjRBTjVLNGhKRmJUTExSNXBxamh4Nk51a2hTOXVBTVdQdEF5eEJVM0d5MGhqd1N1Cm9SZVU4a3A2Z0xrZm9oazJDM2pxN2kyL2wvdVB4VmhGM1JSNlFXb25INVFUOE9nRThjL2tyd1BIU1JWUXVCNjAKYmdBOEtEV1ROVnBCVHUyalpiZ2cyYjBhR0d3VDY2Yk1BQmFFZkt4NnJwa2RDTkI0d2F0TnRiV3daYmprMlNmMAp5bzdGTXJsNG5xWndyVmw5R0h4ZENpRk5nZWl6MnljemJHMD0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=","name":"secretstore-validate"}
{"level":"info","ts":1766173036.4789286,"logger":"controllers.webhook-certs-updater","msg":"updated webhook config","Webhookconfig":{"name":"secretstore-validate"}}
W1219 19:40:15.135090       1 warnings.go:70] v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
{"level":"info","ts":1766173336.4622905,"logger":"controllers.webhook-certs-updater","msg":"updating webhook config","Webhookconfig":{"name":"externalsecret-validate"}}
{"level":"info","ts":1766173336.4683456,"logger":"controllers.webhook-certs-updater","msg":"injecting ca certificate and service names","cacrt":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURSakNDQWk2Z0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREEyTVJrd0Z3WURWUVFLRXhCbGVIUmwKY201aGJDMXpaV055WlhSek1Sa3dGd1lEVlFRREV4QmxlSFJsY201aGJDMXpaV055WlhSek1CNFhEVEkxTVRJeApPVEUzTkRjeE5Wb1hEVE0xTVRJeE56RTRORGN4TlZvd05qRVpNQmNHQTFVRUNoTVFaWGgwWlhKdVlXd3RjMlZqCmNtVjBjekVaTUJjR0ExVUVBeE1RWlhoMFpYSnVZV3d0YzJWamNtVjBjekNDQVNJd0RRWUpLb1pJaHZjTkFRRUIKQlFBRGdnRVBBRENDQVFvQ2dnRUJBTTNLMlNQUjB5bEl6NE1LbXM4ZUV5Z20wWVM4V04zY2U1VDdBYTRkMUU5aQo1T1dxSytnYmpNQ00wclpZSGNwYnBRU2JkbVdxUXlhM1ZKZ2htNGE4Q045ZGtNc1V5Tm1xMnpPcldZOVBaaDBwCnhtUytLMjRJS1pIaTdKN1BZM09lWlQ1UlFaNUF3ZC8wb2tqSHhLb3o4YXliR1NMc255Sm9CUXlsU1Q0dGJRcGwKUGRWdUhZYWd6SktCQUl6QVI3V1JOdTJ3alFXQ2lWSVlOdXF0L2ZlcXU4Y0VMZHBvUW1iUjFiM0xuK0lna2NaZgpKTTJnbXVPakp4NStQaVkvOFl3Y2l1cHBlMndCbW54T05aY2FHQVBxVHlHNHhaYytFU0srMnFldHQ2TlNhVmx0Ci9VbHhkUzJ4TjZuYVRCdXBPdXhTRVlROGp4SzBjTlBhQkx0Rnk5Y291ZUVDQXdFQUFhTmZNRjB3RGdZRFZSMFAKQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCL3dRRk1BTUJBZjh3SFFZRFZSME9CQllFRkRVa21ic3QyMzVydVc2bQorRlduL09KeDk3a3VNQnNHQTFVZEVRUVVNQktDRUdWNGRHVnlibUZzTFhObFkzSmxkSE13RFFZSktvWklodmNOCkFRRUxCUUFEZ2dFQkFMVVdSRjdaZCt2emdFUHZ5dlFSb04zVzRYZFN0dll1dnZSWm9EakltaWNnZHJ2NFp0VTAKMlgyYUk5YVo1WUZ1WjBBVmY1QjdyTjd3a1NVSjg2UzdvMThyb1M3VkxHUXM1ZmZWY3hhcy9SV09INXQ1WmRFTQplWk1TY1lXZGM1REd5T2xPTjRBTjVLNGhKRmJUTExSNXBxamh4Nk51a2hTOXVBTVdQdEF5eEJVM0d5MGhqd1N1Cm9SZVU4a3A2Z0xrZm9oazJDM2pxN2kyL2wvdVB4VmhGM1JSNlFXb25INVFUOE9nRThjL2tyd1BIU1JWUXVCNjAKYmdBOEtEV1ROVnBCVHUyalpiZ2cyYjBhR0d3VDY2Yk1BQmFFZkt4NnJwa2RDTkI0d2F0TnRiV3daYmprMlNmMAp5bzdGTXJsNG5xWndyVmw5R0h4ZENpRk5nZWl6MnljemJHMD0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=","name":"externalsecret-validate"}
{"level":"info","ts":1766173336.4750674,"logger":"controllers.webhook-certs-updater","msg":"updated webhook config","Webhookconfig":{"name":"externalsecret-validate"}}
{"level":"info","ts":1766173336.4794738,"logger":"controllers.webhook-certs-updater","msg":"updating webhook config","Webhookconfig":{"name":"secretstore-validate"}}
{"level":"info","ts":1766173336.4860222,"logger":"controllers.webhook-certs-updater","msg":"injecting ca certificate and service names","cacrt":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURSakNDQWk2Z0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREEyTVJrd0Z3WURWUVFLRXhCbGVIUmwKY201aGJDMXpaV055WlhSek1Sa3dGd1lEVlFRREV4QmxlSFJsY201aGJDMXpaV055WlhSek1CNFhEVEkxTVRJeApPVEUzTkRjeE5Wb1hEVE0xTVRJeE56RTRORGN4TlZvd05qRVpNQmNHQTFVRUNoTVFaWGgwWlhKdVlXd3RjMlZqCmNtVjBjekVaTUJjR0ExVUVBeE1RWlhoMFpYSnVZV3d0YzJWamNtVjBjekNDQVNJd0RRWUpLb1pJaHZjTkFRRUIKQlFBRGdnRVBBRENDQVFvQ2dnRUJBTTNLMlNQUjB5bEl6NE1LbXM4ZUV5Z20wWVM4V04zY2U1VDdBYTRkMUU5aQo1T1dxSytnYmpNQ00wclpZSGNwYnBRU2JkbVdxUXlhM1ZKZ2htNGE4Q045ZGtNc1V5Tm1xMnpPcldZOVBaaDBwCnhtUytLMjRJS1pIaTdKN1BZM09lWlQ1UlFaNUF3ZC8wb2tqSHhLb3o4YXliR1NMc255Sm9CUXlsU1Q0dGJRcGwKUGRWdUhZYWd6SktCQUl6QVI3V1JOdTJ3alFXQ2lWSVlOdXF0L2ZlcXU4Y0VMZHBvUW1iUjFiM0xuK0lna2NaZgpKTTJnbXVPakp4NStQaVkvOFl3Y2l1cHBlMndCbW54T05aY2FHQVBxVHlHNHhaYytFU0srMnFldHQ2TlNhVmx0Ci9VbHhkUzJ4TjZuYVRCdXBPdXhTRVlROGp4SzBjTlBhQkx0Rnk5Y291ZUVDQXdFQUFhTmZNRjB3RGdZRFZSMFAKQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCL3dRRk1BTUJBZjh3SFFZRFZSME9CQllFRkRVa21ic3QyMzVydVc2bQorRlduL09KeDk3a3VNQnNHQTFVZEVRUVVNQktDRUdWNGRHVnlibUZzTFhObFkzSmxkSE13RFFZSktvWklodmNOCkFRRUxCUUFEZ2dFQkFMVVdSRjdaZCt2emdFUHZ5dlFSb04zVzRYZFN0dll1dnZSWm9EakltaWNnZHJ2NFp0VTAKMlgyYUk5YVo1WUZ1WjBBVmY1QjdyTjd3a1NVSjg2UzdvMThyb1M3VkxHUXM1ZmZWY3hhcy9SV09INXQ1WmRFTQplWk1TY1lXZGM1REd5T2xPTjRBTjVLNGhKRmJUTExSNXBxamh4Nk51a2hTOXVBTVdQdEF5eEJVM0d5MGhqd1N1Cm9SZVU4a3A2Z0xrZm9oazJDM2pxN2kyL2wvdVB4VmhGM1JSNlFXb25INVFUOE9nRThjL2tyd1BIU1JWUXVCNjAKYmdBOEtEV1ROVnBCVHUyalpiZ2cyYjBhR0d3VDY2Yk1BQmFFZkt4NnJwa2RDTkI0d2F0TnRiV3daYmprMlNmMAp5bzdGTXJsNG5xWndyVmw5R0h4ZENpRk5nZWl6MnljemJHMD0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=","name":"secretstore-validate"}
{"level":"info","ts":1766173336.4939144,"logger":"controllers.webhook-certs-updater","msg":"updated webhook config","Webhookconfig":{"name":"secretstore-validate"}}


==================== 33-2-eso-logs-since-60m-argocd/cdr-external-secret-operator-external-secrets-cert-controller ====================

+ kubectl logs -n argocd deploy/cdr-external-secret-operator-external-secrets-cert-controller --since=60m
{"level":"info","ts":1766170035.788288,"logger":"setup","msg":"starting manager"}
{"level":"info","ts":1766170035.7885766,"logger":"controller-runtime.metrics","msg":"Starting metrics server"}
{"level":"info","ts":1766170035.7886174,"msg":"starting server","name":"health probe","addr":"[::]:8081"}
{"level":"info","ts":1766170035.7887526,"logger":"controller-runtime.metrics","msg":"Serving metrics server","bindAddress":":8080","secure":false}
{"level":"info","ts":1766170035.7888541,"msg":"Starting EventSource","controller":"customresourcedefinition","controllerGroup":"apiextensions.k8s.io","controllerKind":"CustomResourceDefinition","source":"kind source: *v1.CustomResourceDefinition"}
{"level":"info","ts":1766170035.7888901,"msg":"Starting Controller","controller":"customresourcedefinition","controllerGroup":"apiextensions.k8s.io","controllerKind":"CustomResourceDefinition"}
{"level":"info","ts":1766170035.788919,"msg":"Starting EventSource","controller":"validatingwebhookconfiguration","controllerGroup":"admissionregistration.k8s.io","controllerKind":"ValidatingWebhookConfiguration","source":"kind sou:
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Successfully assigned argocd/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j to i-0de7bNHQTFVZEVRUVVNQktDRUdWNGRHVnlibUZzTFhObFkzSmxkSE13RFFZSktvWklodmNOCkFRRUxCUUFEZ2dFQkFMVVdSRjdaZCt2emdFUHZ5dlFSb04zVzRYZFN0dll1dnZSWm9EakltaWNnZHJ2NFp0VTAKMlgyYUk5YVo1WUZ1WjBBVmY1QjdyTjd3a1NVSjg2UzdvMThyb1M3VkxHUXM1ZmZWY3hhcy9SV09IN
XQ1WmRFTQplWk1TY1lXZGM1REd5T2xPTjRBTjVLNGhKRmJUTExSNXBxamh4Nk51a2hTOXVBTVdQdEF5eEJVM0d5MGhqd1N1Cm9SZVU4a3A2Z0xrZm9oazJDM2pxN2kyL2wvdVB4VmhGM1JSNlFXb25INVFUOE9nRThjL2tyd1BIU1JWUXVCNjAKYmdBOEtEV1ROVnBCVHUyalpiZ2cyYjBhR0d3VDY2Yk1BQmFF
Zkt4NnJwa2RDTkI0d2F0TnRiV3daYmprMlNmMAp5bzdGTXJsNG5xWndyVmw5R0h4ZENpRk5nZWl6MnljemJHMD0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=","name":"externalsecret-validate"}
{"level":"info","ts":1766173036.4616838,"logger":"controllers.webhook-certs-updater","msg":"updated webhook config","Webhookconfig":{"name":"externalsecret-validate"}}
{"level":"info","ts":1766173036.4660382,"logger":"controllers.webhook-certs-updater","msg":"updating webhook config","Webhookconfig":{"name":"secretstore-validate"}}
{"level":"info","ts":1766173036.471456,"logger":"controllers.webhook-certs-updater","msg":"injecting ca certificate and service names","cacrt":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURSakNDQWk2Z0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkF
Rc0ZBREEyTVJrd0Z3WURWUVFLRXhCbGVIUmwKY201aGJDMXpaV055WlhSek1Sa3dGd1lEVlFRREV4QmxlSFJsY201aGJDMXpaV055WlhSek1CNFhEVEkxTVRJeApPVEUzTkRjeE5Wb1hEVE0xTVRJeE56RTRORGN4TlZvd05qRVpNQmNHQTFVRUNoTVFaWGgwWlhKdVlXd3RjMlZqCmNtVjBjekVaTUJjR0ExVU
VBeE1RWlhoMFpYSnVZV3d0YzJWamNtVjBjekNDQVNJd0RRWUpLb1pJaHZjTkFRRUIKQlFBRGdnRVBBRENDQVFvQ2dnRUJBTTNLMlNQUjB5bEl6NE1LbXM4ZUV5Z20wWVM4V04zY2U1VDdBYTRkMUU5aQo1T1dxSytnYmpNQ00wclpZSGNwYnBRU2JkbVdxUXlhM1ZKZ2htNGE4Q045ZGtNc1V5Tm1xMnpPcldZO
VBaaDBwCnhtUytLMjRJS1pIaTdKN1BZM09lWlQ1UlFaNUF3ZC8wb2tqSHhLb3o4YXliR1NMc255Sm9CUXlsU1Q0dGJRcGwKUGRWdUhZYWd6SktCQUl6QVI3V1JOdTJ3alFXQ2lWSVlOdXF0L2ZlcXU4Y0VMZHBvUW1iUjFiM0xuK0lna2NaZgpKTTJnbXVPakp4NStQaVkvOFl3Y2l1cHBlMndCbW54T05aY2FH
QVBxVHlHNHhaYytFU0srMnFldHQ2TlNhVmx0Ci9VbHhkUzJ4TjZuYVRCdXBPdXhTRVlROGp4SzBjTlBhQkx0Rnk5Y291ZUVDQXdFQUFhTmZNRjB3RGdZRFZSMFAKQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCL3dRRk1BTUJBZjh3SFFZRFZSME9CQllFRkRVa21ic3QyMzVydVc2bQorRlduL09KeDk3a3VNQnN
HQTFVZEVRUVVNQktDRUdWNGRHVnlibUZzTFhObFkzSmxkSE13RFFZSktvWklodmNOCkFRRUxCUUFEZ2dFQkFMVVdSRjdaZCt2emdFUHZ5dlFSb04zVzRYZFN0dll1dnZSWm9EakltaWNnZHJ2NFp0VTAKMlgyYUk5YVo1WUZ1WjBBVmY1QjdyTjd3a1NVSjg2UzdvMThyb1M3VkxHUXM1ZmZWY3hhcy9SV09INX
Q1WmRFTQplWk1TY1lXZGM1REd5T2xPTjRBTjVLNGhKRmJUTExSNXBxamh4Nk51a2hTOXVBTVdQdEF5eEJVM0d5MGhqd1N1Cm9SZVU4a3A2Z0xrZm9oazJDM2pxN2kyL2wvdVB4VmhGM1JSNlFXb25INVFUOE9nRThjL2tyd1BIU1JWUXVCNjAKYmdBOEtEV1ROVnBCVHUyalpiZ2cyYjBhR0d3VDY2Yk1BQmFFZ
kt4NnJwa2RDTkI0d2F0TnRiV3daYmprMlNmMAp5bzdGTXJsNG5xWndyVmw5R0h4ZENpRk5nZWl6MnljemJHMD0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=","name":"secretstore-validate"}
{"level":"info","ts":1766173036.4789286,"logger":"controllers.webhook-certs-updater","msg":"updated webhook config","Webhookconfig":{"name":"secretstore-validate"}}
W1219 19:40:15.135090       1 warnings.go:70] v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
{"level":"info","ts":1766173336.4622905,"logger":"controllers.webhook-certs-updater","msg":"updating webhook config","Webhookconfig":{"name":"externalsecret-validate"}}
{"level":"info","ts":1766173336.4683456,"logger":"controllers.webhook-certs-updater","msg":"injecting ca certificate and service names","cacrt":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURSakNDQWk2Z0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQk
FRc0ZBREEyTVJrd0Z3WURWUVFLRXhCbGVIUmwKY201aGJDMXpaV055WlhSek1Sa3dGd1lEVlFRREV4QmxlSFJsY201aGJDMXpaV055WlhSek1CNFhEVEkxTVRJeApPVEUzTkRjeE5Wb1hEVE0xTVRJeE56RTRORGN4TlZvd05qRVpNQmNHQTFVRUNoTVFaWGgwWlhKdVlXd3RjMlZqCmNtVjBjekVaTUJjR0ExV
UVBeE1RWlhoMFpYSnVZV3d0YzJWamNtVjBjekNDQVNJd0RRWUpLb1pJaHZjTkFRRUIKQlFBRGdnRVBBRENDQVFvQ2dnRUJBTTNLMlNQUjB5bEl6NE1LbXM4ZUV5Z20wWVM4V04zY2U1VDdBYTRkMUU5aQo1T1dxSytnYmpNQ00wclpZSGNwYnBRU2JkbVdxUXlhM1ZKZ2htNGE4Q045ZGtNc1V5Tm1xMnpPcldZ
OVBaaDBwCnhtUytLMjRJS1pIaTdKN1BZM09lWlQ1UlFaNUF3ZC8wb2tqSHhLb3o4YXliR1NMc255Sm9CUXlsU1Q0dGJRcGwKUGRWdUhZYWd6SktCQUl6QVI3V1JOdTJ3alFXQ2lWSVlOdXF0L2ZlcXU4Y0VMZHBvUW1iUjFiM0xuK0lna2NaZgpKTTJnbXVPakp4NStQaVkvOFl3Y2l1cHBlMndCbW54T05aY2F
HQVBxVHlHNHhaYytFU0srMnFldHQ2TlNhVmx0Ci9VbHhkUzJ4TjZuYVRCdXBPdXhTRVlROGp4SzBjTlBhQkx0Rnk5Y291ZUVDQXdFQUFhTmZNRjB3RGdZRFZSMFAKQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCL3dRRk1BTUJBZjh3SFFZRFZSME9CQllFRkRVa21ic3QyMzVydVc2bQorRlduL09KeDk3a3VNQn
NHQTFVZEVRUVVNQktDRUdWNGRHVnlibUZzTFhObFkzSmxkSE13RFFZSktvWklodmNOCkFRRUxCUUFEZ2dFQkFMVVdSRjdaZCt2emdFUHZ5dlFSb04zVzRYZFN0dll1dnZSWm9EakltaWNnZHJ2NFp0VTAKMlgyYUk5YVo1WUZ1WjBBVmY1QjdyTjd3a1NVSjg2UzdvMThyb1M3VkxHUXM1ZmZWY3hhcy9SV09IN
XQ1WmRFTQplWk1TY1lXZGM1REd5T2xPTjRBTjVLNGhKRmJUTExSNXBxamh4Nk51a2hTOXVBTVdQdEF5eEJVM0d5MGhqd1N1Cm9SZVU4a3A2Z0xrZm9oazJDM2pxN2kyL2wvdVB4VmhGM1JSNlFXb25INVFUOE9nRThjL2tyd1BIU1JWUXVCNjAKYmdBOEtEV1ROVnBCVHUyalpiZ2cyYjBhR0d3VDY2Yk1BQmFF
Zkt4NnJwa2RDTkI0d2F0TnRiV3daYmprMlNmMAp5bzdGTXJsNG5xWndyVmw5R0h4ZENpRk5nZWl6MnljemJHMD0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=","name":"externalsecret-validate"}
{"level":"info","ts":1766173336.4750674,"logger":"controllers.webhook-certs-updater","msg":"updated webhook config","Webhookconfig":{"name":"externalsecret-validate"}}
{"level":"info","ts":1766173336.4794738,"logger":"controllers.webhook-certs-updater","msg":"updating webhook config","Webhookconfig":{"name":"secretstore-validate"}}
{"level":"info","ts":1766173336.4860222,"logger":"controllers.webhook-certs-updater","msg":"injecting ca certificate and service names","cacrt":"LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSURSakNDQWk2Z0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQk
FRc0ZBREEyTVJrd0Z3WURWUVFLRXhCbGVIUmwKY201aGJDMXpaV055WlhSek1Sa3dGd1lEVlFRREV4QmxlSFJsY201aGJDMXpaV055WlhSek1CNFhEVEkxTVRJeApPVEUzTkRjeE5Wb1hEVE0xTVRJeE56RTRORGN4TlZvd05qRVpNQmNHQTFVRUNoTVFaWGgwWlhKdVlXd3RjMlZqCmNtVjBjekVaTUJjR0ExV
UVBeE1RWlhoMFpYSnVZV3d0YzJWamNtVjBjekNDQVNJd0RRWUpLb1pJaHZjTkFRRUIKQlFBRGdnRVBBRENDQVFvQ2dnRUJBTTNLMlNQUjB5bEl6NE1LbXM4ZUV5Z20wWVM4V04zY2U1VDdBYTRkMUU5aQo1T1dxSytnYmpNQ00wclpZSGNwYnBRU2JkbVdxUXlhM1ZKZ2htNGE4Q045ZGtNc1V5Tm1xMnpPcldZ
OVBaaDBwCnhtUytLMjRJS1pIaTdKN1BZM09lWlQ1UlFaNUF3ZC8wb2tqSHhLb3o4YXliR1NMc255Sm9CUXlsU1Q0dGJRcGwKUGRWdUhZYWd6SktCQUl6QVI3V1JOdTJ3alFXQ2lWSVlOdXF0L2ZlcXU4Y0VMZHBvUW1iUjFiM0xuK0lna2NaZgpKTTJnbXVPakp4NStQaVkvOFl3Y2l1cHBlMndCbW54T05aY2F
HQVBxVHlHNHhaYytFU0srMnFldHQ2TlNhVmx0Ci9VbHhkUzJ4TjZuYVRCdXBPdXhTRVlROGp4SzBjTlBhQkx0Rnk5Y291ZUVDQXdFQUFhTmZNRjB3RGdZRFZSMFAKQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCL3dRRk1BTUJBZjh3SFFZRFZSME9CQllFRkRVa21ic3QyMzVydVc2bQorRlduL09KeDk3a3VNQn
NHQTFVZEVRUVVNQktDRUdWNGRHVnlibUZzTFhObFkzSmxkSE13RFFZSktvWklodmNOCkFRRUxCUUFEZ2dFQkFMVVdSRjdaZCt2emdFUHZ5dlFSb04zVzRYZFN0dll1dnZSWm9EakltaWNnZHJ2NFp0VTAKMlgyYUk5YVo1WUZ1WjBBVmY1QjdyTjd3a1NVSjg2UzdvMThyb1M3VkxHUXM1ZmZWY3hhcy9SV09IN
XQ1WmRFTQplWk1TY1lXZGM1REd5T2xPTjRBTjVLNGhKRmJUTExSNXBxamh4Nk51a2hTOXVBTVdQdEF5eEJVM0d5MGhqd1N1Cm9SZVU4a3A2Z0xrZm9oazJDM2pxN2kyL2wvdVB4VmhGM1JSNlFXb25INVFUOE9nRThjL2tyd1BIU1JWUXVCNjAKYmdBOEtEV1ROVnBCVHUyalpiZ2cyYjBhR0d3VDY2Yk1BQmFF
Zkt4NnJwa2RDTkI0d2F0TnRiV3daYmprMlNmMAp5bzdGTXJsNG5xWndyVmw5R0h4ZENpRk5nZWl6MnljemJHMD0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=","name":"secretstore-validate"}
{"level":"info","ts":1766173336.4939144,"logger":"controllers.webhook-certs-updater","msg":"updated webhook config","Webhookconfig":{"name":"secretstore-validate"}}


==================== 34-2-eso-events-argocd ====================

+ kubectl get events -n argocd --sort-by=.lastTimestamp
LAST SEEN   TYPE      REASON               OBJECT                                                                               MESSAGE
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Successfully assigned argocd/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j to i-0de7b
4307ef02f19f
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Successfully assigned argocd/cdr-external-secret-operator-external-secrets-cert-controllmwm9 to i-0de7b4307ef02f19f
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Successfully assigned argocd/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs to i-0de7b4307ef02f19f
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Successfully assigned argocd/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h to i-0de7b4307ef02f19f
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Healthy -> Missing
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Synced -> OutOfSync
57m         Normal    OperationStarted     application/cdr-external-secret-operator                                             Initiated automated sync to 'da5c7489781d26ebfccfdd760679c6f275bef484'
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets                             Scaled up replica set cdr-external-secret-operator-external-secrets-9c858c9c8 from 0 to 2
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets-webhook                     Scaled up replica set cdr-external-secret-operator-external-secrets-webhook-6b69586684 from 0 to 1
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-webhook-6b69586684          Created pod: cdr-external-secret-operator-external-secrets-webhook-6b696qk4j
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-9c858c9c8                   Created pod: cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-9c858c9c8                   Created pod: cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95   Created pod: cdr-external-secret-operator-external-secrets-cert-controllmwm9
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets-cert-controller             Scaled up replica set cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95 from 0 to 1
57m         Normal    OperationCompleted   application/cdr-external-secret-operator                                             Sync operation to da5c7489781d26ebfccfdd760679c6f275bef484 succeeded
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Started container webhook
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Started container cert-controller
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Started container external-secrets
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.438s (8.438s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Created container: webhook
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.435s (8.435s including waiting). Image size: 65934511 bytes.
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.404s (8.404s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Created container: external-secrets
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Started container external-secrets
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Created container: external-secrets
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.381s:
  57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.381s (8.381s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Created container: cert-controller
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: OutOfSync -> Synced
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Missing -> Progressing
56m         Normal    OperationStarted     application/secret-store                                                             Initiated automated sync to 'da5c7489781d26ebfccfdd760679c6f275bef484'
56m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Progressing -> Healthy
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated health status: Healthy -> Progressing
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated sync status: OutOfSync -> Synced
56m         Normal    OperationCompleted   application/secret-store                                                             Sync operation to da5c7489781d26ebfccfdd760679c6f275bef484 succeeded
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated health status: Progressing -> Healthy
55m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Synced -> Unknown
54m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Unknown -> Synced
50m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to '70dfc2f1a709bb12f3e433305615efd8797a20bb'
50m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Unknown -> OutOfSync
50m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated health status: Healthy -> Progressing
50m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated health status: Progressing -> Degraded
44m         Warning   OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to 70dfc2f1a709bb12f3e433305615efd8797a20bb failed: one or more synchronization tasks completed unsuccessfully (retried 5 times).
42m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to '841f56749a9e03dc0be2fdae89b429b18920a781'
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
42m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to 841f56749a9e03dc0be2fdae89b429b18920a781 succeeded
41m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to 'a75c2320be733ae8be1b663016fc51b57ead9545'
41m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
36m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
36m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to a75c2320be733ae8be1b663016fc51b57ead9545 succeeded
23m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to 'db725726cc199cbb64d62bf668169a7d53be62a3'
23m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
23m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
23m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to db725726cc199cbb64d62bf668169a7d53be62a3 succeeded
16m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> Unknown


==================== 30-3-eso-all-in-ns-argocd ====================

+ kubectl get all -n argocd
NAME                                                                  READY   STATUS    RESTARTS   AGE
pod/argocd-application-controller-0                                   1/1     Running   0          2d20h
pod/argocd-applicationset-controller-788d477d88-9bqcm                 1/1     Running   0          2d20h
pod/argocd-redis-77fb75bcd9-rngdx                                     1/1     Running   0          2d20h
pod/argocd-repo-server-67b754fcff-x5nrv                               1/1     Running   0          2d20h
pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h     1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs     1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9   1/1     Running   0          57m
pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j   1/1     Running   0          57m
pod/gitops-agent-5fc75b4fd9-h82tw                                     1/1     Running   0          2d20h
pod/gitops-agent-upgrader-29436000-7q8kw                              0/1     Error     0          3h38m
pod/gitops-agent-upgrader-29436000-dlc22                              0/1     Error     0          3h43m
pod/gitops-agent-upgrader-29436000-nbjbj                              0/1     Error     0          3h33m
pod/gitops-agent-upgrader-29436000-qv5ns                              0/1     Error     0          3h41m
pod/gitops-agent-upgrader-29436000-tk62s                              0/1     Error     0          3h43m
pod/gitops-agent-upgrader-29436000-vjdwk                              0/1     Error     0          3h44m
pod/gitops-agent-upgrader-29436000-x9zfk                              0/1     Error     0          3h44m

NAME                                                            TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
service/argocd-applicationset-controller                        ClusterIP   172.20.86.17     <none>        7000/TCP         23d
service/argocd-applicationset-controller-metrics                ClusterIP   172.20.153.140   <none>        8080/TCP         23d
service/argocd-redis                                            ClusterIP   172.20.26.33     <none>        6379/TCP         23d
service/argocd-repo-server                                      ClusterIP   172.20.33.21     <none>        8081/TCP         23d
service/argocd-repo-server-metrics                              ClusterIP   172.20.137.34    <none>        8084/TCP         23d
service/argocd-server                                           ClusterIP   172.20.33.91     <none>        80/TCP,443/TCP   23d
service/cdr-external-secret-operator-external-secrets-webhook   ClusterIP   172.20.238.226   <none>        443/TCP          57m
service/gitops-agent-metrics                                    ClusterIP   172.20.250.144   <none>        2112/TCP         23d

NAME                                                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/argocd-applicationset-controller                                1/1     1            1           23d
deployment.apps/argocd-redis                                                    1/1     1            1           23d
deployment.apps/argocd-repo-server                                              1/1     1            1           23d
deployment.apps/argocd-server                                                   0/0     0            0           23d
deployment.apps/cdr-external-secret-operator-external-secrets                   2/2     2            2           57m
deployment.apps/cdr-external-secret-operator-external-secrets-cert-controller   1/1     1            1           57m
deployment.apps/cdr-external-secret-operator-external-secrets-webhook           1/1     1            1           57m
deployment.apps/gitops-agent                                                    1/1     1            1           23d
NAME                                                                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/argocd-applicationset-controller-788d477d88                               1         1         1       23d
replicaset.apps/argocd-redis-77fb75bcd9                                                   1         1         1       23d
replicaset.apps/argocd-repo-server-67b754fcff                                             1         1         1       23d
replicaset.apps/argocd-server-69dc89948c                                                  0         0         0       23d
replicaset.apps/cdr-external-secret-operator-external-secrets-9c858c9c8                   2         2         2       57m
replicaset.apps/cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95   1         1         1       57m
replicaset.apps/cdr-external-secret-operator-external-secrets-webhook-6b69586684          1         1         1       57m
replicaset.apps/gitops-agent-5fc75b4fd9                                                   1         1         1       23d

NAME                                             READY   AGE
statefulset.apps/argocd-application-controller   1/1     23d

NAME                                  SCHEDULE      TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob.batch/gitops-agent-upgrader   0 */4 * * *   <none>     False     0        3h44m           23d

NAME                                       STATUS   COMPLETIONS   DURATION   AGE
job.batch/gitops-agent-upgrader-29436000   Failed   0/1           3h44m      3h44m


==================== 31-3-eso-deploy-describe-argocd/cdr-external-secret-operator-external-secrets-webhook ====================

+ kubectl describe deploy -n argocd cdr-external-secret-operator-external-secrets-webhook
Name:                   cdr-external-secret-operator-external-secrets-webhook
Namespace:              argocd
CreationTimestamp:      Fri, 19 Dec 2025 18:47:01 +0000
Labels:                 app.kubernetes.io/instance=cdr-external-secret-operator
                        app.kubernetes.io/managed-by=Helm
                        app.kubernetes.io/name=external-secrets-webhook
                        app.kubernetes.io/version=v0.10.5
                        argocd.argoproj.io/instance=cdr-external-secret-operator
                        helm.sh/chart=external-secrets-0.10.5
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app.kubernetes.io/instance=cdr-external-secret-operator,app.kubernetes.io/name=external-secrets-webhook
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:           app.kubernetes.io/instance=cdr-external-secret-operator
                    app.kubernetes.io/managed-by=Helm
                    helm.sh/chart=external-secrets-0.10.5
  Service Account:  external-secrets-webhook
  Containers:
   webhook:
    Image:           oci.external-secrets.io/external-secrets/external-secrets:v0.10.5
    Ports:           8080/TCP (metrics), 10250/TCP (webhook)
    Host Ports:      0/TCP (metrics), 0/TCP (webhook)
    SeccompProfile:  RuntimeDefault
    Args:
      webhook
      --port=10250
      --dns-name=cdr-external-secret-operator-external-secrets-webhook.argocd.svc
      --cert-dir=/tmp/certs
      --check-interval=5m
      --metrics-addr=:8080
      --healthz-addr=:8081
      --loglevel=info
      --zap-time-encoding=epoch
    Readiness:    http-get http://:8081/readyz delay=20s timeout=1s period=5s #success=1 #failure=3
    Environment:  <none>
    Mounts:
      /tmp/certs from certs (ro)
  Volumes:
   certs:
    Type:          Secret (a volume populated by a Secret)
    SecretName:    cdr-external-secret-operator-external-secrets-webhook
    Optional:      false
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   cdr-external-secret-operator-external-secrets-webhook-6b69586684 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  57m   deployment-controller  Scaled up replica set cdr-external-secret-operator-external-secrets-webhook-6b69586684 from 0 to 1


==================== 32-3-eso-logs-tail-argocd/cdr-external-secret-operator-external-secrets-webhook ====================

+ kubectl logs -n argocd deploy/cdr-external-secret-operator-external-secrets-webhook --tail=400
{"level":"info","ts":1766170035.7355864,"logger":"setup","msg":"validating certs"}
{"level":"error","ts":1766170035.735631,"logger":"setup","msg":"invalid certs. retrying...","error":"stat /tmp/certs/tls.crt: no such file or directory","stacktrace":"github.com/external-secrets/external-secrets/cmd.waitForCerts\n\t/home/runner/work/external-secrets/external-secrets/cmd/webhook.go:215\ngithub.com/external-secrets/external-secrets/cmd.init.func3\n\t/home/runner/work/external-secrets/external-secrets/cmd/webhook.go:84\ngithub.com/spf13/cobra.(*Command).execute\n\t/home/runner/go/pkg/mod/github.com/spf13/cobra@v1.8.1/command.go:989\ngithub.com/spf13/cobra.(*Command).ExecuteC\n\t/home/runner/go/pkg/mod/github.com/spf13/cobra@v1.8.1/command.go:1117\ngithub.com/spf13/cobra.(*Command).Execute\n\t/home/runner/go/pkg/mod/github.com/spf13/cobra@v1.8.1/command.go:1041\ngithub.com/external-secrets/external-secrets/cmd.Execute\n\t/home/runner/work/external-secrets/external-secrets/cmd/root.go:258\nmain.main\n\t/home/runner/work/external-secrets/external-secrets/main.go:22\nruntime.main\n\t/opt/hostedtoolcache/go/1.23.1/x64/src/runtime/proc.go:272"}
{"level":"info","ts":1766170045.7366672,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766170045.7382169,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1beta1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.7382674,"logger":"controller-runtime.builder","msg":"Registering a validating webhook","GVK":"external-secrets.io/v1beta1, Kind=ExternalSecret","path":"/validate-external-secrets-io-v1beta1-externalsecret"}
{"level":"info","ts":1766170045.7384555,"logger":"controller-runtime.webhook","msg":"Registering webhook","path":"/validate-external-secrets-io-v1beta1-externalsecret"}
{"level":"info","ts":1766170045.7385666,"logger":"controller-runtime.webhook","msg":"Registering webhook","path":"/convert"}
{"level":"info","ts":1766170045.7385795,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1beta1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.7385983,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1beta1, Kind=SecretStore"}
{"level":"info","ts":1766170045.7386265,"logger":"controller-runtime.builder","msg":"Registering a validating webhook","GVK":"external-secrets.io/v1beta1, Kind=SecretStore","path":"/validate-external-secrets-io-v1beta1-secretstore"}
{"level":"info","ts":1766170045.7386785,"logger":"controller-runtime.webhook","msg":"Registering webhook","path":"/validate-external-secrets-io-v1beta1-secretstore"}
{"level":"info","ts":1766170045.7387228,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1beta1, Kind=SecretStore"}
{"level":"info","ts":1766170045.7387364,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1beta1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.7387667,"logger":"controller-runtime.builder","msg":"Registering a validating webhook","GVK":"external-secrets.io/v1beta1, Kind=ClusterSecretStore","path":"/validate-external-secrets-io-v1beta1-clustersecretstore"}
{"level":"info","ts":1766170045.7388265,"logger":"controller-runtime.webhook","msg":"Registering webhook","path":"/validate-external-secrets-io-v1beta1-clustersecretstore"}
{"level":"info","ts":1766170045.7388623,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1beta1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.7388802,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.738887,"logger":"controller-runtime.builder","msg":"skip registering a validating webhook, object does not implement admission.Validator or WithValidator wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.7389185,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1alpha1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.7389414,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=SecretStore"}
{"level":"info","ts":1766170045.738949,"logger":"controller-runtime.builder","msg":"skip registering a validating webhook, object does not implement admission.Validator or WithValidator wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=SecretStore"}
{"level":"info","ts":1766170045.738973,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1alpha1, Kind=SecretStore"}
{"level":"info","ts":1766170045.7389963,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.739013,"logger":"controller-runtime.builder","msg":"skip registering a validating webhook, object does not implement admission.Validator or WithValidator wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.73904,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1alpha1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.7390487,"logger":"setup","msg":"starting manager"}
{"level":"info","ts":1766170045.739152,"msg":"starting server","name":"health probe","addr":"[::]:8081"}
{"level":"info","ts":1766170045.7391315,"logger":"controller-runtime.metrics","msg":"Starting metrics server"}
{"level":"info","ts":1766170045.7392218,"logger":"controller-runtime.webhook","msg":"Starting webhook server"}
{"level":"info","ts":1766170045.7392483,"logger":"controller-runtime.metrics","msg":"Serving metrics server","bindAddress":":8080","secure":false}
{"level":"info","ts":1766170045.7395322,"logger":"controller-runtime.certwatcher","msg":"Updated current TLS certificate"}
{"level":"info","ts":1766170045.7396257,"logger":"controller-runtime.webhook","msg":"Serving webhook server","host":"","port":10250}
{"level":"info","ts":1766170045.7396789,"logger":"controller-runtime.certwatcher","msg":"Starting certificate watcher"}
{"level":"info","ts":1766170345.7386124,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766170345.7389946,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766170645.738081,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766170645.7387476,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766170945.7388382,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766170945.7392204,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766171245.7385771,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766171245.7390757,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766171545.7388167,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766171545.739297,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766171845.737904,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766171845.7384782,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766172145.7385669,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766172145.7389789,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766172445.7380176,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766172445.7384498,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766172745.7379277,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766172745.7384052,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766173045.7385938,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766173045.7389843,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766173345.738105,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766173345.7385082,"logger":"setup","msg":"certs are valid"}


==================== 33-3-eso-logs-since-60m-argocd/cdr-external-secret-operator-external-secrets-webhook ====================

+ kubectl logs -n argocd deploy/cdr-external-secret-operator-external-secrets-webhook --since=60m
{"level":"info","ts":1766170035.7355864,"logger":"setup","msg":"validating certs"}
{"level":"error","ts":1766170035.735631,"logger":"setup","msg":"invalid certs. retrying...","error":"stat /tmp/certs/tls.crt: no such file or directory","stacktrace":"github.com/external-secrets/external-secrets/cmd.waitForCerts\n\t/home/runner/work/external-secrets/external-secrets/cmd/webhook.go:215\ngithub.com/external-secrets/external-secrets/cmd.init.func3\n\t/home/runner/work/external-secrets/external-secrets/cmd/webhook.go:84\ngithub.com/spf13/cobra.(*Command).execute\n\t/home/runner/go/pkg/mod/github.com/spf13/cobra@v1.8.1/command.go:989\ngithub.com/spf13/cobra.(*Command).ExecuteC\n\t/home/runner/go/pkg/mod/github.com/spf13/cobra@v1.8.1/command.go:1117\ngithub.com/spf13/cobra.(*Command).Execute\n\t/home/runner/go/pkg/mod/github.com/spf13/cobra@v1.8.1/command.go:1041\ngithub.com/external-secrets/external-secrets/cmd.Execute\n\t/home/runner/work/external-secrets/external-secrets/cmd/root.go:258\nmain.main\n\t/home/runner/work/external-secrets/external-secrets/main.go:22\nruntime.main\n\t/opt/hostedtoolcache/go/1.23.1/x64/src/runtime/proc.go:272"}
{"level":"info","ts":1766170045.7366672,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766170045.7382169,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1beta1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.7382674,"logger":"controller-runtime.builder","msg":"Registering a validating webhook","GVK":"external-secrets.io/v1beta1, Kind=ExternalSecret","path":"/validate-external-secrets-io-v1beta1-externalsecret"}
{"level":"info","ts":1766170045.7384555,"logger":"controller-runtime.webhook","msg":"Registering webhook","path":"/validate-external-secrets-io-v1beta1-externalsecret"}
{"level":"info","ts":1766170045.7385666,"logger":"controller-runtime.webhook","msg":"Registering webhook","path":"/convert"}
{"level":"info","ts":1766170045.7385795,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1beta1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.7385983,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1beta1, Kind=SecretStore"}
{"level":"info","ts":1766170045.7386265,"logger":"controller-runtime.builder","msg":"Registering a validating webhook","GVK":"external-secrets.io/v1beta1, Kind=SecretStore","path":"/validate-external-secrets-io-v1beta1-secretstore"}
{"level":"info","ts":1766170045.7386785,"logger":"controller-runtime.webhook","msg":"Registering webhook","path":"/validate-external-secrets-io-v1beta1-secretstore"}
{"level":"info","ts":1766170045.7387228,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1beta1, Kind=SecretStore"}
{"level":"info","ts":1766170045.7387364,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1beta1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.7387667,"logger":"controller-runtime.builder","msg":"Registering a validating webhook","GVK":"external-secrets.io/v1beta1, Kind=ClusterSecretStore","path":"/validate-external-secrets-io-v1beta1-clustersecretstore"}
{"level":"info","ts":1766170045.7388265,"logger":"controller-runtime.webhook","msg":"Registering webhook","path":"/validate-external-secrets-io-v1beta1-clustersecretstore"}
{"level":"info","ts":1766170045.7388623,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1beta1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.7388802,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.738887,"logger":"controller-runtime.builder","msg":"skip registering a validating webhook, object does not implement admission.Validator or WithValidator wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.7389185,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1alpha1, Kind=ExternalSecret"}
{"level":"info","ts":1766170045.7389414,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=SecretStore"}
{"level":"info","ts":1766170045.738949,"logger":"controller-runtime.builder","msg":"skip registering a validating webhook, object does not implement admission.Validator or WithValidator wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=SecretStore"}
{"level":"info","ts":1766170045.738973,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1alpha1, Kind=SecretStore"}
{"level":"info","ts":1766170045.7389963,"logger":"controller-runtime.builder","msg":"skip registering a mutating webhook, object does not implement admission.Defaulter or WithDefaulter wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.739013,"logger":"controller-runtime.builder","msg":"skip registering a validating webhook, object does not implement admission.Validator or WithValidator wasn't called","GVK":"external-secrets.io/v1alpha1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.73904,"logger":"controller-runtime.builder","msg":"Conversion webhook enabled","GVK":"external-secrets.io/v1alpha1, Kind=ClusterSecretStore"}
{"level":"info","ts":1766170045.7390487,"logger":"setup","msg":"starting manager"}
{"level":"info","ts":1766170045.739152,"msg":"starting server","name":"health probe","addr":"[::]:8081"}
{"level":"info","ts":1766170045.7391315,"logger":"controller-runtime.metrics","msg":"Starting metrics server"}
{"level":"info","ts":1766170045.7392218,"logger":"controller-runtime.webhook","msg":"Starting webhook server"}
{"level":"info","ts":1766170045.7392483,"logger":"controller-runtime.metrics","msg":"Serving metrics server","bindAddress":":8080","secure":false}
{"level":"info","ts":1766170045.7395322,"logger":"controller-runtime.certwatcher","msg":"Updated current TLS certificate"}
{"level":"info","ts":1766170045.7396257,"logger":"controller-runtime.webhook","msg":"Serving webhook server","host":"","port":10250}
{"level":"info","ts":1766170045.7396789,"logger":"controller-runtime.certwatcher","msg":"Starting certificate watcher"}
{"level":"info","ts":1766170345.7386124,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766170345.7389946,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766170645.738081,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766170645.7387476,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766170945.7388382,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766170945.7392204,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766171245.7385771,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766171245.7390757,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766171545.7388167,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766171545.739297,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766171845.737904,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766171845.7384782,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766172145.7385669,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766172145.7389789,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766172445.7380176,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766172445.7384498,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766172745.7379277,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766172745.7384052,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766173045.7385938,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766173045.7389843,"logger":"setup","msg":"certs are valid"}
{"level":"info","ts":1766173345.738105,"logger":"setup","msg":"validating certs"}
{"level":"info","ts":1766173345.7385082,"logger":"setup","msg":"certs are valid"}


==================== 34-3-eso-events-argocd ====================

+ kubectl get events -n argocd --sort-by=.lastTimestamp
LAST SEEN   TYPE      REASON               OBJECT                                                                               MESSAGE
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Successfully assigned argocd/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j to i-0de7b4307ef02f19f
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Successfully assigned argocd/cdr-external-secret-operator-external-secrets-cert-controllmwm9 to i-0de7b4307ef02f19f
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Successfully assigned argocd/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs to i-0de7b4307ef02f19f
57m         Normal    Scheduled            pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Successfully assigned argocd/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h to i-0de7b4307ef02f19f
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Healthy -> Missing
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Synced -> OutOfSync
57m         Normal    OperationStarted     application/cdr-external-secret-operator                                             Initiated automated sync to 'da5c7489781d26ebfccfdd760679c6f275bef484'
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets                             Scaled up replica set cdr-external-secret-operator-external-secrets-9c858c9c8 from 0 to 2
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets-webhook                     Scaled up replica set cdr-external-secret-operator-external-secrets-webhook-6b69586684 from 0 to 1
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-webhook-6b69586684          Created pod: cdr-external-secret-operator-external-secrets-webhook-6b696qk4j
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-9c858c9c8                   Created pod: cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-9c858c9c8                   Created pod: cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs
57m         Normal    SuccessfulCreate     replicaset/cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95   Created pod: cdr-external-secret-operator-external-secrets-cert-controllmwm9
57m         Normal    ScalingReplicaSet    deployment/cdr-external-secret-operator-external-secrets-cert-controller             Scaled up replica set cdr-external-secret-operator-external-secrets-cert-controller-7f4f76f95 from 0 to 1
57m         Normal    OperationCompleted   application/cdr-external-secret-operator                                             Sync operation to da5c7489781d26ebfccfdd760679c6f275bef484 succeeded
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Pulling              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Pulling image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5"
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Started container webhook
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Started container cert-controller
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Started container external-secrets
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.438s (8.438s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-webhook-6b696qk4j                  Created container: webhook
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.435s (8.435s including waiting). Image size: 65934511 bytes.
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.404s (8.404s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-tdkcs                    Created container: external-secrets
57m         Normal    Started              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Started container external-secrets
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Created container: external-secrets
57m         Normal    Pulled               pod/cdr-external-secret-operator-external-secrets-9c858c9c8-2zz8h                    Successfully pulled image "oci.external-secrets.io/external-secrets/external-secrets:v0.10.5" in 8.381s (8.381s including waiting). Image size: 65934511 bytes.
57m         Normal    Created              pod/cdr-external-secret-operator-external-secrets-cert-controllmwm9                  Created container: cert-controller
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: OutOfSync -> Synced
57m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Missing -> Progressing
56m         Normal    OperationStarted     application/secret-store                                                             Initiated automated sync to 'da5c7489781d26ebfccfdd760679c6f275bef484'
56m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated health status: Progressing -> Healthy
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated health status: Healthy -> Progressing
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated sync status: OutOfSync -> Synced
56m         Normal    OperationCompleted   application/secret-store                                                             Sync operation to da5c7489781d26ebfccfdd760679c6f275bef484 succeeded
56m         Normal    ResourceUpdated      application/secret-store                                                             Updated health status: Progressing -> Healthy
55m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Synced -> Unknown
54m         Normal    ResourceUpdated      application/cdr-external-secret-operator                                             Updated sync status: Unknown -> Synced
50m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to '70dfc2f1a709bb12f3e433305615efd8797a20bb'
50m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Unknown -> OutOfSync
50m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated health status: Healthy -> Progressing
50m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated health status: Progressing -> Degraded
44m         Warning   OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to 70dfc2f1a709bb12f3e433305615efd8797a20bb failed: one or more synchronization tasks completed unsuccessfully (retried 5 times).
42m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to '841f56749a9e03dc0be2fdae89b429b18920a781'
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
42m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
42m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to 841f56749a9e03dc0be2fdae89b429b18920a781 succeeded
41m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to 'a75c2320be733ae8be1b663016fc51b57ead9545'
41m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
36m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
36m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to a75c2320be733ae8be1b663016fc51b57ead9545 succeeded
23m         Normal    OperationStarted     application/transaction-rules-v2-dev                                                 Initiated automated sync to 'db725726cc199cbb64d62bf668169a7d53be62a3'
23m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> OutOfSync
23m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: OutOfSync -> Synced
23m         Normal    OperationCompleted   application/transaction-rules-v2-dev                                                 Sync operation to db725726cc199cbb64d62bf668169a7d53be62a3 succeeded
16m         Normal    ResourceUpdated      application/transaction-rules-v2-dev                                                 Updated sync status: Synced -> Unknown


==================== 40-clustersecretstore-list ====================

+ kubectl get clustersecretstore -o wide
NAME                                           AGE   STATUS   CAPABILITIES   READY
omnidata-external-secrets-clustersecretstore   56m   Valid    ReadWrite      True


==================== 41-clustersecretstore-describe ====================

+ kubectl describe clustersecretstore omnidata-external-secrets-clustersecretstore
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
  Type    Reason  Age                  From                  Message
  ----    ------  ----                 ----                  -------
  Normal  Valid   100s (x13 over 56m)  cluster-secret-store  store validated
  Normal  Valid   100s (x13 over 56m)  cluster-secret-store  store validated


==================== 42-clustersecretstore-status-grep ====================

+ kubectl get clustersecretstore omnidata-external-secrets-clustersecretstore -o yaml | egrep -n 'status:|conditions:|message:|reason:|type:|status:' || true
24:status:
26:  conditions:
28:    message: store validated
29:    reason: Valid
30:    status: "True"
31:    type: Ready


==================== 50-externalsecret-list-omnidata ====================

+ kubectl get externalsecret -n omnidata -o wide
NAME                                                    STORE                                          REFRESH INTERVAL   STATUS              READY
transaction-rules-v2-dev-java-chart-app-env             omnidata-external-secrets-clustersecretstore   1m                 SecretSyncedError   False
transaction-rules-v2-dev-java-chart-application-certs   omnidata-external-secrets-clustersecretstore   1m                 SecretSyncedError   False


==================== 51-externalsecret-yaml-omnidata ====================

+ kubectl get externalsecret -n omnidata -o yaml
apiVersion: v1
items:
- apiVersion: external-secrets.io/v1beta1
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
- apiVersion: external-secrets.io/v1beta1
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
kind: List
metadata:
  resourceVersion: ""


==================== 52-events-omnidata ====================

+ kubectl get events -n omnidata --sort-by=.lastTimestamp
LAST SEEN   TYPE      REASON              OBJECT                                                                 MESSAGE
36m         Normal    Scheduled           pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn               Successfully assigned omnidata/transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn to i-0de7b4307ef02f19f
49m         Normal    Scheduled           pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               Successfully assigned omnidata/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk to i-0de7b4307ef02f19f
50m         Normal    Scheduled           pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-2c8lx               Successfully assigned omnidata/transaction-rules-v2-dev-java-chart-5795b4fbb4-2c8lx to i-0de7b4307ef02f19f
50m         Normal    SuccessfulCreate    replicaset/transaction-rules-v2-dev-java-chart-5795b4fbb4              Created pod: transaction-rules-v2-dev-java-chart-5795b4fbb4-2c8lx
50m         Normal    Pulled              pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-2c8lx               Container image "fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2:2.0.0-20251218_174100-eff70050-SNAPSHOT" already present on machine
50m         Normal    Started             pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-2c8lx               Started container java-chart
50m         Normal    Created             pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-2c8lx               Created container: java-chart
50m         Normal    Killing             pod/transaction-rules-v2-dev-java-chart-5649cc8d95-w9pfb               Stopping container java-chart
50m         Normal    ScalingReplicaSet   deployment/transaction-rules-v2-dev-java-chart                         Scaled down replica set transaction-rules-v2-dev-java-chart-5649cc8d95 from 1 to 0
50m         Normal    SuccessfulDelete    replicaset/transaction-rules-v2-dev-java-chart-5649cc8d95              Deleted pod: transaction-rules-v2-dev-java-chart-5649cc8d95-w9pfb
49m         Normal    Killing             pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-2c8lx               Stopping container java-chart
49m         Normal    ScalingReplicaSet   deployment/transaction-rules-v2-dev-java-chart                         Scaled up replica set transaction-rules-v2-dev-java-chart-5795b4fbb4 from 0 to 1
49m         Normal    SuccessfulCreate    replicaset/transaction-rules-v2-dev-java-chart-5795b4fbb4              Created pod: transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk
41m         Warning   FailedMount         pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               MountVolume.SetUp failed for volume "snowflake-certs" : secret "application-certs" not found
41m         Warning   FailedMount         pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               MountVolume.SetUp failed for volume "kafka-certs" : secret "application-certs" not found
39m         Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-application-certs   error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist
38m         Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-application-certs   error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist
37m         Normal    Started             pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               Started container java-chart
37m         Normal    Created             pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               Created container: java-chart
37m         Normal    Pulled              pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               Container image "fmk.nexus-dev.onefiserv.net/apm/0011753/transaction-data-rules-v2:2.0.0-20251218_174100-eff70050-SNAPSHOT" already present on machine
36m         Warning   BackOff             pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               Back-off restarting failed container java-chart in pod transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk_omnidata(19ff1466-55ae-45ac-b96a-c285e2c1d802)
36m         Normal    SuccessfulCreate    replicaset/transaction-rules-v2-dev-java-chart-5795b4fbb4              Created pod: transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn
9m34s       Warning   FailedMount         pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn               MountVolume.SetUp failed for volume "kafka-certs" : secret "application-certs" not found
5m39s       Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-app-env             error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist
5m38s       Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-app-env             error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist
5m38s       Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-application-certs   error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist
5m37s       Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-application-certs   error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist
5m30s       Warning   FailedMount         pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn               MountVolume.SetUp failed for volume "snowflake-certs" : secret "application-certs" not found


==================== 53-secrets-list-omnidata ====================

+ kubectl get secret -n omnidata -o wide
No resources found in omnidata namespace.


==================== 54-1-externalsecret-describe-omnidata/transaction-rules-v2-dev-java-chart-app-env ====================

+ kubectl describe externalsecret -n omnidata transaction-rules-v2-dev-java-chart-app-env
Name:         transaction-rules-v2-dev-java-chart-app-env
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
  Resource Version:    11676187
  UID:                 93b726e4-fe1e-4e13-ac4b-cb621615488f
Spec:
  Data:
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    None
      Key:                  omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD
      Metadata Policy:      None
    Secret Key:             KAFKA_KEYSTORE_PASSWORD
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    None
      Key:                  omnidata/dev/transaction-rules-v2/env/DB_URL
      Metadata Policy:      None
    Secret Key:             DB_URL
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    None
      Key:                  omnidata/dev/transaction-rules-v2/env/DB_USERNAME
      Metadata Policy:      None
    Secret Key:             DB_USERNAME
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    None
      Key:                  omnidata/dev/transaction-rules-v2/env/DB_PRIVATE_KEY_PASSWORD
      Metadata Policy:      None
    Secret Key:             DB_PRIVATE_KEY_PASSWORD
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    None
      Key:                  omnidata/dev/transaction-rules-v2/env/KAFKA_TRUSTSTORE_PASSWORD
      Metadata Policy:      None
    Secret Key:             KAFKA_TRUSTSTORE_PASSWORD
  Refresh Interval:         1m
  Secret Store Ref:
    Kind:  ClusterSecretStore
    Name:  omnidata-external-secrets-clustersecretstore
  Target:
    Creation Policy:  Owner
    Deletion Policy:  Retain
    Name:             transaction-data-rules-v2-env
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
  Type     Reason        Age                   From              Message
  ----     ------        ----                  ----              -------
  Warning  UpdateFailed  5m40s (x21 over 50m)  external-secrets  error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist
  Warning  UpdateFailed  5m39s (x21 over 50m)  external-secrets  error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD, err: Secret does not exist


==================== 54-2-externalsecret-describe-omnidata/transaction-rules-v2-dev-java-chart-application-certs ====================

+ kubectl describe externalsecret -n omnidata transaction-rules-v2-dev-java-chart-application-certs
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
  Generation:          3
  Resource Version:    11682453
  UID:                 66a58373-355e-4c98-a369-5e26d9c6ab02
Spec:
  Data:
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    Base64
      Key:                  omnidata/dev/transaction-rules-v2/env/wvcAp11753
      Metadata Policy:      None
    Secret Key:             wvcAp11753
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    Base64
      Key:                  omnidata/dev/transaction-rules-v2/env/kafka-truststore
      Metadata Policy:      None
    Secret Key:             kafka-truststore
    Remote Ref:
      Conversion Strategy:  Default
      Decoding Strategy:    Base64
      Key:                  omnidata/dev/transaction-rules-v2/env/snowflake-cert
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
  Type     Reason        Age                  From              Message
  ----     ------        ----                 ----              -------
  Warning  UpdateFailed  39m (x21 over 50m)   external-secrets  error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist
  Warning  UpdateFailed  39m (x21 over 50m)   external-secrets  error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist
  Warning  UpdateFailed  5m40s (x3 over 23m)  external-secrets  error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist
  Warning  UpdateFailed  5m39s (x3 over 23m)  external-secrets  error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist


==================== 60-expected-secrets-details-omnidata ====================


---- Secret: omnidata/application-certs ----
[MISSING] does not exist

---- Secret: omnidata/transaction-data-rules-v2-env ----
[MISSING] does not exist


==================== 70-pods-failedmount-secret-not-found-omnidata ====================

+ kubectl get events -n omnidata --sort-by=.lastTimestamp | egrep -i 'FailedMount|secret.*not found|MountVolume|application-certs' || true
41m         Warning   FailedMount         pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               MountVolume.SetUp failed for volume "snowflake-certs" : secret "application-certs" not found
41m         Warning   FailedMount         pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-bc6vk               MountVolume.SetUp failed for volume "kafka-certs" : secret "application-certs" not found
39m         Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-application-certs   error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist
39m         Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-application-certs   error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64, err: Secret does not exist
9m38s       Warning   FailedMount         pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn               MountVolume.SetUp failed for volume "kafka-certs" : secret "application-certs" not found
5m42s       Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-application-certs   error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist
5m41s       Warning   UpdateFailed        externalsecret/transaction-rules-v2-dev-java-chart-application-certs   error retrieving secret at .data[0], key: omnidata/dev/transaction-rules-v2/env/wvcAp11753, err: Secret does not exist
5m34s       Warning   FailedMount         pod/transaction-rules-v2-dev-java-chart-5795b4fbb4-ql2wn               MountVolume.SetUp failed for volume "snowflake-certs" : secret "application-certs" not found


==================== 80-argocd-apps-all ====================

+ kubectl get applications -A
NAMESPACE   NAME                           SYNC STATUS   HEALTH STATUS
argocd      cdr-external-secret-operator   Synced        Healthy
argocd      ingress-class-controller       Synced        Healthy
argocd      opentelemetry-dev              Synced        Healthy
argocd      secret-store                   Synced        Healthy
argocd      transaction-data-parquet-dev   Unknown       Healthy
argocd      transaction-rules-v2-dev       Unknown       Degraded


==================== 81-argocd-apps-filter-eso ====================

+ kubectl get applications -A | egrep -i 'external-secret|external-secrets|cdr|operator' || true
argocd      cdr-external-secret-operator   Synced        Healthy


==================== _SUMMARY ====================

Se quiser colar aqui no chat, cole o conteúdo do arquivo: eso_report.txt
Comando:
  cat eso_report.txt

✅ Pronto. Arquivo gerado: eso_report.txt
Agora rode: cat eso_report.txt  (e cole aqui)
(END)
