# 数据库技术 - 总览

## 概述

数据库技术是现代应用系统的核心基础设施，负责数据的持久化存储、查询和管理。随着数据规模的快速增长和应用场景的多样化，数据库技术也在不断演进。本章节涵盖了关系型数据库、NoSQL数据库、分布式数据库等核心技术。

## 📚 章节内容

### 1. [关系型数据库](./relational-databases.md)
- **MySQL**: 开源关系型数据库
  - InnoDB存储引擎深度解析
  - 查询优化与索引设计
  - 主从复制与集群部署
  - 性能调优与监控

- **PostgreSQL**: 高级开源数据库
  - 先进的SQL特性支持
  - 扩展性与自定义类型
  - 全文搜索与JSON支持
  - 高可用架构设计

- **Oracle**: 企业级数据库
  - 企业特性与高级功能
  - 性能优化技术
  - 备份恢复策略
  - RAC集群架构

### 2. [NoSQL数据库](./nosql-databases.md)
- **MongoDB**: 文档型数据库
  - 文档模型与数据建模
  - 查询语言与聚合框架
  - 分片与副本集
  - 性能优化技巧

- **Redis**: 内存型数据库
  - 数据结构与应用场景
  - 持久化机制对比
  - 集群模式与高可用
  - 缓存设计模式

- **Cassandra**: 列族数据库
  - 分布式架构设计
  - 数据一致性模型
  - 分区与复制策略
  - 性能调优技术

### 3. [数据库设计与优化](./database-design.md)
- **数据建模**: ER模型、范式理论、反范式设计
- **索引设计**: B+树、哈希索引、全文索引
- **查询优化**: 执行计划、统计信息、查询重写
- **事务管理**: ACID特性、隔离级别、锁机制
- **性能监控**: 慢查询分析、性能指标监控

### 4. [分布式数据库](./distributed-databases.md)
- **分库分表**: 水平分片、垂直分片、分片策略
- **数据一致性**: CAP定理、BASE理论、最终一致性
- **分布式事务**: 2PC、3PC、Saga模式
- **NewSQL数据库**: TiDB、CockroachDB、Spanner
- **数据同步**: 主从复制、多主复制、冲突解决

### 5. [数据仓库与大数据](./data-warehouse.md)
- **数据仓库设计**: 维度建模、星型模式、雪花模式
- **ETL流程**: 数据抽取、转换、加载
- **OLAP技术**: 多维分析、数据立方体
- **大数据技术**: Hadoop、Spark、Flink
- **实时数据处理**: 流式计算、实时分析

## 🎯 数据库类型对比

### 按数据模型分类

| 类型 | 代表产品 | 优势 | 适用场景 |
|------|---------|------|---------|
| **关系型** | MySQL, PostgreSQL | ACID保证、SQL标准、成熟生态 | 传统业务系统、金融交易 |
| **文档型** | MongoDB, CouchDB | 灵活模式、嵌套结构、快速开发 | 内容管理、产品目录 |
| **键值型** | Redis, DynamoDB | 极简模型、高性能、线性扩展 | 缓存、会话存储 |
| **列族型** | Cassandra, HBase | 列式存储、大数据量、高写入 | 时序数据、日志分析 |
| **图数据库** | Neo4j, ArangoDB | 关系查询、图算法、深度遍历 | 社交网络、推荐系统 |

### 性能特性对比

| 特性 | 关系型数据库 | NoSQL数据库 |
|------|-------------|-------------|
| **一致性** | 强一致性 | 最终一致性 |
| **扩展性** | 垂直扩展为主 | 水平扩展优势 |
| **查询能力** | 复杂SQL查询 | 简单查询为主 |
| **事务支持** | 完整ACID | 有限事务支持 |
| **性能** | 中等，依赖优化 | 高性能读写 |
| **运维复杂度** | 相对简单 | 较为复杂 |

## 💡 数据库选型指南

### 业务场景考虑因素

#### 1. 数据特征
- **数据结构**: 结构化 vs 半结构化 vs 非结构化
- **数据量级**: TB级 vs PB级 vs EB级
- **增长速度**: 稳定增长 vs 爆炸性增长
- **数据关系**: 简单关联 vs 复杂关系

#### 2. 访问模式
- **读写比例**: 读多写少 vs 写多读少 vs 读写均衡
- **查询复杂度**: 简单查询 vs 复杂关联 vs 分析查询
- **并发要求**: 低并发 vs 高并发 vs 超高并发
- **响应时间**: 毫秒级 vs 秒级 vs 分钟级

#### 3. 一致性要求
- **强一致性**: 金融交易、库存管理
- **最终一致性**: 社交媒体、内容分发
- **弱一致性**: 监控数据、日志分析

### 典型选型方案

#### 电商系统
```
用户信息、订单数据 → MySQL (强一致性)
商品目录、评论 → MongoDB (灵活文档结构)
购物车、会话 → Redis (高性能缓存)
搜索功能 → Elasticsearch (全文搜索)
推荐系统 → Neo4j (图关系分析)
```

#### 社交媒体
```
用户关系 → Neo4j (复杂关系网络)
动态内容 → MongoDB (非结构化内容)
实时消息 → Redis (低延迟)
时间线数据 → Cassandra (时序数据)
分析数据 → ClickHouse (OLAP分析)
```

## 🔧 数据库性能优化

### 查询优化策略

#### 1. 索引优化
```sql
-- 单列索引
CREATE INDEX idx_user_email ON users(email);

-- 复合索引
CREATE INDEX idx_order_date_status ON orders(order_date, status);

-- 部分索引（PostgreSQL）
CREATE INDEX idx_active_users ON users(email) WHERE status = 'active';

-- 函数索引
CREATE INDEX idx_user_lower_email ON users(LOWER(email));
```

