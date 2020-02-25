## 集合（Set） ##

Redis 的 Set是String 类型的无序集合，集合成员是唯一的，这意味着集合中不能出现重复的数据。

Redis 中集合是通过哈希表来实现的，所以添加，删除，查找的复杂度都是 O(1)。

集合中最大的成员数是 2 的23次方减一 ，（每个集合可存储40多亿个成员）。

例：

    redis 127.0.0.1:6397 > SADD runoobkey redis
	(integer) 1
	redis 127.0.0.1:6397 > SADD runoobkey mongodb
	(integer) 2
	redis 127.0.0.1:6397 > SADD runoobkey mysql
	(integer) 3
	reids 127.0.0.1:6397 > SADD runoobkey mysql
	(integer) 0
	redis 127.0.0.1:6397 > SMEMBERS runoobkey

	1) "mysql"
	2) "mongodb"
	3) "redis"

## 集合命令 ##

- SADD key member1[memeber2] ： 向集合添加一个或多个元素。
- SCARD key ： 获取集合的成员数。
- SDIFF key1[key2] ： 返回给定集合的差集。
- SDIFFSTORE destination key1[key2] ： 返回给定集合的差集并存储在destination 中。
- SINTER key1[key2] ： 返回给定所有集合的交集
- SINTERSOTER destination key1[key1] ： 返回给定所有集合的交集并存储在 destination 中 。
- SISMEMBER key member ： 判断 member 元素是否是集合 key的成员。
- SMEMBERS key ： 返回集合中的所有成员。
- SMOVE source destination memeber ： 将 member 元素是否是集合key的成员。
- SPOP key ： 移除并返回 集合中的一个随机元素。
- SRANDMEMBER key [count] ： 返回集合中一个或多个随机数。
- SREM key memeber1[member2]  ： 移除集合中一个或多个成员。
- SUNION key1[key2] ： 返回所有给定集合的并集。
- SUNIONSTORE destination key1[key2]  ： 所有给定集合的并集存储在 destination 集合中。
- SSCAN key cursor[MATCH pattern][COUNT count] : 迭代集合中的元素。
