kubectl get crd | grep external-secrets
kubectl get pods -n external-secrets
kubectl get deploy -n external-secrets

kubectl get externalsecret -n omnidata
kubectl describe externalsecret -n omnidata transaction-rules-v2-dev-java-chart-application-certs
