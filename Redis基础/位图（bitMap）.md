## 位图 Redis-BitMap ##

	https://juejin.im/post/5a7dcad0f265da4e6f17d942

**bitMap** 通过一个 bit位来表示某个元素对应的值或者状态，其中的 key 就是对应元素本身。bitmaps 本身不是一种数据结构，实际上 它就是字符串 ( key 对应的 value 就是一串二进制)， 但它可以对字符串的位 进行操作。Bitmaps 单独提供了一套命令，所以在 Redis 中使用 BitMaps 和 使用字符串的方式不太相同。就可以把 bitMaps 想象成一个 以 位为单位的数组，数组的每个单元只能存储  0 和 1 ，数组下标在 bitmaps 中叫做偏移量。


### bitMap的命令 ###


**SETBIT**

	SETBIT key offset value

**说明**

对 key 索存储于的字符串值，设置或清除指定偏移量上的位（bit）。

位的设置或清除取决于 value 参数，可以是 0 也可以是 1。

当 key 不存在时，自动生成一个新的字符串值。

字符串会进行伸展（grown）以确保它可以将 value 保存在指定的偏移量上。当字符串值进行伸展时，空白位置以 0 填充。

**offset** 参数必须大约或等于 0 ，小于 2^32(bit 映射被限制在512 MB之内)。

对使用大的 offset 的 SETBIT 操作来说，内存分配可能造成 Redis 服务器被阻塞。


**返回值：**

字符串值指定偏移量上原来储存的位（bit）。

**示例：**

	# SETBIT 会返回之前位的值 （默认是0） 这里会生成 126 个位

	coderknock> SETBIT testBit 125 1
	(integer) 0
	coderknock> SETBIT testBit 125 0
	(integer) 1
	coderknock> SETBIT testBit 125 1
	(integer) 0
	coderknock> SETBIT testBit 125
	(integer) 1
	coderknock> SETBIT testBit 100
	(integer) 0

	# SETBIT value 只能是 0 或 1  二进制只能是 0 或 1

	coderknock> SETBIT testBit 618 2
	(error) ERR bit is not an integer orout of range


**获取值**

GETBIT

**语法：**  GETBIT key offset

**说明** 

对 key 所存储的字符串值，获得指定偏移量上的位（bit）。

当 offset 比字符串值的长度达，或者 key 不存在时，返回0 。

**返回值：** 
