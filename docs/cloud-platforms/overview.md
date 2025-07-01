# 云平台服务 - 总览

## 概述

云计算已成为现代应用开发和部署的基础设施，主流云平台提供了丰富的服务来支持各种业务场景。本章节深入介绍AWS、Azure、Google Cloud等主要云平台的核心服务，帮助开发者选择和使用合适的云服务。

## 📚 章节内容

### 1. [AWS核心服务](./aws-services.md)
- **计算服务**: EC2、Lambda、ECS、EKS无服务器计算
- **存储服务**: S3、EBS、EFS分布式存储解决方案
- **数据库**: RDS、DynamoDB、ElastiCache数据管理
- **网络服务**: VPC、CloudFront、Route 53网络架构
- **监控运维**: CloudWatch、CloudTrail、Systems Manager

### 2. [Azure云服务](./azure-services.md)
- **计算平台**: Virtual Machines、App Service、Azure Functions
- **数据服务**: Cosmos DB、SQL Database、Storage Account
- **AI/ML服务**: Cognitive Services、Machine Learning、Bot Service
- **开发工具**: Azure DevOps、Application Insights、Key Vault
- **企业集成**: Active Directory、Logic Apps、Service Bus

### 3. [Google Cloud平台](./gcp-services.md)
- **计算引擎**: Compute Engine、Cloud Functions、Cloud Run
- **大数据分析**: BigQuery、Dataflow、Pub/Sub消息队列
- **机器学习**: AI Platform、AutoML、TensorFlow Enterprise
- **开发工具**: Cloud Build、Cloud Source Repositories
- **安全服务**: Identity and Access Management、Security Command Center

### 4. [多云架构设计](./multi-cloud.md)
- **云平台选择**: 成本对比、服务特色、地域覆盖
- **迁移策略**: 应用现代化、数据迁移、混合云架构
- **成本优化**: 资源调度、预留实例、Spot实例使用
- **安全合规**: 数据主权、合规认证、安全最佳实践
- **避免锁定**: 云原生工具、容器化、标准化API

### 5. [云原生开发](./cloud-native.md)
- **微服务架构**: 服务网格、API网关、服务发现
- **容器编排**: Kubernetes、Docker、Helm包管理
- **DevOps流程**: CI/CD流水线、基础设施即代码
- **可观测性**: 分布式追踪、日志聚合、指标监控
- **安全防护**: 容器安全、密钥管理、网络策略

## 🎯 主流云平台对比

### 服务功能对比

#### 核心服务对比表
| 服务类型 | AWS | Azure | Google Cloud | 特点 |
|----------|-----|-------|--------------|------|
| **计算** | EC2, Lambda | VM, Functions | Compute Engine, Cloud Functions | 弹性扩容 |
| **存储** | S3, EBS | Blob Storage, Disk | Cloud Storage, Persistent Disk | 高可用性 |
| **数据库** | RDS, DynamoDB | SQL DB, Cosmos DB | Cloud SQL, Firestore | 托管服务 |
| **AI/ML** | SageMaker | ML Studio | AI Platform | 模型训练 |
| **容器** | EKS, ECS | AKS, Container Instances | GKE, Cloud Run | 容器编排 |

### 成本优化策略

#### AWS成本优化
```
计算优化:
• Reserved Instances - 预留实例节省成本
• Spot Instances - 竞价实例降低费用
• Auto Scaling - 自动扩缩容
• Right Sizing - 合理配置资源

存储优化:
• S3 Storage Classes - 智能存储分层
• Lifecycle Policies - 生命周期管理
• Data Compression - 数据压缩
• CloudFront CDN - 减少数据传输
```

## 💡 AWS实践案例

### 无服务器Web应用架构

#### Lambda + API Gateway + DynamoDB
```python
import json
import boto3
from decimal import Decimal

# DynamoDB连接
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('todos')

def lambda_handler(event, context):
    http_method = event['httpMethod']
    path = event['path']
    
    try:
        if http_method == 'GET' and path == '/todos':
            # 获取所有待办事项
            response = table.scan()
            items = response['Items']
            
            # 转换Decimal类型为float
            for item in items:
                for key, value in item.items():
                    if isinstance(value, Decimal):
                        item[key] = float(value)
            
            return {
                'statusCode': 200,
                'headers': {
                    'Content-Type': 'application/json',
                    'Access-Control-Allow-Origin': '*'
                },
                'body': json.dumps(items)
            }
            
        elif http_method == 'POST' and path == '/todos':
            # 创建新的待办事项
            body = json.loads(event['body'])
            
            item = {
                'id': context.aws_request_id,
                'title': body['title'],
                'completed': False,
                'created_at': int(time.time())
            }
            
            table.put_item(Item=item)
            
            return {
                'statusCode': 201,
                'headers': {
                    'Content-Type': 'application/json',
                    'Access-Control-Allow-Origin': '*'
                },
                'body': json.dumps(item, default=str)
            }
            
        elif http_method == 'PUT' and '/todos/' in path:
            # 更新待办事项
            todo_id = path.split('/')[-1]
            body = json.loads(event['body'])
            
            table.update_item(
                Key={'id': todo_id},
                UpdateExpression='SET completed = :completed',
                ExpressionAttributeValues={':completed': body['completed']}
            )
            
            return {
                'statusCode': 200,
                'headers': {
                    'Access-Control-Allow-Origin': '*'
                },
                'body': json.dumps({'message': 'Updated successfully'})
            }
            
    except Exception as e:
        return {
            'statusCode': 500,
            'headers': {
                'Access-Control-Allow-Origin': '*'
            },
            'body': json.dumps({'error': str(e)})
        }
```

