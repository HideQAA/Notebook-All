redis
---

**定义：**

redis （Remote Dictionary Server）远程字典服务器 是一种支持Key-Value等多种数据结构的存储系统，可用于缓存、事件发布或订阅、高速队列等场景，支持网络，提供字符串、哈希、列表、队列、集合结构直接存取，基于内存，可持久化。

**应用场景：**

* 会话缓存（最常用）
* 消息队列，比如支付
* 活动排行榜或计数
* 发布、订阅消息（消息通知）
* 商品列表、评论列表等


**数据类型：**

Redis一共支持五种数据类：string(字符串)、hash(哈希)、list(列表)、set(集合)和zset（sorted set 有序集合）

* string(字符串)

是 redis 最基本的数据类型，一个 key 对应一个 value，需要注意是一个键值最大存储512MB。

* hash(哈希)

redis hash是一个键值对的集合， 是一个string类型的field和value的映射表，适合用于存储对象。

* list(列表)

是redis简单的字符串列表，它按插入顺序排序。

* set(集合)

是string类型的无序集合，也不可重复。

* zset（sorted set 有序集合）

是string类型的有序集合，也不可重复，sorted set中的每个元素都需要指定一个分数，根据分数对元素进行升序排序，如果多个元素有相同的分数，则以字典序进行升序排序，sorted set 因此非常适合实现排名。

**redis服务相关的命令：**

* slect           #选择数据库(数据库编号0-15)
* quit             #退出连接
* info             #获得服务的信息与统计
* monitor       #实时监控
* config get   #获得服务配置
* flushdb       #删除当前选择的数据库中的key
* flushall       #删除所有数据库中的key

**redis的发布与订阅：**

redis发布与订阅(pub/sub)是它的一种消息通信模式，一方发送信息，一方接收信息。

三个客户端同时订阅同一个频道：

![](https://i.imgur.com/JFLJb0b.png)

有新信息发送给频道1时，就会将消息发送给订阅它的三个客户端：

![](https://i.imgur.com/1s0FWJs.png)

**redis持久化：**

redis持久有两种方式:Snapshotting(快照),Append-only file(AOF)

* Snapshotting(快照)：将存储在内存的数据以快照的方式写入二进制文件中，如默认dump.rdb中
 
 * save 900 1   #900秒内如果超过1个Key被修改，则启动快照保存
 
 * save 300 10  #300秒内如果超过10个Key被修改，则启动快照保存

* Append-only file(AOF)：使用AOF持久时，服务会将每个收到的写命令通过write函数追加到文件中（appendonly.aof）

 * appendonly yes  #开启AOF持久化存储方式 
 * appendfsync always  #收到写命令后就立即写入磁盘，效率最差，效果最好
 * appendfsync everysec  #每秒写入磁盘一次，效率与效果居中
 * appendfsync no   #完全依赖OS，效率最佳，效果没法保证

**redis 性能测试：**

自带性能测试。

---
###1. Redis 在 Java web 中的应用： ###

* 缓存常用的数据（读操作远大于写操作的场合）

![](https://i.imgur.com/ttOevrT.png)

* 在需要进行高速读写的场合进行快速读写（系统仅操作 Redis 缓存，没有操作数据库，请求操作全部完成后，批量写入数据库）

![](https://i.imgur.com/koj993q.png)