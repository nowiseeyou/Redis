## 位图 Redis-BitMap ##

	https://juejin.im/post/5a7dcad0f265da4e6f17d942

**bitMap** 通过一个 bit位来表示某个元素对应的值或者状态，其中的 key 就是对应元素本身。bitmaps 本身不是一种数据结构，实际上 它就是字符串 ( key 对应的 value 就是一串二进制)， 但它可以对字符串的位 进行操作。Bitmaps 单独提供了一套命令，所以在 Redis 中使用 BitMaps 和 使用字符串的方式不太相同。就可以把 bitMaps 想象成一个 以 位为单位的数组，数组的每个单元只能存储  0 和 1 ，数组下标在 bitmaps 中叫做偏移量。

- 

### bitMap的命令 ###


#### SETBIT ####

	SETBIT key offset value

**说明**

对 key 索存储于的字符串值，设置或清除指定偏移量上的位（bit）。

位的设置或清除取决于 value 参数，可以是 0 也可以是 1。

当 key 不存在时，自动生成一个新的字符串值。

字符串会进行伸展（grown）以确保它可以将 value 保存在指定的偏移量上。当字符串值进行伸展时，空白位置以 0 填充。

offset 参数必须大约或等于 0 ，小于 2^32(bit 映射被限制在512 MB之内)。

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


#### 获取值 ####

    GETBIT key offset

**说明** 

对 key 所存储的字符串值，获得指定偏移量上的位（bit）。

当 offset 比字符串值的长度达，或者 key 不存在时，返回0 。

**返回值：** 

字符串值指定偏移量上的位（bit）。

- 

#### 获取bitMaps 指定范围值为 1 的位个数 ####

	BITCOUNT key start

**说明：**

计算给定字符串中，被设置为 1 的比特位的数量。

一般情况下，给定的字符串都会被进行计数，通过指定额外的 start 或 end 参数，可以让计数只在特定的位上进行。

start 和 end 参数的设置和  GETRANGE 命令类似，都可以使用负数值： 比如 -1 表示最后一个字节，-2 表示倒数第二个字节，以此类推。

不存在的 key 被当成是空字符串来处理，因此对一个不存在的 key 进行 BITCOUNT 操作，结果为0。

**返回值：**

被设置为 1 的位的数量。


- 


#### 多个bitMap的计算 ####

	BITOP operation destkey key [key...]

**说明：**

对一个或多个保存二进制位的字符串 key 进行位元操作，并将结果保存到 destkey 上。

operation 可以是 AND、 OR 、NOT、XOR 这四种操作中的任意一种：

- `BITOP AND destkey key [key ...]`  ： 对一个或多个 key 求逻辑并，并将结果保存到 destkey 。
- `BITOP OR destkey key [key ...]`	： 对一个或多个 key 求逻辑或，并将结果保存到 destkey 。
- `BITOP XOR destkey key [key ...]`  ： 对一个或多个 key 求逻辑异或，并将结果保存到 destkey 。
- `BITOP NOT destkey key`  ： 对给定 key 求逻辑非，并将结果保存 destkey 。
 
除了 NOT 操作外，其他操作都可以接受一个或多个 key 作为输入。


#### 处理不同长度的字符串 ####

当 BITOP 处理不同长度的字符串时，较短的那个字符串缺少的部分会被看作 0 。

空的 key 也被看作是包含 0 的字符串序列 。

**返回值：**

保存到 destkey 的字符串的长度，和输入 key 中最长的字符串长度相等。



### 注意 ###

- 使用 GET 子命令对超出字符串当前范围的二进制位 进行访问 （包括键不存在的情况），超出部分的 二进制位的值被当做是 0 。
- 使用 SET 子命令或者 INCRBY 子命令对超出字符串 当前范围的二进制位进行访问将导致字符串被扩大，被扩大的部分会使用值为 0 的二进制位进行填充。在对字符串进行扩展时，命令会根据字符目前已有的最远端二进制位，计算出执行操作所需的最小长度。

#### 支持的子命令以及数字类型 ####

以下是 BITFIFLD 命令支持的子命令 ： 

- `GET <type> <offset>` — 返回指定的二进制位范围
- `SET <type> <offset> <value>` — 对指定的二进制位范围进行设置，并返回她的旧值
- `INCRBY <type> <offset> <increment>` — 对指定的二进制位范围执行加法操作，并返回她的旧值。用户可以通过向 increment 参数传入负值来实现相应的减法操作。