### CloudFormation基础设施即代码

#### 完整的Web应用栈定义
```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Serverless Todo Application Stack'

Parameters:
  Environment:
    Type: String
    Default: dev
    AllowedValues: [dev, staging, prod]

Resources:
  # DynamoDB表
  TodoTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Sub 'todos-${Environment}'
      BillingMode: PAY_PER_REQUEST
      AttributeDefinitions:
        - AttributeName: id
          AttributeType: S
      KeySchema:
        - AttributeName: id
          KeyType: HASH
      StreamSpecification:
        StreamViewType: NEW_AND_OLD_IMAGES

  # Lambda执行角色
  LambdaRole:
    Type: AWS::IAM::Role
    Properties:
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
      ManagedPolicyArns:
        - arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole
      Policies:
        - PolicyName: DynamoDBAccess
          PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Effect: Allow
                Action:
                  - dynamodb:GetItem
                  - dynamodb:PutItem
                  - dynamodb:UpdateItem
                  - dynamodb:DeleteItem
                  - dynamodb:Scan
                  - dynamodb:Query
                Resource: !GetAtt TodoTable.Arn

  # Lambda函数
  TodoFunction:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: !Sub 'todo-api-${Environment}'
      Runtime: python3.9
      Handler: index.lambda_handler
      Role: !GetAtt LambdaRole.Arn
      Environment:
        Variables:
          TABLE_NAME: !Ref TodoTable
          ENVIRONMENT: !Ref Environment
      Code:
        ZipFile: |
          # Lambda函数代码在这里
          def lambda_handler(event, context):
              return {'statusCode': 200, 'body': 'Hello World'}

  # API Gateway
  TodoApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: !Sub 'todo-api-${Environment}'
      Description: 'Todo Application API'

  # API Gateway资源
  TodoResource:
    Type: AWS::ApiGateway::Resource
    Properties:
      RestApiId: !Ref TodoApi
      ParentId: !GetAtt TodoApi.RootResourceId
      PathPart: todos

  # API Gateway方法
  TodoGetMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      RestApiId: !Ref TodoApi
      ResourceId: !Ref TodoResource
      HttpMethod: GET
      AuthorizationType: NONE
      Integration:
        Type: AWS_PROXY
        IntegrationHttpMethod: POST
        Uri: !Sub 'arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${TodoFunction.Arn}/invocations'

  # Lambda权限
  LambdaPermission:
    Type: AWS::Lambda::Permission
    Properties:
      FunctionName: !Ref TodoFunction
      Action: lambda:InvokeFunction
      Principal: apigateway.amazonaws.com
      SourceArn: !Sub 'arn:aws:execute-api:${AWS::Region}:${AWS::AccountId}:${TodoApi}/*/*'

  # S3存储桶（前端静态文件）
  WebsiteBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub 'todo-web-${Environment}-${AWS::AccountId}'
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html
      PublicAccessBlockConfiguration:
        BlockPublicAcls: false
        BlockPublicPolicy: false
        IgnorePublicAcls: false
        RestrictPublicBuckets: false

  # CloudFront分发
  CloudFrontDistribution:
    Type: AWS::CloudFront::Distribution
    Properties:
      DistributionConfig:
        Enabled: true
        Origins:
          - DomainName: !GetAtt WebsiteBucket.RegionalDomainName
            Id: S3Origin
            S3OriginConfig:
              OriginAccessIdentity: ''
        DefaultCacheBehavior:
          TargetOriginId: S3Origin
          ViewerProtocolPolicy: redirect-to-https
          CachePolicyId: 4135ea2d-6df8-44a3-9df3-4b5a84be39ad
        PriceClass: PriceClass_100

Outputs:
  ApiEndpoint:
    Description: 'API Gateway endpoint URL'
    Value: !Sub 'https://${TodoApi}.execute-api.${AWS::Region}.amazonaws.com/prod'
    Export:
      Name: !Sub '${AWS::StackName}-ApiEndpoint'

  WebsiteURL:
    Description: 'Website URL'
    Value: !GetAtt CloudFrontDistribution.DomainName
    Export:
      Name: !Sub '${AWS::StackName}-WebsiteURL'
```

