# CI/CD流水线 - 总览

## 概述

CI/CD（持续集成/持续部署）是现代软件开发的核心实践，通过自动化构建、测试和部署流程，提高开发效率和软件质量。本章节涵盖CI/CD原理、工具选择、流水线设计和最佳实践。

## 📚 章节内容

### 1. [持续集成最佳实践](./continuous-integration.md)
- **版本控制**: Git工作流、分支策略
- **代码质量**: 静态分析、代码审查
- **自动化构建**: 构建脚本、依赖管理
- **集成频率**: 频繁提交、快速反馈

### 2. [自动化测试策略](./automated-testing.md)
- **测试金字塔**: 单元测试、集成测试、E2E测试
- **测试覆盖率**: 代码覆盖率、测试有效性
- **性能测试**: 负载测试、压力测试
- **安全测试**: 安全扫描、漏洞检测

### 3. [部署策略](./deployment-strategies.md)
- **蓝绿部署**: 零停机部署、快速回滚
- **金丝雀发布**: 渐进式部署、风险控制
- **滚动更新**: 逐步替换、服务连续性
- **A/B测试**: 特性切换、数据驱动决策

### 4. [GitOps工作流](./gitops.md)
- **声明式配置**: Infrastructure as Code
- **Git作为真实源**: 配置版本化
- **自动化同步**: 状态管理、自动修复
- **可观测性**: 部署状态、变更追踪

## 🎯 CI/CD工具对比

### CI/CD平台对比

| 平台 | 优势 | 劣势 | 适用场景 |
|------|------|------|---------|
| **Jenkins** | 高度可配置、插件丰富 | 维护成本高、学习曲线陡 | 复杂企业环境 |
| **GitHub Actions** | 与GitHub集成、易用 | 功能相对有限 | 开源项目、中小团队 |
| **GitLab CI** | 完整DevOps工具链 | 资源消耗较大 | 全栈DevOps需求 |
| **Azure DevOps** | 企业级功能、微软生态 | 绑定微软平台 | 企业客户 |
| **CircleCI** | 云原生、性能优秀 | 成本相对较高 | 快速迭代团队 |

### 容器化CI/CD

| 工具 | 特点 | 优势 | 适用场景 |
|------|------|------|---------|
| **Tekton** | Kubernetes原生 | 云原生、可扩展 | K8s环境 |
| **Argo Workflows** | 工作流引擎 | 灵活性高、DAG支持 | 复杂流程 |
| **Drone** | 轻量级、容器化 | 简单易用、资源高效 | 中小型项目 |

## 🛠️ 流水线实践

### GitHub Actions示例

#### 基础CI流水线
```yaml
name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [14.x, 16.x, 18.x]
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run linting
      run: npm run lint
    
    - name: Run tests
      run: npm run test:coverage
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage/coverage-final.json

  build:
    needs: test
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18.x'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Build application
      run: npm run build
    
    - name: Build Docker image
      run: |
        docker build -t ${{ github.repository }}:${{ github.sha }} .
        docker tag ${{ github.repository }}:${{ github.sha }} ${{ github.repository }}:latest
    
    - name: Push to registry
      if: github.ref == 'refs/heads/main'
      run: |
        echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
        docker push ${{ github.repository }}:${{ github.sha }}
        docker push ${{ github.repository }}:latest
```

### Jenkins流水线

#### Jenkinsfile示例
```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'your-registry.com'
        IMAGE_NAME = 'your-app'
        KUBECONFIG = credentials('kubeconfig')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'npm ci'
                        sh 'npm run test:unit'
                    }
                    post {
                        always {
                            publishTestResults testResultsPattern: 'test-results.xml'
                        }
                    }
                }
                
                stage('Security Scan') {
                    steps {
                        sh 'npm audit'
                        sh 'docker run --rm -v "$(pwd):/src" securecodewarrior/docker-slim analyze /src'
                    }
                }
            }
        }
        
        stage('Build') {
            steps {
                script {
                    def image = docker.build("${DOCKER_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}")
                    docker.withRegistry("https://${DOCKER_REGISTRY}", 'docker-registry-credentials') {
                        image.push()
                        image.push('latest')
                    }
                }
            }
        }
        
        stage('Deploy to Staging') {
            steps {
                sh '''
                    helm upgrade --install ${IMAGE_NAME}-staging ./helm-chart \
                        --set image.tag=${BUILD_NUMBER} \
                        --set environment=staging \
                        --namespace staging
                '''
            }
        }
        
        stage('Integration Tests') {
            steps {
                sh 'npm run test:integration'
            }
        }
        
        stage('Deploy to Production') {
            when {
                branch 'main'
            }
            steps {
                input message: 'Deploy to production?', ok: 'Deploy'
                sh '''
                    helm upgrade --install ${IMAGE_NAME} ./helm-chart \
                        --set image.tag=${BUILD_NUMBER} \
                        --set environment=production \
                        --namespace production
                '''
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        failure {
            emailext (
                subject: "Pipeline Failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
                body: "Build failed. Check console output at ${env.BUILD_URL}",
                to: "${env.CHANGE_AUTHOR_EMAIL}"
            )
        }
    }
}
```

