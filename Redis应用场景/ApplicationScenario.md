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