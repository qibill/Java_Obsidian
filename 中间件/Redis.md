---
tags:
  - 中间件
---
![image](https://mmbiz.qpic.cn/mmbiz_png/gB9Yvac5K3OSCEIQBtroLfFiaMMWzJpyxoZ9Qog3MIZmVibbhozjUdiaAvXZzBQwecWskzfluZ0FWQoiaPDI69NGNw/640?wx_fmt=png\&tp=webp\&wxfrom=5\&wx_lazy=1\&wx_co=1)

redis有几种数据类型

- String字符类型

- map散列类型

- list列表类型

- set集合类型

- sortedset有序集合类型

都分别适合干啥
为啥适合
redis的事务

redis的永久缓存策略有几种
1. rdb
2. aof

都有啥区别


### redis的应用场景
缓存（数据查询、短连接、新闻内容、商品内容等等）。（最多使用）

分布式集群架构中的session分离。

聊天室的在线好友列表。

任务队列。（秒杀、抢购、12306等等）

应用排行榜。

网站访问统计。

数据过期处理（可以精确到毫秒）


### 秒杀系统

使用最简单的，kv结构，用原子类型做key，userid做value，商品库存数量就是原子变量的最大值。每个秒杀，使用rpush key value插入秒杀请求，当插入的秒杀请求数达到上限，停止后续插入。

后台启动多个线程，使用lpop key读取秒杀成功的userid，然后再操作db做最终的下订单减库存

1. 确定一个基准时间。可以使用一个sql语句从数据库中取出一个当前时间。SELECT NOW();
2. 活动开始的时间是固定的。
3. 使用活动开始时间-基准时间=一个秒为单位的数值；
4. 在Redis中设置一个key(活动开始标识)。设置key的过期时间为第三步计算出来的时间。
5. 展示页面的时间取出key的有效时间。ttl命令。使用计算倒计时。
6. 一旦活动开始的key失效，说明活动开始。
7. 需要在活动的逻辑中，先判断活动是否开始。

### 倒计时方案

1. 确定一个基准时间。可以使用一个sql语句从数据库中取出一个当前时间。SELECT NOW();
2. 活动开始的时间是固定的。
3. 使用活动开始时间-基准时间=一个秒为单位的数值；
4. 在Redis中设置一个key(活动开始标识)。设置key的过期时间为第三步计算出来的时间。
5. 展示页面的时间取出key的有效时间。ttl命令。使用计算倒计时。
6. 一旦活动开始的key失效，说明活动开始。
7. 需要在活动的逻辑中，先判断活动是否开始。

### 通过代码监测redis连接池里面  每个连接的状态是否正常
连接池配置:testOnBorrow, testWhileIdle