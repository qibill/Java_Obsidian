---
tags:
  - 中间件
---
![image](https://mmbiz.qpic.cn/mmbiz_png/gB9Yvac5K3OSCEIQBtroLfFiaMMWzJpyxoZ9Qog3MIZmVibbhozjUdiaAvXZzBQwecWskzfluZ0FWQoiaPDI69NGNw/640?wx_fmt=png\&tp=webp\&wxfrom=5\&wx_lazy=1\&wx_co=1)

### 1. 数据类型

- String字符类型
	常用命令： set,get,decr,incr,mget 等。

- map散列类型
	常用命令： hget,hset,hgetall 等

- list列表类型
	常用命令： lpush,rpush,lpop,rpop,lrange 等
	可以通过 lrange 命令，就是从某个元素开始读取多少个元素，可以基于 list 实现分页查询。

- set集合类型
	常用命令： sadd,spop,smembers,sunion 等

- sortedset有序集合类型
	常用命令： zadd,zrange,zrem,zcard 等

### 2. Redis 如何实现 key 的过期删除？

定期删除和惰性删除的形式。

*   定期删除 Redis 每隔一段时间从设置过期时间的 key 集合中，随机抽取一些 key ，检查是否过期，如果已经过期做删除处理。
*   惰性删除 Redis 在 key 被访问的时候检查 key 是否过期，如果过期则删除。

### 3. Redis 的持久化机制

数据快照（RDB）+ 修改数据语句文件（AOF）

### 4. 如何解决 Redis 缓存雪崩和缓存穿透？

*   缓存雪崩 缓存同一时间大面积的失效，所以，后面的请求都会落到数据库上，造成数据库短时间内承受大量请求而崩掉。
    *   解决方式
        *   事前：保证 Redis 集群的稳定性，发现机器宕机尽快补上，设置合适的内存淘汰策略。
        *   事中：本地缓存 + 限流降级，避免大量请求落在数据库上。
        *   事后：利用 Redis 持久化机制尽快恢复缓存。

*   缓存穿透 一般是黑客故意去请求缓存中不存在的数据，导致所有的请求都落到数据库上，造成数据库短时间内承受大量请求而崩掉。
    *   解决方式 将不存在的数据列举到一个足够大的 map 上，这样遭到攻击时，直接拦截 map 中的请求，请求到数据库上面。或是把不存在的也做缓存，值为 null ，设置过期时间。

### 5. 如何使用 Redis 实现消息队列？

Redis 实现消息队列依赖于 Redis 集群的稳定性，通常不建议使用。

*   Redis 自带发布订阅功能，基于 publish 和 subscribe 命令。
*   使用 List 存储消息，lpush，rpop 分别发送接收消息。### 2. Redis 如何实现 key 的过期删除？

定期删除和惰性删除的形式。

*   定期删除 Redis 每隔一段时间从设置过期时间的 key 集合中，随机抽取一些 key ，检查是否过期，如果已经过期做删除处理。
*   惰性删除 Redis 在 key 被访问的时候检查 key 是否过期，如果过期则删除。

### 3. Redis 的持久化机制

数据快照（RDB）+ 修改数据语句文件（AOF）

### 4. 如何解决 Redis 缓存雪崩和缓存穿透？

*   缓存雪崩 缓存同一时间大面积的失效，所以，后面的请求都会落到数据库上，造成数据库短时间内承受大量请求而崩掉。
    *   解决方式
        *   事前：保证 Redis 集群的稳定性，发现机器宕机尽快补上，设置合适的内存淘汰策略。
        *   事中：本地缓存 + 限流降级，避免大量请求落在数据库上。
        *   事后：利用 Redis 持久化机制尽快恢复缓存。

*   缓存穿透 一般是黑客故意去请求缓存中不存在的数据，导致所有的请求都落到数据库上，造成数据库短时间内承受大量请求而崩掉。
    *   解决方式 将不存在的数据列举到一个足够大的 map 上，这样遭到攻击时，直接拦截 map 中的请求，请求到数据库上面。或是把不存在的也做缓存，值为 null ，设置过期时间。

### 5. 如何使用 Redis 实现消息队列？

Redis 实现消息队列依赖于 Redis 集群的稳定性，通常不建议使用。

*   Redis 自带发布订阅功能，基于 publish 和 subscribe 命令。
*   使用 List 存储消息，lpush，rpop 分别发送接收消息。

### 6. redis的事务

### 7 redis的应用场景
- 缓存（数据查询、短连接、新闻内容、商品内容等等）。（最多使用）
- 分布式集群架构中的session分离。
- 聊天室的在线好友列表。
- 任务队列。（秒杀、抢购、12306等等）
- 应用排行榜。
- 网站访问统计。
- 数据过期处理（可以精确到毫秒）

#### 秒杀系统

使用最简单的，kv结构，用原子类型做key，userid做value，商品库存数量就是原子变量的最大值。每个秒杀，使用rpush key value插入秒杀请求，当插入的秒杀请求数达到上限，停止后续插入。

后台启动多个线程，使用lpop key读取秒杀成功的userid，然后再操作db做最终的下订单减库存

1. 确定一个基准时间。可以使用一个sql语句从数据库中取出一个当前时间。SELECT NOW();
2. 活动开始的时间是固定的。
3. 使用活动开始时间-基准时间=一个秒为单位的数值；
4. 在Redis中设置一个key(活动开始标识)。设置key的过期时间为第三步计算出来的时间。
5. 展示页面的时间取出key的有效时间。ttl命令。使用计算倒计时。
6. 一旦活动开始的key失效，说明活动开始。
7. 需要在活动的逻辑中，先判断活动是否开始。

#### 倒计时方案

1. 确定一个基准时间。可以使用一个sql语句从数据库中取出一个当前时间。SELECT NOW();
2. 活动开始的时间是固定的。
3. 使用活动开始时间-基准时间=一个秒为单位的数值；
4. 在Redis中设置一个key(活动开始标识)。设置key的过期时间为第三步计算出来的时间。
5. 展示页面的时间取出key的有效时间。ttl命令。使用计算倒计时。
6. 一旦活动开始的key失效，说明活动开始。
7. 需要在活动的逻辑中，先判断活动是否开始。

### 通过代码监测redis连接池里面  每个连接的状态是否正常
连接池配置:testOnBorrow, testWhileIdle