# Redis 高级教程 #

----------
    KEYS *:获取redis所有key


## Redis数据备份与恢复 ##

Redis SAVE 命令用于创建当前数据库的备份。

**语法 ：**

    redis 127.0.0.1:6397> SAVE

**实例 ：**

    redis 127.0.0.1:6397> SAVE
	OK

该命令将在redis 安装目录中创建 dump.rdb文件。

**恢复数据**

如果需要恢复数据，只需要将备份文件（dump.rdb） 移动到redis 安装目录并启动服务即可。获取 redis 目录可以使用 CONFIG 命令，如下所示 ： 

    redis 127.0.0.1:6397>CONFIG GET dir
	1) "dir"
	2) "/usr/local/redis/bin"

以上命令 CONFIG GET dir 输出的 redis 安装目录为 /user/local/redis/bin。

**Bgsave**

创建redis 备份文件也可以使用命令 BGSAVE ,该命令在后台执行。

**实例**

    redis 127.0.0.1:6397> BGSAVE

	Background saving started



----------



## Redis安全 ##

我们可以通过 redis 的配置文件设置密码参数，这样客户端连接到 redis 服务就需要密码验证，这样可以让你的 redis 服务更安全。

**实例**

    127.0.0.1:6397> CONFIG get requirepass
	1) "requirepass"
	2) ""
	
默认情况下 requirepass 参数是空的，这就意味着你无需通过密码验证就可以连接到 redis 服务。

你可以通过一下命令来修改该参数：

    127.0.0.1:6397> CONFIG set requirepass "aa123456"
	OK
	127.0.0.1:6397> CONFIG get requirepass
	1) "requirepass"
	2) "aa123456"

设置密码后台，客户端连接 redis 服务就需要密码验证，否则无法执行命令。

**语法**

AUTH 命令基本语法格式如下：

    127.0.0.1:6397> AUTH password

**实例**

    127.0.0.1:6397> AUTH "aa123456"
	OK
	127.0.0.1:6397> SET mykey "Test value"
	OK
	127.0.0.1:6397> GET mykey
	"Test value"





----------

## Redis性能测试 ##

Redis 性能测试是通过同时执行多个命令实现的。

**语法**

redis 性能测试的基本命令如下：

    redi-benchmark [option] [option value]

注意：该命令是在 redis 的目录下执行的，而不是 redis 客户端的内部指令。

**实例**

以下实例同时执行 10000 个请求来检测性能：

    $ redis-benchmark -n 10000 -q

	PING_INLINE: 141043.72 requests per second
	PING_BULK: 142857.14 requests per second
	SET: 141442.72 requests per second
	GET: 145348.83 requests per second
	INCR: 137362.64 requests per second
	LPUSH: 145348.83 requests per second
	LPOP: 146198.83 requests per second
	SADD: 146198.83 requests per second
	SPOP: 149253.73 requests per second
	LPUSH (needed to benchmark LRANGE): 148588.42 requests per second
	LRANGE_100 (first 100 elements): 58411.21 requests per second
	LRANGE_300 (first 300 elements): 21195.42 requests per second
	LRANGE_500 (first 450 elements): 14539.11 requests per second
	LRANGE_600 (first 600 elements): 10504.20 requests per second
	MSET (10 keys): 93283.58 requests per second


redis 性能测试工具可选参数如下所示 ： 

1. -h ： 指定服务器主机名 （默认：127.0.0.1）
2. -p ： 指定服务器端口 （默认：6397）
3. -s ： 指定服务器 socket
4. -c ： 指定并发连接数 （默认：50）
5. -n ： 指定请求数 （默认：10000）
6. -d ： 以字节的形式指定 SET/GET 值的数据大小 （默认：2）
7. -k ： 1 = keep alive 0 = reconnect (默认：1)
8. -r ： SET/GET/INCR 使用随机 key，SADD 使用随机值。
9. -P ： 通过管道传输 <numreq> 请求 （默认：1）
10. -q ： 强制退出 redis。 仅显示 query/sec 值。
11. --csv ： 以 CSV 格式输出。
12. -l ： 生成循环，永久执行测试。
13. -t ： 仅运行以逗号分隔的测试命令列表。
14. -I ： Idle 模式。仅打开 N 个 idle 连接并等待。

**实例**

以下实例 我们使用了多个参数来测试 redis的性能：

    $ redis-benchmark -h 127.0.0.1 -p 6397 -t set,lpush -n 10000 -q

	SET: 146198.83 requests per second
	LPUSH: 145560.41 requests per second

以上实例中主机：127.0.0.1，端口号：6397，执行的命令为 set，lpush ,请求数为：10000，通过 -q 参数让结果只显示每秒的请求数。




----------


## Redis 客户端连接 ##

Redis 通过监听一个 TCP 端口或者 Unix socket 的方式来接收来自客户端的链接，当一个连接建立后，Redis 内部会进行以下一些操作：

- 首先，客户端socket 会被设置为非阻塞模式，因为Redis 在网络时间处理上采用的是非阻塞多路复用模型。
- 然后为这个 socket 设置 TCP_NODELAY 属性，禁用 Nagle 算法。
- 然后创建一个可读的文件时间用于监听这个客户端的 socket 的数据发送。

**最大连接数**

在 Redis2.4中，最大连接数是被直接硬编码在代码里面的，而在2.6版本中这个值变成可配置的。

