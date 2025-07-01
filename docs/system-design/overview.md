# 系统设计基础 - 总览

## 概述

系统设计是构建大规模分布式系统的核心技能，是高级程序员面试的重要考查内容。本章节涵盖了现代互联网系统设计的核心概念、原则和最佳实践。

## 📚 章节内容

### 1. [可扩展性设计原则 (Scalability Principles)](./scalability.md)
- 水平扩展 vs 垂直扩展
- 无状态设计与状态管理
- 服务拆分与微服务架构
- 性能指标与容量规划

### 2. [负载均衡与反向代理 (Load Balancing)](./load-balancing.md)
- 负载均衡算法（轮询、权重、最少连接）
- 七层与四层负载均衡
- 反向代理的作用与实现
- 健康检查与故障转移

### 3. [缓存策略 (Caching Strategies)](./caching.md)
- 缓存模式（Cache-aside、Write-through、Write-back）
- 本地缓存 vs 分布式缓存
- Redis与Memcached对比
- 缓存雪崩、穿透、击穿问题

### 4. [数据库设计与优化 (Database Design)](./database-design.md)
- 数据库分片（水平分片、垂直分片）
- 读写分离与主从复制
- ACID特性与事务管理
- NoSQL数据库选择

### 5. [消息队列与异步处理 (Message Queues)](./message-queues.md)
- 消息队列的作用与优势
- 发布-订阅模式 vs 点对点模式
- Kafka、RabbitMQ、Redis等对比
- 消息可靠性与幂等性

### 6. [微服务架构 (Microservices)](./microservices.md)
- 微服务拆分原则
- 服务间通信（REST、RPC、消息队列）
- API网关与服务发现
- 分布式事务处理

### 7. [数据存储与管理 (Data Storage)](./data-storage.md)
- 关系型数据库设计
- NoSQL数据库选型
- 数据一致性模型
- 备份与灾难恢复

### 8. [性能优化 (Performance Optimization)](./performance.md)
- 系统性能指标（延迟、吞吐量、QPS）
- 性能瓶颈识别与分析
- 数据库查询优化
- 系统监控与调优

## 🎯 系统设计核心原则

### 1. 可扩展性 (Scalability)
- **水平扩展**: 增加更多服务器来处理负载
- **垂直扩展**: 增强单台服务器的硬件配置
- **弹性扩展**: 根据负载自动调整资源

### 2. 可靠性 (Reliability)
- **容错设计**: 系统部分故障时仍能正常运行
- **冗余备份**: 关键组件的多副本部署
- **故障隔离**: 防止局部故障影响整个系统

### 3. 可用性 (Availability)
- **高可用**: 系统持续提供服务的能力
- **服务等级协议**: SLA定义的可用性目标
- **故障恢复**: 快速检测和恢复故障

### 4. 一致性 (Consistency)
- **强一致性**: 所有节点同时看到相同数据
- **最终一致性**: 系统最终达到一致状态
- **弱一致性**: 允许短期数据不一致

## 🏗️ 系统设计步骤

### 第1步：需求澄清 (Requirements Clarification)
1. **功能需求**: 系统需要提供哪些功能？
2. **非功能需求**: 性能、可用性、一致性要求
3. **规模估算**: 用户数量、数据量、QPS等
4. **约束条件**: 技术栈、预算、时间限制

### 第2步：系统容量估算 (Capacity Estimation)
```
用户数量：1亿注册用户，1000万日活用户
QPS估算：1000万 / (24 * 60 * 60) ≈ 116 QPS
峰值QPS：116 * 3 = 348 QPS
存储需求：假设每用户1KB数据，共需要100GB存储
带宽需求：假设平均响应100KB，需要34.8MB/s带宽
```

### 第3步：系统接口设计 (System API Design)
```
POST /api/users          创建用户
GET /api/users/{id}      获取用户信息
PUT /api/users/{id}      更新用户信息
DELETE /api/users/{id}   删除用户
GET /api/users/{id}/posts 获取用户的帖子
```

### 第4步：数据模型设计 (Data Model Design)
```sql
-- 用户表
CREATE TABLE users (
    id BIGINT PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 帖子表
CREATE TABLE posts (
    id BIGINT PRIMARY KEY,
    user_id BIGINT REFERENCES users(id),
    content TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 第5步：高层设计 (High-Level Design)
```
[客户端] -> [负载均衡器] -> [Web服务器] -> [应用服务器] -> [数据库]
                                    |
                                    v
                                [缓存层]
