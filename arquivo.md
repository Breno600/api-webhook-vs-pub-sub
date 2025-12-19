aws sts get-caller-identity

aws secretsmanager describe-secret \
  --region us-east-1 \
  --secret-id 'omnidata/dev/transaction-rules-v2/env/KAFKA_KEYSTORE_PASSWORD'

aws secretsmanager describe-secret \
  --region us-east-1 \
  --secret-id 'omnidata/dev/transaction-rules-v2/env/wvcAp11753_b64'
kubectl -n omnidata get externalsecret transaction-rules-v2-dev-java-chart-app-env -o yaml
kubectl -n omnidata get externalsecret transaction-rules-v2-dev-java-chart-application-certs -o yaml
kubectl get clustersecretstore omnidata-external-secrets-clustersecretstore -o yaml
kubectl describe clustersecretstore omnidata-external-secrets-clustersecretstore
kubectl -n argocd get sa external-secrets -o yaml
kubectl get clustersecretstore omnidata-external-secrets-clustersecretstore -o yaml

kubectl -n omnidata get externalsecret transaction-rules-v2-dev-java-chart-app-env -o yaml

kubectl -n argocd get sa external-secrets -o yaml