#### 2. 查询重写
```sql
-- 避免SELECT *
SELECT id, name, email FROM users WHERE active = 1;

-- 使用EXISTS代替IN
SELECT * FROM orders o 
WHERE EXISTS (SELECT 1 FROM order_items oi WHERE oi.order_id = o.id);

-- 使用LIMIT避免全表扫描
SELECT * FROM large_table ORDER BY created_at DESC LIMIT 10;
```

#### 3. 分页优化
```sql
-- 传统分页（性能随页数增加而下降）
SELECT * FROM posts ORDER BY id LIMIT 1000, 20;

-- 游标分页（性能稳定）
SELECT * FROM posts WHERE id > 12345 ORDER BY id LIMIT 20;
```

### 数据库架构优化

#### 1. 读写分离
```
应用层 → 主库（写操作）
       → 从库1（读操作）
       → 从库2（读操作）
```

#### 2. 分库分表
```
-- 垂直分库
user_db: users, profiles, settings
order_db: orders, order_items, payments

-- 水平分表
users_0, users_1, users_2, users_3
根据user_id % 4分表
```

#### 3. 缓存策略
```
应用 → Redis缓存 → 数据库
     ↓
   Cache Miss时更新缓存
```

## 📊 监控与运维

### 关键性能指标

#### 系统级指标
- **CPU使用率**: 数据库服务器负载
- **内存使用**: 缓冲池命中率
- **磁盘I/O**: IOPS、延迟、吞吐量
- **网络**: 连接数、带宽使用

#### 数据库级指标
- **QPS/TPS**: 每秒查询/事务数
- **慢查询**: 执行时间超过阈值的查询
- **锁等待**: 锁竞争和等待时间
- **连接数**: 活跃连接和最大连接数

### 监控工具推荐

#### 开源工具
- **Prometheus + Grafana**: 指标收集与可视化
- **MySQL Enterprise Monitor**: MySQL官方监控
- **pgAdmin**: PostgreSQL管理工具
- **MongoDB Ops Manager**: MongoDB集群管理

#### 云服务监控
- **AWS CloudWatch**: RDS监控
- **Azure Monitor**: Azure数据库监控
- **阿里云DMS**: 数据库管理服务

## 🔒 数据库安全

### 安全威胁与防护

#### 1. SQL注入防护
```java
// 错误示例 - SQL注入风险
String sql = "SELECT * FROM users WHERE email = '" + userInput + "'";

// 正确示例 - 使用预编译语句
String sql = "SELECT * FROM users WHERE email = ?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setString(1, userEmail);
```

#### 2. 访问控制
```sql
-- 创建只读用户
CREATE USER 'readonly'@'%' IDENTIFIED BY 'password';
GRANT SELECT ON database.* TO 'readonly'@'%';

-- 创建应用用户（限制权限）
CREATE USER 'app_user'@'%' IDENTIFIED BY 'password';
GRANT SELECT, INSERT, UPDATE ON app_db.* TO 'app_user'@'%';
```

#### 3. 数据加密
- **传输加密**: SSL/TLS连接
- **存储加密**: 透明数据加密(TDE)
- **字段级加密**: 敏感字段加密存储

### 数据备份策略

#### 备份类型
- **全量备份**: 完整数据备份
- **增量备份**: 变化数据备份
- **日志备份**: 事务日志备份

#### 备份方案设计
```
每日全量备份 + 每小时增量备份 + 实时日志备份
保留策略：日备份30天，周备份12周，月备份12个月
```

## 📝 面试重点

### 数据库理论
1. **ACID特性**: 原子性、一致性、隔离性、持久性
2. **隔离级别**: 读未提交、读已提交、可重复读、串行化
3. **范式理论**: 第一范式到第五范式
4. **CAP定理**: 一致性、可用性、分区容错性

### 实际应用
1. **索引设计**: 何时创建索引，如何选择索引类型
2. **查询优化**: 如何分析和优化慢查询
3. **分库分表**: 拆分策略和实现方案
4. **高可用**: 主从复制、集群部署

### 问题解决
1. **数据库锁**: 死锁检测和解决
2. **性能瓶颈**: 定位和解决性能问题
3. **数据一致性**: 分布式环境下的一致性保证
4. **容量规划**: 存储和性能容量评估

## 📖 学习资源

### 经典教材
1. **《数据库系统概念》** - Abraham Silberschatz
2. **《高性能MySQL》** - Baron Schwartz
3. **《PostgreSQL技术内幕》** - 深入理解PostgreSQL
4. **《Redis设计与实现》** - 黄健宏
5. **《MongoDB权威指南》** - Kristina Chodorow

### 在线资源
1. [MySQL官方文档](https://dev.mysql.com/doc/) - MySQL权威文档
2. [PostgreSQL文档](https://www.postgresql.org/docs/) - PostgreSQL官方文档
3. [Redis命令参考](https://redis.io/commands) - Redis命令手册
4. [MongoDB大学](https://university.mongodb.com/) - MongoDB官方培训
5. [Database Systems](http://db-book.com/) - 数据库系统教程

### 实践平台
1. [MySQL Tutorial](https://www.mysqltutorial.org/) - MySQL实践教程
2. [PostgreSQL Tutorial](https://www.postgresqltutorial.com/) - PostgreSQL学习
3. [Redis Labs](https://redislabs.com/redis-enterprise/) - Redis云服务
4. [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) - MongoDB云数据库

---

通过系统学习数据库技术，您将具备设计高性能、高可用数据存储系统的能力，这是现代应用开发的核心技能之一。 