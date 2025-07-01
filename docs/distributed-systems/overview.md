# 分布式系统 - 总览

## 概述

分布式系统是现代大规模互联网应用的基础架构，涉及CAP定理、一致性协议、分布式事务等核心概念。掌握分布式系统设计是高级工程师必备技能，也是系统设计面试的重点考查内容。

## 📚 章节内容

### 1. [CAP定理与一致性模型](./cap-consistency.md)
- **CAP定理**: 一致性、可用性、分区容错性
- **BASE理论**: 基本可用、软状态、最终一致性
- **一致性级别**: 强一致性、弱一致性、最终一致性
- **Quorum机制**: 读写仲裁、副本管理

### 2. [分布式锁与协调](./distributed-coordination.md)
- **分布式锁**: Zookeeper、Redis、etcd实现
- **共识算法**: Raft、Paxos协议详解
- **选主算法**: Bully算法、Ring算法
- **分布式协调**: 服务发现、配置管理

### 3. [服务发现与注册](./service-discovery.md)
- **服务注册**: Consul、Eureka、Nacos
- **负载均衡**: 客户端、服务端负载均衡
- **健康检查**: 心跳机制、故障检测
- **服务网格**: Istio、Linkerd架构

### 4. [分布式事务](./distributed-transactions.md)
- **两阶段提交**: 2PC协议、事务协调器
- **三阶段提交**: 3PC改进、超时处理
- **Saga模式**: 补偿事务、事务编排
- **TCC模式**: Try-Confirm-Cancel

### 5. [微服务架构设计](./microservices-architecture.md)
- **服务拆分**: 领域驱动设计、康威定律
- **API网关**: 路由、认证、限流、熔断
- **服务间通信**: REST、gRPC、消息队列
- **数据一致性**: 最终一致性、CQRS模式

## 🎯 核心概念深入

### CAP定理实践
```
一致性(Consistency): 所有节点同时看到相同数据
可用性(Availability): 系统持续提供服务
分区容错性(Partition tolerance): 网络分区时系统仍可运行

实际系统选择:
- CP系统: Zookeeper、HBase
- AP系统: Cassandra、DynamoDB  
- CA系统: RDBMS(单机场景)
```

### 一致性协议对比

| 协议 | 容错能力 | 消息复杂度 | 适用场景 |
|------|---------|-----------|---------|
| **Raft** | f < n/2 | O(n) | 强一致性、配置管理 |
| **Paxos** | f < n/2 | O(n²) | 理论基础、复杂场景 |
| **PBFT** | f < n/3 | O(n²) | 拜占庭容错、区块链 |
| **Gossip** | 高容错 | O(log n) | 最终一致性、P2P |

## 📝 面试重点

### 分布式系统理论
1. **CAP定理**: 理解权衡、实际选择策略
2. **一致性模型**: 强一致性vs最终一致性应用场景
3. **共识算法**: Raft选举过程、日志复制机制
4. **分布式事务**: 2PC问题、Saga补偿机制

### 实际系统设计
1. **服务拆分**: 如何划分微服务边界
2. **数据一致性**: 跨服务数据同步策略
3. **故障处理**: 故障检测、自动恢复机制
4. **性能优化**: 缓存、异步处理、批量操作

### 工程实践
1. **监控告警**: 分布式链路追踪、指标监控
2. **部署运维**: 蓝绿部署、灰度发布
3. **容量规划**: 服务扩容、数据分片策略
4. **安全考虑**: 服务间认证、数据加密

## 🛠️ 技术栈选择

### 服务发现
- **Consul**: 健康检查、KV存储、DNS接口
- **Eureka**: Netflix开源、客户端发现
- **Zookeeper**: 强一致性、配置中心
- **etcd**: CoreOS开源、Kubernetes使用

### 消息队列
- **Kafka**: 高吞吐、持久化、分区
- **RabbitMQ**: 可靠性、路由灵活
- **Apache Pulsar**: 多租户、地理复制
- **Redis Streams**: 轻量级、内存存储

### 分布式存储
- **Cassandra**: 最终一致性、线性扩展
- **MongoDB**: 文档存储、副本集
- **TiDB**: ACID事务、MySQL协议
- **CockroachDB**: 全球分布、强一致性

## 📖 学习资源

### 经典教材
1. **《设计数据密集型应用》** - Martin Kleppmann
2. **《分布式系统概念与设计》** - George Coulouris
3. **《微服务架构设计模式》** - Chris Richardson

### 论文推荐
1. **CAP Theorem** - Eric Brewer
2. **Raft Consensus Algorithm** - Diego Ongaro
3. **Amazon Dynamo** - Giuseppe DeCandia
4. **Google MapReduce** - Jeffrey Dean

---

分布式系统是复杂的技术领域，需要理论与实践相结合，通过系统学习为高级技术岗位做好准备。 