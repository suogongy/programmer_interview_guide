# 高可用与性能优化 - 总览

## 概述

高可用性和性能优化是大规模系统设计的核心要求，直接影响用户体验和业务连续性。本章节深入探讨系统可用性设计、负载均衡策略、缓存系统、数据库优化等关键技术，帮助构建稳定高效的生产系统。

## 📚 章节内容

### 1. [系统可用性设计](./availability-design.md)
- **可用性指标**: SLA、SLO、SLI定义和计算方法
- **故障恢复**: 熔断器、降级策略、超时重试机制
- **容灾备份**: 多地域部署、数据备份、灾难恢复预案
- **监控告警**: 实时监控、异常检测、告警通知机制
- **故障演练**: 混沌工程、故障注入、应急响应流程

### 2. [负载均衡架构](./load-balancing.md)
- **负载均衡算法**: 轮询、加权轮询、最少连接、一致性哈希
- **四层负载均衡**: TCP/UDP层负载分发、网络层优化
- **七层负载均衡**: HTTP/HTTPS应用层负载分发
- **健康检查**: 主动探测、被动检测、故障节点隔离
- **会话保持**: 粘性会话、会话复制、无状态设计

### 3. [缓存系统设计](./caching-strategies.md)
- **缓存模式**: Cache-Aside、Write-Through、Write-Behind
- **分布式缓存**: Redis、Memcached集群配置和优化
- **缓存失效**: TTL策略、LRU算法、缓存穿透预防
- **一致性保证**: 缓存与数据库同步、最终一致性
- **性能调优**: 内存管理、网络优化、持久化配置

### 4. [数据库性能优化](./database-optimization.md)
- **查询优化**: 索引设计、SQL调优、执行计划分析
- **读写分离**: 主从复制、读写路由、数据一致性
- **分库分表**: 垂直拆分、水平分片、分布式事务
- **连接池管理**: 连接复用、连接数优化、超时配置
- **NoSQL优化**: 文档数据库、键值存储、列族存储调优

### 5. [微服务性能调优](./microservice-optimization.md)
- **服务网格**: Istio、Linkerd流量管理和观测
- **API网关**: 限流、熔断、认证、路由策略
- **分布式追踪**: Jaeger、Zipkin链路追踪分析
- **异步处理**: 消息队列、事件驱动、流式处理
- **资源优化**: 容器资源配置、JVM调优、内存管理

## 🎯 系统可用性设计

### 可用性等级定义

#### SLA服务等级协议
| 可用性 | 年度停机时间 | 月度停机时间 | 周停机时间 | 应用场景 |
|--------|--------------|--------------|------------|----------|
| **99%** | 3.65天 | 7.31小时 | 1.68小时 | 内部系统 |
| **99.9%** | 8.77小时 | 43.83分钟 | 10.08分钟 | 一般业务 |
| **99.99%** | 52.6分钟 | 4.38分钟 | 1.01分钟 | 关键业务 |
| **99.999%** | 5.26分钟 | 26.3秒 | 6.05秒 | 核心系统 |

### 高可用架构模式

#### 多层级容错设计
```
应用层容错:
• 熔断器模式 - 快速失败保护
• 重试机制 - 指数退避重试
• 超时控制 - 防止资源耗尽
• 降级策略 - 核心功能保障

数据层容错:
• 主从复制 - 数据备份
• 读写分离 - 负载分担
• 分片集群 - 水平扩展
• 异地备份 - 灾难恢复

基础设施容错:
• 多可用区 - 机房级容错
• 负载均衡 - 流量分发
• 自动扩缩 - 弹性伸缩
• 健康检查 - 故障检测
```

### 熔断器实现

#### Netflix Hystrix模式
```java
@Component
public class UserService {
    
    private final CircuitBreaker circuitBreaker;
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
        this.circuitBreaker = CircuitBreaker.ofDefaults("userService");
        
        // 配置熔断器
        circuitBreaker.getEventPublisher()
            .onStateTransition(event -> 
                logger.info("Circuit breaker state transition: {}", event));
    }
    
    public User getUserById(Long id) {
        return circuitBreaker.executeSupplier(() -> {
            // 可能失败的远程调用
            return userRepository.findById(id)
                .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
        });
    }
    
    // 降级方法
    public User getUserByIdFallback(Long id) {
        return User.builder()
            .id(id)
            .name("Default User")
            .email("default@example.com")
            .build();
    }
}
```

## 💡 负载均衡实践

### Nginx负载均衡配置

