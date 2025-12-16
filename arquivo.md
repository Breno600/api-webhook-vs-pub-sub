{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<ACCOUNT_ID>:oidc-provider/oidc.eks.us-east-1.amazonaws.com/id/EB5DD51801CB3D0100E96F2126345668"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringLike": {
          "oidc.eks.us-east-1.amazonaws.com/id/EB5DD51801CB3D0100E96F2126345668:sub": "system:serviceaccount:*:*"
        },
        "StringEquals": {
          "oidc.eks.us-east-1.amazonaws.com/id/EB5DD51801CB3D0100E96F2126345668:aud": "sts.amazonaws.com"
        }
      }
    }
  ]
}
