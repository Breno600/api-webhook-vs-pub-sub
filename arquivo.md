OIDC_URL="oidc.eks.us-east-1.amazonaws.com/id/EB5DD51801CB3D0100E96F2126345668"

THUMBPRINT=$(echo | openssl s_client -servername ${OIDC_URL} -showcerts -connect ${OIDC_URL}:443 2>/dev/null \
  | openssl x509 -fingerprint -noout \
  | cut -d= -f2 | tr -d ':' | tr '[:upper:]' '[:lower:]')

echo $THUMBPRINT

aws iam create-open-id-connect-provider \
  --url https://oidc.eks.us-east-1.amazonaws.com/id/EB5DD51801CB3D0100E96F2126345668 \
  --client-id-list sts.amazonaws.com \
  --thumbprint-list $THUMBPRINT
