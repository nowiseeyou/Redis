## 哈希（Hash） ##

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。
Redis 中的每个hash 可以存储 2 的32次方减一 键值对(40多亿)。

例：

    127.0.0.1:6397> HMSET runoobkey name "redis tutorial" description "redis basic commands for caching" like 20 visitors 2300
	ok
	127.0.0.1:6397> HGETALL runoobkey
	1) "name"
	2) "redis tutorial"
	3) "description"
	4) "redis basic commands for caching"
	5) "likes"
	6) "20"
	7) "visitors"
	8) "23000"

在以上实例中，我们设置了redis 的一些描述信息（name, description,likes,visitors）到哈希表的 runoobkey中。

Redis hash 命令。

- HDEL key field1[field2] : 删除一个或多个哈希表字段。
- HEAXISTS key field : 查看哈希表 key 中，指定的字段是否存在。
- HGET key field : 获取在哈希表中指定字段的值。
- HGETALL key : 获取在哈希表中指定 key 的所有指端和值。
- HINCRBY key field increment : 为哈希表 key 中的指定字段的整数值加上增量 increment。
- HKEYS key : 获取所有哈希表中的字段。
- HLEN key : 获取哈希表中字段的数量。
- HMGET key field1[field2] : 获取所有给定字段的值。
- HMSET key field1 value1[field2 value2] : 同时将多个 field-value(域-值)对设置到哈希表 key 中。
- HSET key field value : 将哈希表 key 中的字段 field 的值设为 value。
- HSETNX key field value : 只有在字段 field 不存在时，设置哈希表字段的值。
- HVALS key : 获取哈希表的中所有值。
- HSCAN key cursor[MATCH pattern][COUNT count] ： 迭代哈希表中的键值对。