maxclients 的默认值 : 10000，你也可以在redis.config  中对这个值进行修改。

    config get maxclients 

	1)	"maxclients"
	2)	"10000"

**实例**

以下实例我们以服务启动时设置最大连接数为 100000 ：

    redis-server --maxclients 100000
	

**客户端命令**

1. CLIENT LIST ： 返回连接到 redis 服务的客户端列表 
2. CLIENT SETNAME ： 设置当前连接的名称。
3. CLIENT GETNAME ： 获取通过 CLIENT SETNAME 命令设置的服务名称
4. CLIENT PAUSE ： 挂起客户端连接，指定挂起的时间以毫秒计
5. CLIENT KILL ： 关闭客户端连接

## Redis 管道技术 ##

Redis 是一种基于客户端-服务端模型以及请求/响应协议的TCP服务。这意味着通常情况下一个请求会遵循以下步骤 ： 

- 客户端向服务端发送一个查询请求，并监听 Socket 返回，通常是以阻塞模型，等待服务端响应。
- 服务端处理命令，并将结果返回给客户端。

Redis 管道技术可以在服务端未响应时，客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应。

**实例**

查看 redis 管道，只需要启动 redis 实例并输入一下命令：

    $(echo -en "PING\r\n SET runoobkey redis\r\nGET runoobkey\r\nINCR visitor\r\nINCR visitor\r\nINCR visitor\r\n"; sleep 10)| nc localhost 6397

	+PONG
	+OK
	redis
	:1
	:2
	:3

以上实例中我们通过使用 PING 命令查看 redis 服务是否可用，之后我们设置了 runoobkey 的值为 redis，然后我们获取 runoobkey 的值并使得 visitor 自增3次。
在返回的结果中我们可以看到这些命令一次性向 redis 服务提交，并最终一次性读取所有服务端的响应。

**管道技术的优势**

管道技术最明显的优势是提高了 redis 服务的性能。

在下面的测试中，我们将使用 Redis 的 Ruby 客户端，支持管道技术特性，测试管道技术对速度的提升效果。

    require "rubygems"
	require "redis"
	def bench(descr)
	start = Time.now
	yield
	puts "#{descr} #{Time.now-start} seconds"
	end
	def without_pipelining
	r = Redis.new
	10000.times{
		r.ping
	}
	end
	def with_pipelining
	r = Redis.new
	r.pipelined {
		1000.times{
			r.ping
		}
	}
	end
	bench("without pipelining") {
		without_pipelining
	}
	bench() {
		with_pipelining
	}

从处于局域网中的Mac OS X 系统上执行上面这个简单脚本的数据表明。开启了管道操作后，往返延时已经被改善的相当低了。

    without pipelining 1.185238 seconds
	with pipelining 0.250783 seconds

如你所见，开启通道后，我们的速度效率提升了 5倍。



----------
## Redis 分区 ##

分区是分割数据到多个Redis 实例的处理过程，因此每个实例值保存 key 的一个子集。

**分区优势**

- 通过利用多台计算机内存的和值，允许我们构造更大的数据库。
- 通过多核和多台计算机，允许我们扩展计算能力；通过多台计算机和网络适配器，允许我们扩展网络宽带。

**分区不足**

redis的一些特性在分区方面表现的不是很好：

- 涉及多个 key 的操作通常是不被支持的，举例来说，当两个 set 映射到不同的 redis 实例上时，你就不能对这两个 set 执行交集操作。
- 涉及多个 key 的redis 事务不能使用。
- 当使用分区时，数据处理较为复杂，比如你需要处理多个rdb/aof文件，并且从多个实例和主机备份持久化文件。
- 增加或者删除容量也比较复杂。redis集群大多数支持在运行时增加，删除节点的透明数据平衡的能力，但是类似于客户端分区，代理等其他系统则不支持这项特性，然而，一种叫做 presharding 的技术对此是有帮助的。

**分区类型**

Redis 有两种类型的分区。假设有 4个 Redis实例 R0,R1,R2,R3，和类似 user:1,user:2这样表明用户的多个 key ，对既定的 key 有多种不同方式来选择这个 key 存放在哪个实例中。也就是说，有不同的系统来映射某个 key 到某个 Redis 服务。

**范围分区**

最简单的分区是按范围分区，就是映射一定范围的对象到特定的 Redis 实例。

比如，ID 从 0 到 10000 的用户会保存到实例 R0 ，ID从10001 到 20000的用户会保存到 R1,以此类推。

这种方式是可行的，并且在实际中使用，不足就是要有一个区间范围到实例的映射表。这个表要被管理，同时还需要各种对象的映射表，通常对 Redis 来说并非是好的方法。

**哈希分区**

另外一种分区方法是 hash 分区，这对任何 key 都适用，也无需是 object_name这种形式，像下面描述的一样简单：

- 用一个 hash 函数将 key 转换为一个数字，比如使用 crc32 hash 函数。对 key foobar 执行 crc32(foobar) 会输出类似 93024922 的整数。
- 对这个整数取模，将其转化为 0-3 之间的数字，就可以将这个 整数映射到 4个 Redis 实例中的一个了，93024922 % 4 = 2，就是说 key foobar 应该被存到R2实例中。注意：取模操作是取除的余数，通常在多种编程语言中用 % 操作符来实现的。

