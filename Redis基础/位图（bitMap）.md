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

### 二进制位和位置偏移量 ###

在二进制位范围命令中，用户有两种方法来设置偏移量 ：

- 如果用户给定的是一个没有任何前缀的数字，那么这个数字指示的就是字符串以零为开始 （zero-base）的偏移量。
- 另一方面，如果用户给定的是一个带 `#` 前缀的偏移量，那么命令将使用这个偏移量与被设置的数字类型的位长度相乘，从而计算出正在的偏移量。

例：

	DITFIELD mystring SET i8 #0 100 i8 #1 200

 命令会把  `mystring` 键里面，第一个 `i8` 长度的二进制位的值设置为 `100` ， 并把第二个 `i8`  长度的二进制位的值设置为 `200` 。当我们把 一个字符串键当成数组来使用，并且数组中储存的都是同等长度的整数时，使用 `#` 前缀可以让我们免去手动计算被设置二进制位所在位置的麻烦。

### 溢出控制 ###

用户可以通过 `OVERFLOW` 命令以及以下展示的三个参数，指定 `BITFIELD` 命令在执行自增或者自减操作时，碰上向上溢出（overflow） 或者向下溢出（underflow）情况时的行为：

- **WRAP ：**  使用回绕(wrap around) 方法处理有符号整数和无符号整数的溢出情况。对于无符号整数来说，回绕就像使用数值本身与能够被储存的最大无符号整数 被执行取模计算，这也是 C 语言的标准行为。对于有符号整数来说，上溢将导致数字重新从最小负数开始计算，而下溢将导致数字重新开始从最大的整数开始计算。比如说，如果我们对一个值 127 的 i8 整数执行加一操作，那么将得到结果 -128 。
- **SAT ：**  使用饱和计算（saturation arithmetic）方法处理溢出，也就是说，下溢计算的结果为最小的整数值，而上溢计算结果为最大的整数值。举个例子，如果我们对一个值为 120 的 i8 整数执行 加 10 计算，那么命令结果将为 i8 类型所能储存的最大整数值 127。 与此相反，如果一个针对 i8 值的计算造成了下溢，那么这个 i8 值将被设置为 -127 。

- **FAIL ：** 在这个模式下，命令将拒绝执行那些会导致上溢或者下溢情况出现的计算，并向用户返回空值表示计算未被执行。

需要注意的是，`OVERFLOW` 子命令只会对紧随着它之后执行的 `INCRBY` 命令产生效果，这一效果将一直延续到与它一同执行的下一个 `OVERFLOW` 命令为止。在默认情况下， `INCRBY` 命令使用 	`WRAP` 方式来处理溢出计算。

以下是一个使用 `OVERFLOW` 子命令来控制溢出行为的例子：

 	coderknock> BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
	1) (integer) 1
	2) (integer) 1
	
	coderknock> BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
	1) (integer) 2
	2) (integer) 2
	
	coderknock> BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
	1) (integer) 3
	2) (integer) 3
	
	coderknock> BITFIELD mykey incrby u2 100 1 OVERFLOW SAT incrby u2 102 1
	1) (integer) 0  -- 使用默认的 WRAP 方式处理溢出
	2) (integer) 3  -- 使用 SAT 方式处理溢出

而以下则是一个因为 `OVERFLOW FAIL` 行为而导致子命令返回控制的例子：

	coderknock> BITFIELD mykey OVERFLOW FAIL incrby u2 102 1
	1) (nil)

#### 作用 ####

**BITFIELD** 命令的作用在于它能够将很多小的整数储存到一个长度较大的位图中，又或者将一个非常庞大的键分割为多个较小的键来进行储存，从而非常高效地使用内存，使得 Redis 能够得到更多不同的应用————特别是在实时分析领域： BITFIELD 能够以指定的方式对计算溢出进行控制的能力，使得他可以被应用于这一领域。

#### 性能注意事项 ####


**BITFIELD** 在一般情况下都是一个快速命令，需要注意的是，访问一个长度较短的字符串的远端二进制位 将引发一次内存分配操作，这一操作花费的时间可能会比命令访问已有的字符串花费的时间要长。