```

### 第6步：详细设计 (Detailed Design)
- 具体组件的实现细节
- 数据库分片策略
- 缓存策略
- 安全性考虑

### 第7步：扩展性讨论 (Scalability Discussion)
- 识别瓶颈
- 扩展策略
- 监控和运维

## 📊 系统设计权衡

### CAP定理
在分布式系统中，以下三个特性最多只能同时满足两个：
- **一致性 (Consistency)**: 所有节点同时看到相同数据
- **可用性 (Availability)**: 系统持续提供服务
- **分区容错性 (Partition Tolerance)**: 系统在网络分区时仍能运行

### 常见权衡
| 场景 | 选择 | 说明 |
|------|------|------|
| 银行系统 | CP | 一致性比可用性更重要 |
| 社交网络 | AP | 可用性比一致性更重要 |
| 搜索引擎 | AP | 允许短期数据不一致 |

## 🔧 常用技术栈

### Web层
- **Web服务器**: Nginx, Apache, IIS
- **应用服务器**: Tomcat, Jetty, Node.js
- **API网关**: Kong, Zuul, AWS API Gateway

### 应用层
- **编程语言**: Java, Python, Go, Node.js
- **框架**: Spring Boot, Django, Express
- **容器**: Docker, Kubernetes

### 数据层
- **关系型数据库**: MySQL, PostgreSQL, Oracle
- **NoSQL数据库**: MongoDB, Cassandra, DynamoDB
- **缓存**: Redis, Memcached, Hazelcast
- **搜索**: Elasticsearch, Solr

### 基础设施
- **消息队列**: Kafka, RabbitMQ, AWS SQS
- **监控**: Prometheus, Grafana, ELK Stack
- **CI/CD**: Jenkins, GitLab CI, GitHub Actions

## 🎯 面试中的系统设计

### 常见设计题目
1. **社交媒体系统**: 设计类似Twitter的系统
2. **聊天系统**: 设计WhatsApp或微信
3. **视频网站**: 设计YouTube或Netflix
4. **搜索引擎**: 设计Google搜索
5. **电商系统**: 设计Amazon或淘宝
6. **短链接服务**: 设计bit.ly
7. **网约车系统**: 设计Uber或滴滴

### 设计评估标准
1. **完整性**: 是否覆盖所有重要组件
2. **可扩展性**: 系统是否能处理大规模负载
3. **可靠性**: 系统是否具备容错能力
4. **现实性**: 设计是否符合实际工程约束
5. **清晰性**: 是否能清楚表达设计思路

### 面试技巧
1. **先问后答**: 主动澄清需求和约束
2. **从简到繁**: 先画基本架构，再深入细节
3. **数据驱动**: 用具体数字支撑设计决策
4. **权衡分析**: 主动讨论不同方案的优缺点
5. **保持互动**: 与面试官保持沟通

## 📈 系统监控指标

### 性能指标
- **延迟 (Latency)**: 请求响应时间
- **吞吐量 (Throughput)**: 每秒处理请求数
- **并发数 (Concurrency)**: 同时处理的请求数
- **错误率 (Error Rate)**: 失败请求所占比例

### 可用性指标
- **正常运行时间 (Uptime)**: 系统可用时间占比
- **平均故障间隔时间 (MTBF)**: 故障之间的平均时间
- **平均修复时间 (MTTR)**: 故障修复的平均时间

### 业务指标
- **日活跃用户 (DAU)**: 每日活跃用户数
- **月活跃用户 (MAU)**: 每月活跃用户数
- **转化率**: 关键行为的转化比例

## 🔗 学习资源

### 经典书籍
1. **《设计数据密集型应用》** - Martin Kleppmann
2. **《大规模分布式系统架构与设计实战》** - 谢启鸿
3. **《微服务设计》** - Sam Newman
4. **《高性能MySQL》** - Baron Schwartz

### 在线资源
1. [System Design Primer](https://github.com/donnemartin/system-design-primer) - 系统设计学习指南
2. [High Scalability](http://highscalability.com/) - 大规模系统案例分析
3. [AWS Architecture Center](https://aws.amazon.com/architecture/) - 云架构最佳实践
4. [Google Cloud Architecture Framework](https://cloud.google.com/architecture/framework) - 谷歌云架构指南

### 技术博客
1. [Netflix Tech Blog](https://netflixtechblog.com/) - Netflix技术团队博客
2. [Uber Engineering](https://eng.uber.com/) - Uber工程技术博客
3. [Facebook Engineering](https://engineering.fb.com/) - Facebook工程博客
4. [阿里技术](https://tech.alibaba.com/) - 阿里巴巴技术博客

## 🚀 实践项目

### 入门项目
1. **简单博客系统**: 用户、文章、评论功能
2. **在线书签**: 收藏和分类网页链接
3. **任务管理**: TODO列表应用

### 进阶项目
1. **社交媒体平台**: 关注、动态、推荐功能
2. **实时聊天**: WebSocket、消息推送
3. **文件分享**: 上传下载、权限控制

### 高级项目
1. **分布式缓存系统**: 一致性哈希、数据复制
2. **搜索引擎**: 爬虫、索引、排序算法
3. **推荐系统**: 协同过滤、机器学习

---

## 🔗 快速导航

系统设计是一个需要大量实践的技能领域。建议结合具体项目来学习各种设计模式和最佳实践，并关注业界的最新技术发展。

**下一章**: [可扩展性设计原则 (Scalability Principles)](./scalability.md) 