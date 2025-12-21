AWSTemplateFormatVersion: '2010-09-09'
Parameters:
    SecretName:
        Type: String
        Description: 'Secret Name'
        Default: 'application-secrets-<+execution.steps.Validation.output.outputVariables.ENVIRONMENT>'
    SecretDescription:
        Type: String
        Description: 'Secret Description'
        Default: 'Secret for environment <+execution.steps.Validation.output.outputVariables.ENVIRONMENT>'
    SecretValue:
        Type: String
        Description: 'Secret JSON'
        NoEcho: true
    Project:
        Type: String
        Description: 'Project name for tagging'
        Default: '<+project.name>'
    Environment:
        Type: String
        Description: 'Environment name for tagging'
        Default: '<+execution.steps.Validation.output.outputVariables.ENVIRONMENT>'
    ManagedBy:
        Type: String
        Description: 'Managed by for tagging'
        Default: 'cloudformation'
Resources:
    SecretHarness:
        Type: AWS::SecretsManager::Secret
        Properties:
            Name: !Ref SecretName
            Description: !Ref SecretDescription
            SecretString: !Ref SecretValue
            Tags:
                - Key: Project
                  Value: !Ref Project
                - Key: Environment
                  Value: !Ref Environment
                - Key: ManagedBy
                  Value: !Ref ManagedBy
