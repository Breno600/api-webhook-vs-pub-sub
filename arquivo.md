~ $ echo | openssl s_client -servername oidc.eks.us-east-1.amazonaws.com/id/EB5DD51801CB3D0100E96F2126345668 -showcerts -connect oidc.eks.us-east-1.amazonaws.com/id/EB5DD51801CB3D0100E96F2126345668:443 2>/dev/null   | openssl x509 -fingerprint -noout   | cut -d= -f2 | tr -d ':' | tr '[:upper:]' '[:lower:]'
Could not find certificate from <stdin>
