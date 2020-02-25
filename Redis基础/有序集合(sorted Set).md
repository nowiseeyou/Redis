## 有序集合（sorted set） ##

Redis 有序集合和集合一样也是 string 类型元素的集合，且不允许重复的成员。

不同的是每个元素都会关联一个 double 类型的分数，redis 正是通过分数来为集合中的成员进行从小到大的排序。

有序集合的成员是唯一的，但分数（score）却可以重复。

集合是通过 哈希表 来实现的，所以添加，删除，查找的复杂度都是 O(1)。集合中最大的成员数为 2的23次方 减一（每个集合可存储40多亿个成员）。

例：

    redis 127.0.0.1:6397 > ZADD runoobkey 1 redis
	(integer) 1
	redis 127.0.0.1:6397 > ZADD runoobkey 2 mongodb
	(integer) 1
	redis 127.0.0.1:6397 > ZADD runoobkey 3 mysql
	(integer) 1
	redis 127.0.0.1:6397 > ZADD runoobkey 3 mysql
	(integer) 0
	redis 127.0.0.1:6397 > ZADD runoobkey 4 mysql
	(integer) 0
	redis 127.0.0.1:6397 > ZRANGE runoobkey 0 10 WITHSOCRES

	1) "redis"
	2) "1"
	3) "mongodb"
	4) "2"
	5) "mysql"
	6) "4"


## Redis 有序集合命令 ##

- ZADD key score1 memeber1[score2 memeber2] ： 向有序集合添加一个或多个成员，或者更新已存在成员的分数。
- ZCARD key ： 获取有序结婚的成员数。
- ZCOUNT key min max ： 计算在有序集合中指定区间分数的成员数。
- ZINCRBY key increment member ： 有序集合中对指定成员的分数加上增量 increment
- ZINTERSTORE destination numkeys key[key...] ： 计算给定的一个或多个有序集的交集并将结果存储在新的有序 key 中。
- ZLEXCOUNT key min max ： 在有序集合中计算指定字典区间内成员数。
- ZRANGE key start stop [WITHSOCRES] ： 通过索引区间返回有序集合指定区间内的成员。
- ZRANGEBYLEX key min max [LIMIT offset count] ： 通过字典区间返回有序集合的成员。
- ZRANK key member ： 返回有序集合中指定成员的索引。
- ZREM key member[member...] ： 移除有序集合中的一个或多个成员。
- ZREMRANGEBYLEX key min max ： 移除有序集合中给定的字典区间的所有成员。
- ZREMRANGEBYRANK key start stop ： 移除有序集合中给定的排名区间的所有成员。
- ZREMRANGEBYSCORE key min max ： 移除有序集合给定的分数区间的所有成员。
- ZREVRANGE key start stop [WITHSCORES] ： 返回有序集中指定分数区间内的成员，分数从高到低排序。
- ZREVRANK key member ： 返回有序集合中指定成员的排名，有序集成员按分数值递减（从大到小）排序。
- ZSCORE key member ： 返回有序集中，成员的分数值。
- ZUNIONSTORE destination numkeys key[key...] ： 计算给定的一个或多个有序集的并集，并存储在新的key中。
- ZSCAN key cursor[MATCH pattern][COUNT count] ： 迭代有序集合中的元素(包括元素成员和元素分数值)。

