## 列表（List） ##

Redis 列表是简单的字符串列表，按照插入顺序排序，你可以添加一个元素到表的头部（左边）或尾部（右边）。

一个链表最多可以包含 2 的 23次方 减一个元素（每个列表超过40亿个元素）。

例：

    reids 127.0.0.1:6397 > LPUSH runoobkey reids
	(integer) 1
	redis 127.0.0.1:6397 > LPUSH runoobkey mongobd
	(integer) 2
	redis 127.0.0.1:6397 > LPUSH runoobkey mysql
	(integer) 3
	redis 127.0.0.1:6397 > LRANGE runoobkey 0 10

	1) "mysql"
	2) "mongodb"
	3) "redis"

Redis 链表命令

- BLPOP key1[key2]timeout : 移出并获得列表的第一个元素，如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
- BRPOP key1[key2]timeout : 移出并获得列表的最后一个元素，如果列表没有元素将会阻塞列表直到等待超时或发现可弹出元素为止。
- BRPOPLPUSH score destination timeout ： 从列表中弹出一个值，将弹出的元素插入到另一个列表中并返回她；如果列表没有元素会阻塞列表知道等待超时或发现可弹出元素为止。
- LINDEX key index : 通过索引获取列表的元素。
- LINSERT key BEFORE|AFTER pivot value : 在列表的元素前或者后插入元素。
- LLINE key : 获取列表长度
- LPOP key : 移出并获取列表的长度
- LPUSH key value1[value2] : 将一个或多个值插入到列表的头部。
- LPUSHX key value : 将一个值插入到已存在的列表头部。
- LRANGE key start stop : 获取列表指定范围的元素。
- LREM key start stop :获取列表指定范围元素。
- LSET key index value : 通过索引设置列表元素的值。
- LTRIM key start stop: 对一个列表进行修剪（trim），就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
- RPOP key : 移除列表的最后一个元素，返回值为移除的元素。
- RPOPLPUSH source destination ： 移除列表的最后一个元素，并将该元素添加到另一个列表并返回。
- RPUSH key value1[value2] : 在列表中添加一个或多个值。
- RPUSHX key value  : 为已存在的列表添加至。