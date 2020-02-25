## 字符串(String) ##

**语法**

    redis 127.0.0.1:6397> COMMAND KEY_NAME

实例

    redsi 127.0.0.1:6397> SET runoobeky redis
	OK
	redis 127.0.0.1:6397> GET runoobkey
	"redis"

**Redis字符串命令**

- SET key value : 设置指定 key 的值。
- GET key : 获取指定key的值。
- GETRANGE key start end : 返回 key 中字符串值的子字符。
- GETSET key value : 将给的的 key 的值设为 value ，并返回 key 的旧值（old value）。
- GETBIT key offset : 对 key 所储存的的字符串值，获取指定偏移量上的位（bit）。
- MGET key1[key2..] : 获取所有（一个或多个）给定 key 值。
- SETBIT key offset value : 对 key 所储存的字符串值，设置或清除指定偏移量的位（bit）。
- SETTNX key value : 只有在 key 不存在时设置 key 的值。
- STRLEN key : 返回 key 储存的字符串值的长度。
- MSET key value [key value...] : 同时设置一个或多个key-value 对，当且仅当所有给定的key都不存在。
- MSETNX key milliseconds value : 这个命令和 SETEX 命令相似，但它以毫秒为单位设置 key的生存时间,而不是像 SETEX 命令那样，以秒为单位。
- PSETEX key milliseconds value : 这个命令和SETEX 命令类似，单他以毫秒为单位设置key的生成时间，而不像 SETEX 命令那样，以秒为单位。
- INCR key ： 将 key中储存的数字值增加1。
- INCRBY key increment : 将 key所储存的值加上给定的增量值（increment）。
- INCREBYFLOAT key increment : 将 key 所存储的值加上给定的 浮点增量值（increment）。
- DECR key ： 将 key 中储存的数字值减一。
- DECRBY key decrement : key 所储存的值减去给定的减量值（decrement）。
- APPEND key value : 如果 key 已经存在并且是一个字符串，APPEND 命令指定的value 追加到该 key 原来的值（value）的末尾。