#### 二进制位的排列 ####

- **BITFIELD** 把位图第一个字节偏移量 0 上的二进制位看作是 `most significant` 位，以此类推。举例 ，如果我们对一个已经预先被全部设置为 0 的位图进行设置。将它偏移量 7 的值设置为 5位无符号整数值23,（二进制位 10111），那么命令将生产出以下这个位图标识
	
		
		+--------+--------+
		|00000001|01110000|
		+--------+--------+

当偏移量和整数长度与字节边界进行对齐时， **BITFIELD** 表示二进制位的方式跟大端表示法（big endian）一致，但是在没有对齐的情况下，理解这些二进制位是如何排列的非常重要。

#### 返回值 ： ####

如果 **member** 元素是集合的成员，返回1 。

如果 **member** 元素不是集合的成员，或 key 不存在，则返回 0 。

**示例**

	coderknock> SISMEMBER saddTest add1
	(integer) 1
	# add7  元素不存在
	coderknock> SISMEMBER saddTest add7
	(integer) 0
	# key 不存在
	coderknock> SISMEMBER nonSet a
	(integer) 0
	# key 类型不是集合
	coderknock> SISMEMBER embstrKey a
	(error) WRONGTYPE Operation against a key holding the wrong kind of value    


### 案例 ###

#### 场景一 ： 用户签到  ####
		
	
	# 用户 uid

	$uid      = 99;
	$cacheKey = "sign_" . $uid;
	
	
	# 记录有 uid 的key
	
	# $cacheKey = sprintf("sign_%d" ,$uid);
	
	# 每月开始的日期
	
	$startDate = date( "Y-m" ) . "-01";
	
	# 今天的日期
	
	$todayDate = date( 'Y-m-d' );
	
	#计算 offset (时间差)
	
	$startTime = strtotime( $startDate );
	$todayTime = time();
	$offset    = ceil( ( $todayTime - $startTime ) / 86400 );
	
	
	# 签到图表
	echo date( "Y-m" ) . "月签到表 :  <br /><br />";
	
	for ( $i = 1; $i <= $offset; $i++ ) {
	    $signState = $redis->getBit( $cacheKey, $i );
	    $isSign    = boolval( $signState ) ? "√" : "×";
	    echo date( "Y-m" ) . "-" . $i . " 签到状态 ：" . $isSign . "<br />";
	}
	
	echo "<hr >";
	
	echo "今天是第 " . $offset . " 天<br /><br />";
	
	# 已连续签到
	
	$beforeLXCheckInSum = 0;
	for ( $i = $offset - 1; $i > 0; $i-- ) {
	    $signState = $redis->getBit( $cacheKey, $i );
	
	    if ( intval( $signState ) != 1 ) break;
	    $beforeLXCheckInSum++;
	}
	
	echo "已连续签到天数(当天未签到) : " . $beforeLXCheckInSum . "<br /><br />";
	
	
	# 签到
	
	# 一年一个用户会占用多少空间 （大约 365/8 = 45.625 Byte）
	
	# 查询签到
	
	$signStatus = $redis->getBit( $cacheKey, $offset );
	if ( $signStatus === 1 ) {
	    # 统计 签到次数
	
	    $signCount = $redis->bitCount( $cacheKey );
	    echo "总签到天数： " . $signCount . "<br />";
	
	} else {
	
	    $signRst = $redis->setBit( $cacheKey, $offset, 1 );
	    if ( $signRst != 0 ) exit( "今天已经签到过了！<br />" );
	
	    echo $todayDate . " 签到成功！<br />";
	}
	
	
	# 计算当月连续签到天数
	
	$lXCheckInSum = 0;
	for ( $i = $offset; $i > 0; $i-- ) {
	    $signState = $redis->getBit( $cacheKey, $i );
	
	    if ( intval( $signState ) != 1 ) break;
	
	    $lXCheckInSum++;
	}
	
	exit( "连续签到天数：" . $lXCheckInSum );
	
	
	exit( "End..." );

