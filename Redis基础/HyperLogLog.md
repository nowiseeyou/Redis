## Redis HyperLogLog ##

Redis HyperLogLog 是用来做技术统计算法，HyperLogLog 的优点是，在输入元素的数量或体积非常非常大时，计算技术所需的空间总是固定的，并且很小的。

在 Redis 里面，每个HyperLogLog键只需要话费 12KB 内存，就可以计算将近2^64 个不同元素的技术，这和计算技术时，元素越多耗费内存就越多的集合形成鲜明对比。

但是，因为HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所有HyperLogLog 不能像集合那样，范湖输入的各个元素。

**什么是基数**

比如数据集 {1,3,5,7,5,7,8}，那么这个数据集的基数集为{1，3,5,7,8}，基数（不重复元素）为5 ，基数估计就是在误差可接受的范围内，快速计算基数。

**例**
以下演示了HyperLogLog 的工作过程 ：

    redis 127.0.0.1:6397 > PFADD runoobkey "redis"
	1) (integer) 1
	redis 127.0.0.1:6397 > PFADD runoobkey "mongodb"
	1) (integer) 1
	redis 127.0.0.1:6397 > PFADD runoobkey "mysql"
	1) (integer) 1
	redis 127.0.0.1:6397 > PFCOUNT runoobkey
	
	(integer) 3


## Redis HyperLogLog 命令 ##

- PFADD key element[element...] ： 添加指定元素到HyperLogLog 中。
- PFCOUNT key[key...] ： 返回给定HyperLogLog 的技术估算值。
- PFMERGE destkey sourcekey [sourcekey...] ： 将多个HyperLogLog合并为一个HyperLogLog