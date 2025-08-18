AWSTemplateFormatVersion: '2010-09-09'
Description: ALB + TargetGroup + Listener/Rule + ECS Task Definition (Fargate-ready)

Parameters:
  VpcId:
    Type: AWS::EC2::VPC::Id
    Description: VPC onde o ALB será criado
  PublicSubnets:
    Type: List<AWS::EC2::Subnet::Id>
    Description: Subnets públicas para o ALB
  AppPort:
    Type: Number
    Default: 8080
    Description: Porta exposta pelo container/app
  HealthCheckPath:
    Type: String
    Default: /health
    Description: Caminho de health check do Target Group
  HostHeader:
    Type: String
    Default: '*'
    Description: Host header para a rule (ex: api.example.com). Use * para qualquer host
  PathPattern:
    Type: String
    Default: /*
    Description: Path pattern para a rule (ex: /api/*)
  RulePriority:
    Type: Number
    Default: 10
    Description: Prioridade da listener rule (menor número = maior prioridade)
  ContainerImage:
    Type: String
    Default: public.ecr.aws/nginx/nginx:latest
    Description: Imagem do container
  ContainerCpu:
    Type: Number
    Default: 256
    Description: CPU (task definition) – unidades CPU
  ContainerMemory:
    Type: Number
    Default: 512
    Description: Memória (MB) – task definition
  LogRetentionDays:
    Type: Number
    Default: 14
    Description: Retenção (dias) para CloudWatch Logs

Resources:
  AlbSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: SG do ALB
      VpcId: !Ref VpcId
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  ApplicationLoadBalancer:
    Type: AWS::ElasticLoadBalancingV2::LoadBalancer
    Properties:
      Name: !Sub ${AWS::StackName}-alb
      Scheme: internet-facing
      Type: application
      IpAddressType: ipv4
      SecurityGroups: [!Ref AlbSecurityGroup]
      Subnets: !Ref PublicSubnets

  AppTargetGroup:
    Type: AWS::ElasticLoadBalancingV2::TargetGroup
    Properties:
      Name: !Sub ${AWS::StackName}-tg
      VpcId: !Ref VpcId
      Port: !Ref AppPort
      Protocol: HTTP
      TargetType: ip        # para ECS Fargate (awsvpc)
      HealthCheckEnabled: true
      HealthCheckPath: !Ref HealthCheckPath
      HealthCheckProtocol: HTTP
      Matcher:
        HttpCode: '200-399'

  HttpListener:
    Type: AWS::ElasticLoadBalancingV2::Listener
    Properties:
      LoadBalancerArn: !Ref ApplicationLoadBalancer
      Port: 80
      Protocol: HTTP
      DefaultActions:
        - Type: fixed-response
          FixedResponseConfig:
            StatusCode: '404'
            ContentType: text/plain
            MessageBody: 'Not Found'

  AppListenerRule:
    Type: AWS::ElasticLoadBalancingV2::ListenerRule
    Properties:
      ListenerArn: !Ref HttpListener
      Priority: !Ref RulePriority
      Conditions:
        - Field: host-header
          HostHeaderConfig:
            Values: [!Ref HostHeader]
        - Field: path-pattern
          PathPatternConfig:
            Values: [!Ref PathPattern]
      Actions:
        - Type: forward
          TargetGroupArn: !Ref AppTargetGroup

  ExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: !Sub ${AWS::StackName}-ecs-exec
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service: ecs-tasks.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy

  AppLogGroup:
    Type: AWS::Logs::LogGroup
    Properties:
      LogGroupName: !Sub /ecs/${AWS::StackName}
      RetentionInDays: !Ref LogRetentionDays

  TaskDefinition:
    Type: AWS::ECS::TaskDefinition
    Properties:
      Family: !Sub ${AWS::StackName}-td
      RequiresCompatibilities: [FARGATE]
      Cpu: !Ref ContainerCpu
      Memory: !Ref ContainerMemory
      NetworkMode: awsvpc
      ExecutionRoleArn: !GetAtt ExecutionRole.Arn
      ContainerDefinitions:
        - Name: app
          Image: !Ref ContainerImage
          PortMappings:
            - ContainerPort: !Ref AppPort
              Protocol: tcp
          Essential: true
          LogConfiguration:
            LogDriver: awslogs
            Options:
              awslogs-group: !Ref AppLogGroup
              awslogs-region: !Ref AWS::Region
              awslogs-stream-prefix: app

Outputs:
  AlbDNS:
    Description: DNS do ALB
    Value: !GetAtt ApplicationLoadBalancer.DNSName
  TargetGroupArn:
    Description: ARN do Target Group
    Value: !Ref AppTargetGroup
  ListenerArn:
    Description: ARN do Listener HTTP
    Value: !Ref HttpListener
  ListenerRuleArn:
    Description: ARN da Listener Rule
    Value: !Ref AppListenerRule
  TaskDefinitionArn:
    Description: ARN da Task Definition
    Value: !Ref TaskDefinition
