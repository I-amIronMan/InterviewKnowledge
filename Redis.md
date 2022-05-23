### 分布式缓存常见的技术选型方案有哪些？
Memcached和Redis
#### Redis 和 Memcached 的区别和共同点
共同点：
+ 都是基于内存的数据库，一般都用来当做缓存使用。
+ 都有过期策略。
+ 两者的性能都非常高。

区别：
+ Redis 支持更丰富的数据类型（支持更复杂的应用场景）。Redis 不仅仅支持简单的 k/v 类型的数据，同时还提供 list，set，zset，hash 等数据结构的存储。Memcached 只支持最简单的 k/v 数据类型。
+ Redis 支持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用,而 Memcached 把数据全部存在内存之中。
+ Redis 有灾难恢复机制。 因为可以把缓存中的数据持久化到磁盘上。
+ Redis 在服务器内存使用完之后，可以将不用的数据放到磁盘上。但是，Memcached 在服务器内存使用完之后，就会直接报异常。
+ Memcached 没有原生的集群模式，需要依靠客户端来实现往集群中分片写入数据；但是 Redis 目前是原生支持 cluster 模式的。
+ Memcached 是多线程，非阻塞 IO 复用的网络模型；Redis 使用单线程的多路 IO 复用模型。 （Redis 6.0 引入了多线程 IO ）
+ Redis 支持发布订阅模型、Lua 脚本、事务等功能，而 Memcached 不支持。并且，Redis 支持更多的编程语言。
+ Memcached 过期数据的删除策略只用了惰性删除，而 Redis 同时使用了惰性删除与定期删除。
#### Redis 除了做缓存，还能做什么？
+ 分布式锁（Redisson）
+ 限流（Redis+Lua脚本）
+ 消息队列（Stream数据结构）
+ 复杂业务场景

#### Redis常见数据结构
+ string
+ list
+ hash
+ set
+ sorted set
+ bitmap
#### Redis的过期策略
设置过期时间除了有助于缓解内存的消耗。  
+ 如何判断过期？
Redis 通过一个叫做过期字典（可以看作是 hash 表）来保存数据过期的时间。过期字典的键指向 Redis 数据库中的某个 key(键)，过期字典的值是一个 long long 类型的整数，这个整数保存了 key 所指向的数据库键的过期时间（毫秒精度的 UNIX 时间戳）。
+ 过期的数据的删除策略
1. 惰性删除 ：只会在取出 key 的时候才对数据进行过期检查。这样对 CPU 最友好，但是可能会造成太多过期 key 没有被删除。
2. 定期删除 ： 每隔一段时间抽取一批 key 执行删除过期 key 操作。并且，Redis 底层会通过限制删除操作执行的时长和频率来减少删除操作对 CPU 时间的影响。
#### 内存淘汰机制
+ volatile-lru
+ volatile-ttl
+ volatile-random
+ allkeys-lru
+ allkeys-random
+ no-eviction
#### Redis持久化机制
快照和追加文件
+ 快照持久化
Redis 可以通过创建快照来获得存储在内存里面的数据在某个时间点上的副本。Redis 创建快照之后，可以对快照进行备份，可以将快照复制到其他服务器从而创建具有相同数据的服务器副本（Redis 主从结构，主要用来提高 Redis 性能），还可以将快照留在原地以便重启服务器的时候使用。
+ AOF持久化
#### Redis事务
1. 开始事务（MULTI）。
2. 命令入队(批量操作 Redis 的命令，先进先出（FIFO）的顺序执行)。
3. 执行事务(EXEC)。