#### 多种负载均衡算法
```nginx
# upstream配置
upstream backend_servers {
    # 轮询（默认）
    server 192.168.1.10:8080;
    server 192.168.1.11:8080;
    server 192.168.1.12:8080;
    
    # 加权轮询
    # server 192.168.1.10:8080 weight=3;
    # server 192.168.1.11:8080 weight=2;
    # server 192.168.1.12:8080 weight=1;
    
    # 最少连接
    # least_conn;
    
    # IP哈希
    # ip_hash;
    
    # 健康检查
    keepalive 32;
}

server {
    listen 80;
    server_name api.example.com;
    
    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # 超时配置
        proxy_connect_timeout 30s;
        proxy_send_timeout 30s;
        proxy_read_timeout 30s;
        
        # 失败重试
        proxy_next_upstream error timeout invalid_header http_500 http_502 http_503;
        proxy_next_upstream_tries 3;
        proxy_next_upstream_timeout 10s;
    }
    
    # 健康检查端点
    location /health {
        access_log off;
        return 200 "healthy\n";
    }
}
```

### 应用层负载均衡

#### Spring Cloud LoadBalancer
```java
@Configuration
@LoadBalancerClient(name = "user-service", configuration = CustomLoadBalancerConfiguration.class)
public class LoadBalancerConfig {
}

@Configuration
public class CustomLoadBalancerConfiguration {
    
    @Bean
    @Primary
    public ReactorLoadBalancer<ServiceInstance> customLoadBalancer(
            Environment environment,
            LoadBalancerClientFactory loadBalancerClientFactory) {
        
        String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);
        return new RoundRobinLoadBalancer(
            loadBalancerClientFactory.getLazyProvider(name, ServiceInstanceListSupplier.class),
            name);
    }
}

@Service
public class UserServiceClient {
    
    @Autowired
    private LoadBalancerExchangeFilterFunction lbFunction;
    
    private final WebClient webClient;
    
    public UserServiceClient() {
        this.webClient = WebClient.builder()
            .filter(lbFunction)
            .build();
    }
    
    public Mono<User> getUser(Long id) {
        return webClient.get()
            .uri("http://user-service/users/{id}", id)
            .retrieve()
            .bodyToMono(User.class)
            .timeout(Duration.ofSeconds(5))
            .retry(3)
            .onErrorReturn(createDefaultUser(id));
    }
}
```

## 🛠️ 缓存系统设计

### Redis缓存优化

#### 缓存模式实现
```java
@Service
public class UserCacheService {
    
    @Autowired
    private RedisTemplate<String, Object> redisTemplate;
    
    @Autowired
    private UserRepository userRepository;
    
    private static final String USER_CACHE_KEY = "user:";
    private static final int CACHE_TTL = 3600; // 1小时
    
    // Cache-Aside模式
    public User getUserById(Long id) {
        String cacheKey = USER_CACHE_KEY + id;
        
        // 1. 先查缓存
        User cachedUser = (User) redisTemplate.opsForValue().get(cacheKey);
        if (cachedUser != null) {
            return cachedUser;
        }
        
        // 2. 缓存未命中，查数据库
        User user = userRepository.findById(id).orElse(null);
        if (user != null) {
            // 3. 写入缓存
            redisTemplate.opsForValue().set(cacheKey, user, CACHE_TTL, TimeUnit.SECONDS);
        }
        
        return user;
    }
    
    // Write-Through模式
    @Transactional
    public User updateUser(User user) {
        // 1. 更新数据库
        User updatedUser = userRepository.save(user);
        
        // 2. 同步更新缓存
        String cacheKey = USER_CACHE_KEY + user.getId();
        redisTemplate.opsForValue().set(cacheKey, updatedUser, CACHE_TTL, TimeUnit.SECONDS);
        
        return updatedUser;
    }
    
    // 缓存预热
    @PostConstruct
    public void warmUpCache() {
        List<User> activeUsers = userRepository.findActiveUsers();
        for (User user : activeUsers) {
            String cacheKey = USER_CACHE_KEY + user.getId();
            redisTemplate.opsForValue().set(cacheKey, user, CACHE_TTL, TimeUnit.SECONDS);
        }
    }
    
    // 批量失效
    public void invalidateUserCache(List<Long> userIds) {
        List<String> keys = userIds.stream()
            .map(id -> USER_CACHE_KEY + id)
            .collect(Collectors.toList());
        redisTemplate.delete(keys);
    }
}
```

### 分布式缓存集群

#### Redis Cluster配置
```yaml
# redis-cluster.conf
port 7000
cluster-enabled yes
cluster-config-file nodes-7000.conf
cluster-node-timeout 15000
appendonly yes
appendfilename "appendonly-7000.aof"
cluster-require-full-coverage no

# 内存优化
maxmemory 2gb
maxmemory-policy allkeys-lru

# 持久化配置
save 900 1
save 300 10
save 60 10000

# 网络优化
tcp-keepalive 300
timeout 0
```

## 📊 数据库性能优化

### MySQL优化实践

