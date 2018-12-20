# Redis
Redis是一个开源的、基于内存的数据结构存储器，可以用作数据库、缓存和消息中间件。
Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。

### 数据类型
Redis支持五种数据类型：string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。
Redis是一个key-value存储系统。物种数据类型是指value的类型。
String:
hash:是一个string类型的field和value的映射表
list:简单的字符串列表
set:String 类型的无序集合
zset(sorted set):string类型元素的集合,且不允许重复的成员。

### Redis 在 Java Web 中的应用
Redis 在 Java Web 主要有两个应用场景：
* 存储 缓存 用的数据
* 需要高速读/写的场合使用它快速读/写

**缓存**
读：
![缓存读取逻辑](https://pic1.zhimg.com/80/v2-59e6bc0e759f911af90ef2ce04580fc8_hd.jpg "缓存读取逻辑")
写：
![写逻辑](https://pic4.zhimg.com/80/v2-2a22f9f9a052e58f844cd93653f06daf_hd.jpg "写逻辑")

**高速读/写的场合**
在某一个瞬间或者是某一个短暂的时刻有成千上万的请求到达服务器，如果单纯的使用数据库来进行处理，就算不崩，也会很慢的，所以使用Redis。
1. 当一个请求到达服务器时，只是把业务数据在 Redis 上进行读写，而没有对数据库进行任何的操作，这样就能大大提高读写的速度，从而满足高速响应的需求；
2. 但是这些缓存的数据仍然需要持久化，也就是存入数据库之中，所以在一个请求操作完 Redis 的读/写之后，会去判断该高速读/写的业务是否结束，这个判断通常会在秒杀商品为0，红包金额为0时成立，如果不成立，则不会操作数据库；如果成立，则触发事件将 Redis 的缓存的数据以批量的形式一次性写入数据库，从而完成持久化的工作。
![高并发Redis](https://pic3.zhimg.com/80/v2-98f72d397b021a7dd56b9be8af9d77a6_hd.jpg "高并发Redis")

**Spring Boot整合Redis**
1. 引入依赖
![](/引入依赖.png "")
2. application.properties配置
![](/Redis配置.png )
3. 测试代码
将对象转换成Json字符串存入Redis，在将取出的Redis使用工具类转成对象，还是使用的String类型
![](/Redis测试代码.png )
4. 工具类
