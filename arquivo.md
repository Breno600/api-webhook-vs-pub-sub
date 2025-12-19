~ $ kubectl apply -f cluster.yaml -n omnidata
error: resource mapping not found for name: "" namespace: "omnidata" from "cluster.yaml": no matches for kind "ClusterSecretStore" in version "external-secrets.io/v1beta1"
ensure CRDs are installed first
~ $ cat cluster.yaml 
apiVersion: external-secrets.io/v1beta1
kind: ClusterSecretStore
metadata:
  labels:
    argocd.argoproj.io/instance: secret-store
    name: omnidata-external-secrets-clustersecretstore
  namespace: omnidata
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

~ $ 