## 🛠️ 容器化部署实践

### Kubernetes在云平台的应用

#### EKS集群配置
```yaml
# eks-cluster.yaml
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: todo-app-cluster
  region: us-west-2
  version: "1.24"

nodeGroups:
  - name: worker-nodes
    instanceType: t3.medium
    desiredCapacity: 2
    minSize: 1
    maxSize: 4
    volumeSize: 20
    ssh:
      allow: true
    iam:
      withAddonPolicies:
        autoScaler: true
        cloudWatch: true

addons:
  - name: vpc-cni
  - name: coredns
  - name: kube-proxy
  - name: aws-load-balancer-controller

cloudWatch:
  clusterLogging:
    enable: ["api", "audit", "authenticator", "controllerManager", "scheduler"]
```

#### 应用部署配置
```yaml
# todo-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: todo-app
  namespace: production
spec:
  replicas: 3
  selector:
    matchLabels:
      app: todo-app
  template:
    metadata:
      labels:
        app: todo-app
    spec:
      containers:
      - name: todo-app
        image: your-ecr-repo/todo-app:latest
        ports:
        - containerPort: 3000
        env:
        - name: DATABASE_URL
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: url
        - name: REDIS_URL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: redis-url
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5

---
apiVersion: v1
kind: Service
metadata:
  name: todo-app-service
  namespace: production
spec:
  selector:
    app: todo-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 3000
  type: LoadBalancer

---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: todo-app-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
  rules:
  - host: todo.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: todo-app-service
            port:
              number: 80
```

## 📊 云服务监控与运维

### CloudWatch监控配置

#### 自定义指标和告警
```python
import boto3
import json
from datetime import datetime

cloudwatch = boto3.client('cloudwatch')

def put_custom_metric(metric_name, value, unit='Count', namespace='TodoApp'):
    """发送自定义指标到CloudWatch"""
    try:
        cloudwatch.put_metric_data(
            Namespace=namespace,
            MetricData=[
                {
                    'MetricName': metric_name,
                    'Value': value,
                    'Unit': unit,
                    'Timestamp': datetime.utcnow()
                }
            ]
        )
    except Exception as e:
        print(f"Error sending metric: {e}")

def create_alarm(alarm_name, metric_name, threshold, comparison='GreaterThanThreshold'):
    """创建CloudWatch告警"""
    cloudwatch.put_metric_alarm(
        AlarmName=alarm_name,
        ComparisonOperator=comparison,
        EvaluationPeriods=2,
        MetricName=metric_name,
        Namespace='TodoApp',
        Period=300,
        Statistic='Sum',
        Threshold=threshold,
        ActionsEnabled=True,
        AlarmActions=[
            'arn:aws:sns:us-west-2:123456789012:todo-alerts'
        ],
        AlarmDescription=f'Alarm when {metric_name} exceeds {threshold}',
        Unit='Count'
    )
```

### 日志聚合和分析

#### CloudWatch Logs Insights查询
```sql
-- 分析API错误率
fields @timestamp, @message
| filter @message like /ERROR/
| stats count() by bin(5m)
| sort @timestamp desc

-- 分析Lambda函数性能
fields @timestamp, @duration, @billedDuration, @memorySize, @maxMemoryUsed
| filter @type = "REPORT"
| stats avg(@duration), max(@duration), min(@duration) by bin(5m)

-- 分析用户行为模式
fields @timestamp, requestId, userId, action
| filter action in ["create_todo", "complete_todo", "delete_todo"]
| stats count() by action, bin(1h)
| sort @timestamp desc
```

## 🚀 最佳实践建议

### 云架构设计原则
```
高可用性:
• 多可用区部署
• 自动故障转移
• 负载均衡配置
• 健康检查监控

可扩展性:
• 自动扩缩容
• 水平扩展设计
• 缓存策略优化
• 数据库读写分离

安全性:
• 最小权限原则
• 网络安全组配置
• 数据加密传输
• 定期安全审计

成本优化:
• 资源使用监控
• 自动化资源管理
• 预留实例规划
• 无服务器架构应用
```

### 云迁移策略
```
评估阶段:
• 现有架构分析
• 依赖关系梳理
• 性能基准测试
• 成本效益分析

迁移规划:
• 分阶段迁移计划
• 风险评估和应对
• 回滚策略制定
• 团队培训计划

实施阶段:
• 试点项目验证
• 渐进式迁移
• 实时监控调整
• 文档更新维护
``` 