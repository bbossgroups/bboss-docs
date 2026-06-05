# Redis分布式锁使用文档

## 一、概述

bboss-data 模块基于 Redis 提供了简单易用的分布式锁实现，支持通过 `RedisTool` 和 `RedisHelper` 两种方式操作分布式锁。分布式锁可用于在分布式环境下对共享资源进行互斥访问，防止并发冲突。

## 二、API 说明

### 2.1 获取分布式锁

```java
public boolean tryLock(String lockKey, String lockValue, int expireTime)
```

| 参数 | 类型 | 说明 |
|------|------|------|
| lockKey | String | 锁的唯一标识key |
| lockValue | String | 锁的拥有者标识，用于安全释放锁 |
| expireTime | int | 锁的过期时间，单位：**毫秒** |

**返回值**：`true` 表示获取锁成功，`false` 表示获取锁失败（锁已被其他客户端持有）。

### 2.2 释放分布式锁

```java
public boolean releaseLock(String lockKey, String lockValue)
```

| 参数 | 类型 | 说明 |
|------|------|------|
| lockKey | String | 锁的唯一标识key |
| lockValue | String | 锁的拥有者标识，必须与获取时一致才能释放 |

**返回值**：`true` 表示释放成功，`false` 表示释放失败（通常是因为锁已过期或不是当前客户端持有）。

## 三、使用方式

### 3.1 通过 RedisTool 使用（推荐）

`RedisTool` 为单例模式，使用简单，自动管理 Redis 连接。

```java
import org.frameworkset.nosql.redis.RedisTool;

public class BusinessService {
    
    public void doBusiness() {
        String lockKey = "biz:order:10001";
        String lockValue = "server_node_01";
        int expireTime = 30000; // 30秒
        
        boolean acquired = RedisTool.getInstance().tryLock(lockKey, lockValue, expireTime);
        if (!acquired) {
            System.out.println("获取锁失败，资源正被其他节点处理");
            return;
        }
        
        try {
            // 执行业务逻辑
            processOrder();
        } finally {
            // 确保锁被释放
            boolean released = RedisTool.getInstance().releaseLock(lockKey, lockValue);
            System.out.println("锁释放结果: " + released);
        }
    }
    
    private void processOrder() {
        // 业务处理
    }
}
```

### 3.2 通过 RedisHelper 使用

`RedisHelper` 需要手动获取和释放连接，适合需要灵活控制连接生命周期的场景。

```java
import org.frameworkset.nosql.redis.RedisFactory;
import org.frameworkset.nosql.redis.RedisHelper;

public class BusinessService {
    
    public void doBusiness() {
        String lockKey = "biz:order:10002";
        String lockValue = "server_node_02";
        int expireTime = 30000;
        
        RedisHelper redisHelper = null;
        boolean acquired = false;
        try {
            redisHelper = RedisFactory.getRedisHelper();
            acquired = redisHelper.tryLock(lockKey, lockValue, expireTime);
            
            if (!acquired) {
                System.out.println("获取锁失败");
                return;
            }
            
            // 执行业务逻辑
            processOrder();
            
        } finally {
            if (acquired) {
                redisHelper.releaseLock(lockKey, lockValue);
            }
            if (redisHelper != null) {
                redisHelper.release();
            }
        }
    }
    
    private void processOrder() {
        // 业务处理
    }
}
```

## 四、核心特性

### 4.1 互斥性

同一时刻，只有一个客户端能持有某个锁。当客户端A持有锁时，客户端B尝试获取会返回 `false`。

### 4.2 防死锁（自动过期）

锁支持设置过期时间（expireTime，单位毫秒）。如果客户端在持有锁期间崩溃或网络中断，锁会在过期时间后自动释放，避免死锁。

```java
// 设置5秒过期时间
boolean acquired = RedisTool.getInstance().tryLock("my:lock", "owner", 5000);
```

### 4.3 安全释放（持有者验证）

释放锁时必须提供与获取时相同的 `lockValue`。只有锁的持有者才能释放锁，防止误释放他人持有的锁。

```java
// 客户端A获取锁
RedisTool.getInstance().tryLock("my:lock", "owner_A", 5000);

// 客户端B尝试释放（失败，因为lockValue不匹配）
RedisTool.getInstance().releaseLock("my:lock", "owner_B"); // 返回 false

// 客户端A正确释放（成功）
RedisTool.getInstance().releaseLock("my:lock", "owner_A"); // 返回 true
```

## 五、最佳实践

### 5.1 锁的过期时间设置

过期时间应根据业务执行时间合理设置：
- **过短**：业务未执行完锁已过期，导致并发安全问题
- **过长**：客户端异常后锁长时间不释放，影响其他客户端

建议设置比业务最大执行时间略长（如业务最大10秒，设置15-20秒），并结合业务幂等性设计。

### 5.2 lockValue 的生成

`lockValue` 应保证全局唯一，建议使用以下策略：
- 服务器IP + 线程ID + 时间戳
- UUID
- 业务流水号

```java
String lockValue = UUID.randomUUID().toString();
```

### 5.3 确保锁最终释放

务必在 `finally` 块中释放锁，防止业务异常导致锁无法释放（虽然有过期时间兜底，但及时释放更高效）：

```java
boolean acquired = RedisTool.getInstance().tryLock(lockKey, lockValue, expireTime);
if (acquired) {
    try {
        // 业务逻辑
    } finally {
        RedisTool.getInstance().releaseLock(lockKey, lockValue);
    }
}
```

### 5.4 锁的粒度

锁的粒度应尽可能小，减少竞争：
- **推荐**：按业务ID加锁，如 `"order:lock:" + orderId`
- **避免**：全局唯一锁，如 `"global:lock"`

### 5.5 获取锁失败的处理策略

获取锁失败时，可根据业务场景选择：
- **直接返回**：适用于非核心操作
- **重试机制**：间隔一定时间后重试
- **降级处理**：执行降级逻辑

```java
// 重试获取锁示例
    /* @param lockKey 锁的key
    * @param lockValue 锁的值，用于标识锁的拥有者
    * @param expireTime 过期时间(毫秒)
    * @param retryTimes 重试次数
    * @param retryInterval 重试间隔(毫秒) 大于0 起作用            
     */
boolean acquired = RedisTool.getInstance().tryLockWithRetry(lockKey, lockValue, expireTime,retryTimes,retryInterval);
```



## 六、注意事项

1. **过期时间单位**：`expireTime` 参数单位为**毫秒**，不是秒
2. **锁续约**：当前实现不支持自动续约，如果业务执行时间可能超过过期时间，需在业务代码中自行续约或设置足够长的过期时间
3. **时钟漂移**：分布式锁依赖Redis服务器时钟，极端情况下可能受时钟漂移影响
4. **RedLock增强**：如需更高可靠性（多Redis实例场景），可考虑基于RedLock算法的实现

## 七、测试用例参考

参见 `bboss-data/test/org/frameworkset/nosql/RedisTest.java`，包含以下测试场景：

| 测试方法 | 说明 |
|----------|------|
| testDistributedLockByTool | RedisTool方式获取/释放锁 |
| testDistributedLockByHelper | RedisHelper方式获取/释放锁 |
| testDistributedLockTimeout | 锁超时自动释放场景 |
| testDistributedLockCompetition | 多客户端竞争锁场景 |
| testReleaseOthersLock | 安全释放验证（防止误释放他人锁） |
