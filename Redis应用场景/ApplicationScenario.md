	https://juejin.im/post/5d8882c8f265da03951a325e


## string ##

 **string** 是redis 最基本的类型，可以理解成与 Memcached 一模一样的类型，一个 key对应一个 value。 value其实不仅是 String，也可以是数字，string 类型是二进制安全的。意思是Redis的 string 可以包含任何数据，比如 jpg 图片或序列化的对象。 string 类型是 Redis最基本的数据类型，string类型的值最大能存储 512MB。

#### 常用命令 ####

**get  set  incr  decr  mget** 等

#### 应用场景 ####

**String** 是最常用的一种数据类型，普通的 key/value 存储都可以归为此类，既可以完全实现目前 Memcached 的功能，并且效率更高。还可以享受 Redis 的定时持久化，操作日志及 Replication 等功能。除了提供与 Memcached 一样的 **get set incr decr** 等操作外，Redis还提供了下面一些操作：

- 获取字符串长度
- 往字符串 append 内容
- 设置和获取字符串的某一段内容
- 设置及获取字符串的某一位（bit）
- 批量设置一系列字符串的内容

#### 使用场景 ####

常规 key-value 缓存应用，常规计数：微博数，粉丝数。
**实现方式：**  String 在 Redis 内部存储默认就是一个字符串，被 redisObject 所引用，当遇到 **incr,decr** 等操作时会转成数值型进行计算，此时 redisObject的 encoding 字段为 int。

**注意：** 一个键最大能存储 512MB


## Hash ##

**Redis Hash** 存储用户信息，Key 作为用户 uid ，value 是一个 Map ，这个 Map 的 Key 是成员的属性名，value 是属性值，这样对数据的修改和存取都可以直接通过内部 Map 的 key ( Redis 里称内部 Map 的 key 为 field)，也就是通过 key(uid) + field(属性标签) 就可以直接操作对应属性数据了，既不需要重复存储数据，也不会带来序列化和并发修改控制的问题。

#### 常用命令 ####

**hset hget hmset hmget**

#### 使用场景 ####

存储部分变更数据，如用户信息等。

#### 实现方式 ####

**Redis Hash** 对应 Value 内部实际就是一个 HashMap ， 实际这里会有两种不同实现，这个Hash 的成员比较少时 Redis 为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的 HashMap 结构，对应的 value redisObject 的 encoding 为zipmap，当成员数量增大时会自动转成真正的 HashMap ， 此时 encoding 为 ht。


**注意：** Redis 提供了接口（hgetall） 可以直接取到全部的接口数据，但是如果内部 Map 的成员很多，那么涉及到了遍历整个内部 Map 的操作，由于 Redis 单线程模型的缘故，这个遍历操作可能会比较耗时，而另其他客户端的请求完全不响应，这点格外需要注意。 每个 hash 可以存储 2^32 - 1 键值对（40多亿）


## List ##

list 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表头部（左边）或者 尾部（右边）。

#### 常用命令 ####

- lpush - 添加左边元素
- rpush - 添加右边元素
- lpop - 移除左边第一个元素
- rpop - 移除右边第一个元素
- lrange - 获取列表片段 ( LRANGE key start stop)

#### 应用场景 ####

**Redis list** 应用场景非常多，也是 Redis 最重要的数据结构之一，比如 twitter 的关注列表，粉丝列表等都可以用 Redis 的 list 结构来实现。

**List** 就是链表，使用 List 结构，我们可以轻松地实现最新消息排行等功能，List 的另一个应用就是**消息队列** ，可以利用 List 的PUSH 操作，将任务存在 List中，然后工作线程再用 POP 操作将任务取出进行执行。Redis 还提供了操作 List 某一段的 api，你可以直接查询，删除 List 中某一段的元素。

#### 实现方式 ####

**Redis List** 的实现为一个双向链表，既可以支持反向查找和遍历，更方便操作，不过带来了部分额外的内存开销，Redis内部的很多实现，包括发送缓冲队列等也都是用的这个数据结构。

**Redis List** 是每个子元素都是 String 类型的双向链表，可以通过 push 和 pop 操作从列表的头部或者尾部添加或者删除元素，这样 list 既可以作为栈，也可以作为队列。获取越接近两端的元素速度越快，但通过索引访问时比较慢。

#### 使用场景 ####

**消息队列系统**：使用 List 可以构建队列系统，使用 sorted set 甚至可以构建有优先级的队列系统。比如将 Redis 用作日志收集器，实际上还是一个队列，多个端点将日志信息写入 Redis，然后一个worker 统一将所有日志写到磁盘。

取最新 N 个数据的操作： 记录前 N 个最新登录的用户 ID 列表，超出的范围可以从数据库中获得。