#### 查询优化示例
```sql
-- 索引优化
CREATE INDEX idx_user_email_status ON users(email, status);
CREATE INDEX idx_order_user_date ON orders(user_id, created_date);

-- 复合索引使用
EXPLAIN SELECT * FROM users 
WHERE email = 'user@example.com' 
AND status = 'active';

-- 分页优化（避免深度分页）
-- 传统分页（性能差）
SELECT * FROM orders ORDER BY id LIMIT 100000, 20;

-- 优化后分页（使用ID范围）
SELECT * FROM orders WHERE id > 100000 ORDER BY id LIMIT 20;

-- 子查询优化
-- 原始查询
SELECT * FROM users u 
WHERE u.id IN (
    SELECT DISTINCT o.user_id 
    FROM orders o 
    WHERE o.total > 1000
);

-- 优化后查询
SELECT DISTINCT u.* FROM users u
INNER JOIN orders o ON u.id = o.user_id
WHERE o.total > 1000;
```

#### 连接池配置
```java
@Configuration
public class DatabaseConfig {
    
    @Bean
    @Primary
    public DataSource primaryDataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/app");
        config.setUsername("app_user");
        config.setPassword("password");
        
        // 连接池配置
        config.setMaximumPoolSize(20);  // 最大连接数
        config.setMinimumIdle(5);       // 最小空闲连接
        config.setConnectionTimeout(30000);  // 连接超时
        config.setIdleTimeout(600000);       // 空闲超时
        config.setMaxLifetime(1800000);      // 连接最大生命周期
        
        // 性能优化
        config.setLeakDetectionThreshold(60000);
        config.addDataSourceProperty("cachePrepStmts", "true");
        config.addDataSourceProperty("prepStmtCacheSize", "250");
        config.addDataSourceProperty("prepStmtCacheSqlLimit", "2048");
        
        return new HikariDataSource(config);
    }
}
```

### 读写分离架构

#### 动态数据源切换
```java
@Component
public class DynamicDataSourceAspect {
    
    @Around("@annotation(readOnly)")
    public Object around(ProceedingJoinPoint point, ReadOnly readOnly) throws Throwable {
        try {
            // 设置为只读数据源
            DataSourceContextHolder.setDataSourceType(DataSourceType.SLAVE);
            return point.proceed();
        } finally {
            // 清除数据源设置
            DataSourceContextHolder.clearDataSourceType();
        }
    }
}

@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface ReadOnly {
}

public class DataSourceContextHolder {
    private static final ThreadLocal<DataSourceType> contextHolder = new ThreadLocal<>();
    
    public static void setDataSourceType(DataSourceType dataSourceType) {
        contextHolder.set(dataSourceType);
    }
    
    public static DataSourceType getDataSourceType() {
        return contextHolder.get();
    }
    
    public static void clearDataSourceType() {
        contextHolder.remove();
    }
}
```

## 🚀 性能监控与调优

### APM监控系统

#### Micrometer指标收集
```java
@Component
public class MetricsCollector {
    
    private final MeterRegistry meterRegistry;
    private final Counter requestCounter;
    private final Timer responseTimer;
    private final Gauge activeConnections;
    
    public MetricsCollector(MeterRegistry meterRegistry) {
        this.meterRegistry = meterRegistry;
        this.requestCounter = Counter.builder("api.requests.total")
            .description("Total API requests")
            .register(meterRegistry);
        
        this.responseTimer = Timer.builder("api.response.time")
            .description("API response time")
            .register(meterRegistry);
        
        this.activeConnections = Gauge.builder("db.connections.active")
            .description("Active database connections")
            .register(meterRegistry, this, MetricsCollector::getActiveConnections);
    }
    
    public void recordRequest(String endpoint, String method) {
        requestCounter.increment(
            Tags.of(
                Tag.of("endpoint", endpoint),
                Tag.of("method", method)
            )
        );
    }
    
    public Timer.Sample startTimer() {
        return Timer.start(meterRegistry);
    }
    
    public void recordResponseTime(Timer.Sample sample, String endpoint, int status) {
        sample.stop(Timer.builder("api.response.time")
            .tags("endpoint", endpoint, "status", String.valueOf(status))
            .register(meterRegistry));
    }
    
    private double getActiveConnections() {
        // 实际实现中从连接池获取活跃连接数
        return 10.0;
    }
}
```

### 性能调优清单
```
应用层优化:
✓ JVM参数调优（堆内存、GC策略）
✓ 线程池配置优化
✓ 连接池参数调整
✓ 缓存命中率优化

数据库优化:
✓ 慢查询分析和优化
✓ 索引设计和维护
✓ 连接数和事务优化
✓ 分库分表策略

网络优化:
✓ HTTP/2协议升级
✓ gzip压缩开启
✓ CDN加速配置
✓ 长连接复用

监控体系:
✓ 实时性能监控
✓ 异常告警配置
✓ 容量规划预警
✓ 用户体验监控
``` 