## 🚀 部署策略详解

### 蓝绿部署

#### 实现原理
```
Production (Green) ← 用户流量
    ↓
Load Balancer
    ↓
Staging (Blue) ← 新版本部署

部署完成后切换：
Production (Blue) ← 用户流量  
    ↓
Load Balancer
    ↓
Old Version (Green) ← 保留用于回滚
```

#### Kubernetes实现
```yaml
# 蓝绿部署脚本
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: blue-green-demo
spec:
  replicas: 3
  strategy:
    blueGreen:
      activeService: active-service
      previewService: preview-service
      autoPromotionEnabled: false
      scaleDownDelaySeconds: 30
      prePromotionAnalysis:
        templates:
        - templateName: success-rate
        args:
        - name: service-name
          value: preview-service
  selector:
    matchLabels:
      app: blue-green-demo
  template:
    metadata:
      labels:
        app: blue-green-demo
    spec:
      containers:
      - name: blue-green-demo
        image: nginx:1.19
        ports:
        - containerPort: 80
```

### 金丝雀发布

#### 流量分配策略
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: canary-demo
spec:
  replicas: 10
  strategy:
    canary:
      steps:
      - setWeight: 10    # 10%流量到新版本
      - pause: {duration: 10m}
      - setWeight: 30    # 30%流量到新版本
      - pause: {duration: 10m}
      - setWeight: 50    # 50%流量到新版本
      - pause: {duration: 10m}
      - setWeight: 100   # 100%流量到新版本
      trafficRouting:
        istio:
          virtualService:
            name: canary-vs
            routes:
            - primary
      analysis:
        templates:
        - templateName: success-rate
        - templateName: latency
        startingStep: 2
        interval: 30s
```

## 📊 监控与可观测性

### 关键指标监控

#### 流水线指标
- **构建时间**: 构建耗时趋势
- **成功率**: 构建成功/失败比例
- **部署频率**: 部署到生产的频率
- **故障恢复时间**: MTTR (Mean Time To Recovery)

#### 应用指标
- **部署成功率**: 部署成功的百分比
- **回滚频率**: 需要回滚的部署比例
- **服务可用性**: SLA/SLO指标
- **性能指标**: 响应时间、错误率

### 监控实现

#### Prometheus + Grafana
```yaml
# 流水线监控配置
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: jenkins-monitor
spec:
  selector:
    matchLabels:
      app: jenkins
  endpoints:
  - port: http
    path: /prometheus
    interval: 30s
```

## 🔒 安全最佳实践

### 密钥管理
1. **环境变量**: 敏感信息不入库
2. **密钥轮换**: 定期更新密钥
3. **权限最小化**: 按需分配权限
4. **审计日志**: 记录所有操作

### 安全扫描集成
```yaml
# 安全扫描示例
- name: Security Scan
  steps:
    - name: SAST (Static Analysis)
      run: |
        sonar-scanner \
          -Dsonar.projectKey=${{ github.repository }} \
          -Dsonar.sources=. \
          -Dsonar.host.url=${{ secrets.SONAR_HOST_URL }} \
          -Dsonar.login=${{ secrets.SONAR_TOKEN }}
    
    - name: Dependency Check
      run: |
        dependency-check.sh \
          --project "MyProject" \
          --scan . \
          --format JSON \
          --out reports/
    
    - name: Container Scan
      run: |
        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
          -v $PWD:/tmp/.cache/ aquasec/trivy \
          image myapp:latest
```

## 📝 面试重点

### CI/CD理论
1. **CI/CD概念**: 持续集成、持续部署的区别和联系
2. **流水线设计**: 阶段划分、并行执行、失败处理
3. **部署策略**: 蓝绿部署、金丝雀发布的优缺点
4. **测试策略**: 测试金字塔、测试左移

### 工具实践
1. **Jenkins**: Pipeline as Code、插件生态
2. **GitHub Actions**: Workflow语法、市场Actions
3. **Docker**: 容器化构建、多阶段构建
4. **Kubernetes**: 容器编排、滚动更新

### 问题解决
1. **流水线优化**: 构建时间优化、并行化
2. **故障排查**: 日志分析、问题定位
3. **安全考虑**: 密钥管理、漏洞扫描
4. **监控告警**: 指标监控、异常处理

## 📖 学习资源

### 经典书籍
1. **《持续交付》** - Jez Humble
2. **《DevOps实践指南》** - Gene Kim
3. **《凤凰项目》** - Gene Kim

### 在线资源
1. [Jenkins官方文档](https://www.jenkins.io/doc/) - Jenkins权威指南
2. [GitHub Actions文档](https://docs.github.com/actions) - GitHub Actions完整文档
3. [GitLab CI/CD](https://docs.gitlab.com/ee/ci/) - GitLab CI/CD指南

### 实践平台
1. [Play with Docker](https://labs.play-with-docker.com/) - Docker实验环境
2. [Katacoda](https://www.katacoda.com/) - 在线学习平台
3. [GitLab.com](https://gitlab.com/) - 免费CI/CD环境

---

CI/CD是现代软件开发的基础设施，掌握这些技能将大大提升您的工程效率和系统质量。 