api-webhook-vs-pub-sub $ aws secretsmanager list-secrets   --region us-east-1
{
    "SecretList": [
        {
            "ARN": "arn:aws:secretsmanager:us-east-1:909139952102:secret:omnidata/dev/transaction-rules-v2/env-uHclFG",
            "Name": "omnidata/dev/transaction-rules-v2/env",
            "Description": "envs do transaction rules omnidata",
            "LastChangedDate": "2025-12-19T19:01:17.936000+00:00",
            "LastAccessedDate": "2025-12-19T00:00:00+00:00",
            "Tags": [],
            "SecretVersionsToStages": {
                "25e46fa2-0640-4b01-b9f3-3331d993d43c": [
                    "AWSCURRENT"
                ],
                "34873505-b58b-46c8-8385-fb9bf5b58662": [
                    "AWSPREVIOUS"
                ]
            },
            "CreatedDate": "2025-12-19T17:36:42.173000+00:00"
        }
    ]
}
api-webhook-vs-pub-sub $ 
