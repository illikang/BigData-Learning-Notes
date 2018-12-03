<!-- GFM-TOC -->
* [Redis数据类型](#Redis数据类型)
    * [概述](#概述)
    * [key](#key)
      * [key键](#key键)
      * [key命令](#key命令)
    * [String](#String)
      * [String字符串](#String字符串)
      * [String值命令](#key命令)
    * [hash](#hash)
      * [hash哈希](#hash哈希)
      * [hash值命令](#快速选择)
    * [list](#list)
      * [list列表](#list列表)
      * [list值命令](#list值命令)
    * [set](#set)
      * [set集合](#set集合)
      * [set值命令](#set值命令)
    * [zset](#set)
      * [zset有序集合](#zset有序集合)
      * [zset值命令](#zset值命令)

# Redis数据类型
## 概述
Redis是基于key-value的存储系统。
它的值（value）支持五种数据类型：String(字符串),hash（哈希）,list（列表）,set（集合）和zset(sorted set：有序集合)。

## key

### key键
Redis是键值存储结构，我们要存储的数据在值中，而值得索引则存储在键中。猜测：没有在资料中看到key的数据类型，猜测应该是字符串类型。

### key命令
Redis键命令用于管理redis的键
1. DEL key：在 key 存在时删除 key。
```
del bb
//返回0表示操作失败，因为key bb不存在
>(integer) 0
set bb test
del bb
>(integer) 1
```
2. DUMP key ：序列化给定 key ，并返回被序列化的值。
```
set bb tst
dump bb
>"\x00\x03tst\a\x00f\xfe\xe8\x96\xa4\rU}"
```
3. EXISTS key :检查给定 key 是否存在。
```
exists bc
>(integer) 0
```
4. EXPIRE key seconds:为给定 key 设置过期时间，以秒计。
```
expire a 5
>(integer) 1
get a
>(nil)
```
5. EXPIREAT key timestamp :和 EXPIRE 类似，都用于为 key 设置过期时间。 不同在于 EXPIREAT 命令接受的时间参数是 UNIX 时间戳(unix timestamp)。
6. PEXPIRE key milliseconds :设置 key 的过期时间以毫秒计。
7. PEXPIREAT key milliseconds-timestamp :置 key 过期时间的时间戳(unix timestamp) 以毫秒计
8. KEYS pattern :查找所有符合给定模式( pattern)的 key 。类似模糊匹配。
```
set name1 leon
set name2 tom
set name3 jacky
set name4 lili
//查找以name为开头的key
keys name*
> "name1"
> "name2"
> "name3"
> "name4"
//查找redis中所有的key可用*
keys *
```
9. MOVE key db :将当前数据库的 key 移动到给定的数据库 db 当中。
```
//redis默认使用数据库0，用select index命令选择数据库
select 0
set name aaaa   //在库0中存入键值对name=aaaa
select 1
exists name    //库1中不存在name
>(integer) 0
select 0       //返回库0
move name 1    //将键name添加到库1
>(integer) 1   
select 1       //返回库1
exists name    //检查name是否存在
>(integer) 1    //返回1，表示存在
```
10. PERSIST key :移除 key 的过期时间，key 将持久保持。
11. PTTL key ：以毫秒为单位返回 key 的剩余的过期时间。
12. TTL key ：以秒为单位，返回给定 key 的剩余生存时间(TTL, time to live)。
13. RANDOMKEY ：从当前数据库中随机返回一个 key 。
14. RENAME key newkey :修改 key 的名称。
```
set person a
rename person p
get person
>(nil)
get p
>"a"
```
15. RENAMENX key newkey :仅当 newkey 不存在时，将 key 改名为 newkey 。
16. TYPE key :返回 key 所储存的值的类型。
```
type p
>string
```

## String

### String字符串
1. String是redis最基本的类型，其他集合类型都是基于String构建的。
2. String类型是二进制安全的，即redis的string可以包含任何数据，比如jpg图片或者序列化对象。
3. String类型的值最大能存储512MB.

### String值命令
1. SET key value ：设定指定key的值。
```
set name "Leon"
```
2. GET key:获取指定key的值。
```
get name
>"Leon"
```
3. ETRANGE key start end :返回 key 中字符串值的子字符。
```
getrange name 1 2
//注意下标索引是从0开始的，所以这里1 2返回的是字符串“Leon”的的第2、3个字母。
>""eo
```
4. GETSET key value：将给定 key 的值设为 value ，并返回 key 的旧值(old value)。
```
getset name illikang
>"Leon"
```
5. SETBIT key offset:对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)。<br/>
6. GETBIT key offset：对 key 所储存的字符串值，获取指定偏移量上的位(bit)。
```
//bit 默认被初始化为0
set bitmap "test"
getbit bitmap 20
//在没有设置过bitmap偏移量20位上值时，取出的是0
>(integer) 0
//设置位值以后，再取出的值为设置值。
setbit bitmap 20 1
getbit bitmap 20
>(integer) 1
```
补充：bit位存储是个很强大的功能。bit位只能存储0或1，可以用户很多场景，比如性别，商品是否有库存，座位是否已经预定等等。<br/>
7. MGET key1 [key2..]：获取所有(一个或多个)给定 key 的值。
```
mget name bitmap
>"illikang"
>"te{t"
```
补充：这里bitmap取出的值变为“te{t”是因为之前的位操作，改变了字符串的内容。<br/>
8. SETEX key seconds value：将值 value 关联到 key ，并将 key 的过期时间设为 seconds (以秒为单位)。
```
setex expire 10 aaa
get expire
>"aaa"
//10秒后，键值失效，再取值返回未空
get expire
(nil)
```
9. SETNX key value:只有在 key 不存在时设置 key 的值。
```
//键name已经存在。用setnx赋值，返回0，无法成功复制
setnx name leon
>(integer) 0
get name
>"illikang"
//键age不存在。用setnx赋值，返回1，可以成功复制
setnx age 10
>(integer) 1
get age
>"10"
```
10. SETRANGE key offset value:用 value 参数覆写给定 key 所储存的字符串值，从偏移量 offset 开始。
```
//键值对name="illikang"
setrange name 3 aaaa
//返回的8是修改后字符串的总长度
>(integer) 8
get name
>"illaaag"
```
11. STRLEN key:返回 key 所储存的字符串值的长度。
```
strlen name
>(integer) 8
```
12. MSET key value [key value ...]:同时设置一个或多个 key-value 对。
13. MSETNX key value [key value ...] :同时设置一个或多个 key-value 对，当且仅当所有给定 key 都不存在。
14. PSETEX key milliseconds value:和 SETEX 命令相似，但它以毫秒为单位设置 key 的生存时间，而不是像 SETEX 命令那样，以秒为单位。
15. INCR key:将 key 中储存的数字值增一。
```
set num 1
incr num
>(integer) 2
get num
"2"
```
16. INCRBY key increment:将 key 所储存的值加上给定的增量值（increment） 。
```
incrby num 6
>(integer) 8
```
17. INCRBYFLOAT key increment:将 key 所储存的值加上给定的浮点增量值（increment） 。
```
incrbyfloat num 1.2
>"9.19999999999"
```
18. DECR key:将 key 中储存的数字值减一。
19. DECRBY key decrement:key 所储存的值减去给定的减量值（decrement） 。
20. APPEND key value:如果 key 已经存在并且是一个字符串， APPEND 命令将指定的 value 追加到该 key 原来值（value）的末尾。
```
set a wang
append a kang
>(integer) 8
get a
>"wangkang"
```

## hash

### hash哈希
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象

### hash值命令
1. HSET key field value ：将哈希表 key 中的字段 field 的值设为 value 。
```
hset h1 name leon
>(integer) 1   //创建成功
```
2. HSETNX key field value :只有在字段 field 不存在时，设置哈希表字段的值。
```
hestnx h1 name Leon
>(integer) 0   //key h1的field name已经存在，无法创建
hsetnx h1 age 30
>(integer) 1    //key h1的field age不存在，可以创建
```
3. HMSET key field1 value1 [field2 value2 ] ：同时将多个 field-value (域-值)对设置到哈希表 key 中。
4. HGET key field ：获取存储在哈希表中指定字段的值。
```
hget h1 name
>"leon"
```
5. HGETALL key :获取在哈希表中指定 key 的所有字段和值
```
hgetall h1
>"name"
>"leon"
>"age"
>"30"
```
6. HKEYS key :获取所有哈希表中的字段(field)
```
hkeys h1
>"name"
>"age"
```
7. HMGET key field1 [field2] :获取所有给定字段的值
```
hmget h1 name age
1) "leon"
2) "30"
```
8. HEXISTS key field :查看哈希表 key 中，指定的字段是否存在。
```
hexists h1 name
(integer) 1
```
9. HDEL key field1 [field2]  :删除一个或多个哈希表字段
```
hdel h1 age
(integer) 1
```
10. HINCRBY key field increment 为哈希表 key 中的指定字段的整数值加上增量 increment 。
```
hset h1 age 20
hincrby h1 age 1
(integer) 21
```
11. HINCRBYFLOAT key field increment :为哈希表 key 中的指定字段的浮点数值加上增量 increment 。
12. HLEN key :获取哈希表中字段的数量
```
hlen h1
(integer) 2
```
13. HVALS key :获取哈希表中所有值
```
hvals h1
1) "leon"
2) "21"
```
14. HSCAN key cursor [MATCH pattern] [COUNT count] :迭代哈希表中的键值对。

## list

### list列表
Redis的list是每个子元素都是String类型的双向链表，可以通过push和pop操作从列表的头部或者尾部添加或者删除元素，这样List即可以作为栈，也可以作为队列。

### list值命令
1. LPUSH key value1 [value2] :将一个或多个值插入到列表头部
```
lpush group leon wang zhang tom
>(integer) 4
```
2. LPOP key :移出并获取列表的第一个元素
```
lpop group
>"tom"
```
3. RPUSH key value1 [value2] :在列表尾中添加一个或多个值
```
//group:[leon，wang,zhang]
rpush group first
lpop group
>"zhang"
```
4. RPOP key ：移除列表的最后一个元素，返回值为移除的元素。
```
//group:[first,leon, wang]
rpop group
>"first"
```
5. BLPOP key1 [key2 ] timeout :移出并获取列表的第一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
```
lpush newlist aa
blpop newlist 5
//列表newlist中有元素，马上得到输出结果
>1)"newlist"
>2)"aa"
blpop newlist 5
//列表newlist已经没有元素，等待大约5秒以后输出
（nil）
（5.04s）
```
6. BRPOP key1 [key2 ] timeout :移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
7. BRPOPLPUSH source destination timeout ：从列表中弹出一个值，将弹出的元素插入到另外一个列表中并返回它； 如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止。
8. LINDEX key index ：通过索引获取列表中的元素
```
//group:[leon, wang]
lindex group 1
>"leon"
```
9. LINSERT key BEFORE|AFTER pivot value :在列表的元素前或者后插入元素
```
//group:[leon, wang]
linsert group after leon three
//group:[leon, three,wang]
```
10. LLEN key :获取列表长度
11. LRANGE key start stop ：获取列表指定范围内的元素,0表示列表的第一个元素，1表示列表的第二个元素，以此类推。也可是使用负数下表，以-1表示列表的最后一个元素，-2表示列表的倒数第二个元素，以此类推
```
//group:[leon,three,wang]
lrange group 0 -1 //获取列表的所有元素
>"wang"
>"three"
>"leon"
```
12. LREM key count value :移除列表元素
  * count > 0 : 从表头开始向表尾搜索，移除与 VALUE 相等的元素，数量为 COUNT 。
  * count < 0 : 从表尾开始向表头搜索，移除与 VALUE 相等的元素，数量为 COUNT 的绝对值。
  * count = 0 : 移除表中所有与 VALUE 相等的值。
```
lpush team aa aa bb bb cc dd aa  // team=[aa,aa,bb,bb,cc,dd,aa]
lrem team -2 aa
>(interger) 2
>1)"aa"
>2)"dd"
>3)"cc"
>4)"bb"
>5)"bb"
```
13. LSET key index value :通过索引设置列表元素的值
```
// team =[bb,bb,cc,dd,aa]
lset team 0 new      //team =[bb,bb,cc,dd,new]
```
14. LTRIM key start stop :对一个列表进行修剪(trim)，就是说，让列表只保留指定区间内的元素，不在指定区间之内的元素都将被删除。
```
// team =[bb,bb,cc,dd,new]
ltrim team 0 2    //   team =[cc,dd,new]
```
15. RPOPLPUSH source destination :移除列表的最后一个元素，并将该元素添加到另一个列表并返回
16. RPUSHX key value :为已存在的列表添加值
17. LPUSHX key value :将一个值插入到已存在的列表头部

## set

### set集合

Set是String类型的无序集合。集合成员是唯一的，这意味着集合中不能出现重复的数据。Redis的set是通过哈希表实现的，所以添加、删除、查找的复杂度都是O(1)。

### set值命令
1. SADD key member1 [member2] 向集合添加一个或多个成员
```
sadd s1 leon tom jack
(integer) 3
```
2. SREM key member1 [member2] :移除集合中一个或多个成员
```
srem s1 leon
(integer) 1
```
3. SCARD key :获取集合的成员数
```
scard s1
(integer) 2
```
4. SMEMBERS key :返回集合中的所有成员
```
smembers s1
1) "jack"
2) "tom"
```
5. SISMEMBER key member :判断 member 元素是否是集合 key 的成员
```
sismember s1 jack
(integer) 1
```
6. SDIFF key1 [key2] :返回给定所有集合的差集
```
sadd s1 leon jack leom tom
sadd s2 leon lucy jason
1) "jack"
2) "leom"
3) "tom"
```
7. SDIFFSTORE destination key1 [key2] :返回给定所有集合的差集并存储在 destination 中
8. SINTER key1 [key2]
```
sinter s1 s2
1) "leon"
```
9. SINTERSTORE destination key1 [key2] :返回给定所有集合的交集并存储在 destination 中
10. SMOVE source destination member :将 member 元素从 source 集合移动到 destination 集合
```
smove s1 s2 jack
(integer) 1
```
11. SPOP key :移除并返回集合中的一个随机元素
```
spop s1
"leom"
```
12. SRANDMEMBER key [count] :返回集合中一个或多个随机数
13. SUNION key1 [key2] :返回所有给定集合的并集
```
sunion s1 s2
1) "leon"
2) "jason"
3) "jack"
4) "tom"
5) "lucy"
```
14. SUNIONSTORE destination key1 [key2] :所有给定集合的并集存储在 destination 集合中
15. SSCAN key cursor [MATCH pattern] [COUNT count] :迭代集合中的元素

## zset

### zset有序集合
1. Redis有序集合和集合一样也是string类型元素的集合，且不允许重复的成员。
2. 不同的是，有序集合中的每个元素都会关联一个double类型的分数。Redis正是通过分数来为集合中的成员进行从小到大的排序。
3. 有序集合的成员时唯一的，但与成员关联的double分数却可以重复。
4. 在redis sorted sets里面当items内容大于64的时候同时使用了hash和skiplist两种设计实现。这也会为了排序和查找性能做的优化。所以如上可知：
    * 添加和删除都需要修改skiplist，所以复杂度为O(log(n))。
    * 但是如果仅仅是查找元素的话可以直接使用hash，其复杂度为O(1)
    * 其他的range操作复杂度一般为O(log(n))
    * 当然如果是小于64的时候，因为是采用了ziplist的设计，其时间复杂度为O(n)

### zset值命令
1. ZADD key score1 member1 [score2 member2] :向有序集合添加一个或多个成员，或者更新已存在成员的分数
```
zadd z1 1 leon 2 tom 3 jack
(integer) 3
```
2. ZCARD key :获取有序集合的成员数
```
zcard z1
(integer) 3
```
3. ZCOUNT key min max :计算在有序集合中指定区间分数的成员数
```
zcount z1 1 3
(integer) 3
```
4. ZINCRBY key increment member :有序集合中对指定成员的分数加上增量 increment
```
zincrby z1 1 leon   // 1.leon 2.tom 3. jacky
"2"
```
5. ZINTERSTORE destination numkeys key [key ...] :计算给定的一个或多个有序集的交集并将结果集存储在新的有序集合 key 中
6. ZLEXCOUNT key min max :在有序集合中计算指定字典区间内成员数量
```
zlexcount z1 - +
(integer) 4
```
7. 	ZRANGE key start stop [WITHSCORES] :通过索引区间返回有序集合成指定区间内的成员
```
zrange z1 0 200
1) "leon"
2) "tom"
3) "jack"
4) "lucy"
```
8. ZRANGEBYLEX key min max [LIMIT offset count] :通过字典区间返回有序集合的成员
9. ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT] :通过分数返回有序集合指定区间内的成员
10. ZRANK key member :返回有序集合中指定成员的索引
11. REM key member [member ...] :移除有序集合中的一个或多个成员
12. ZREMRANGEBYLEX key min max :移除有序集合中给定的字典区间的所有成员
13. ZREMRANGEBYRANK key start stop :移除有序集合中给定的排名区间的所有成员
14. ZREMRANGEBYSCORE key min max :移除有序集合中给定的分数区间的所有成员
15. ZREVRANGE key start stop [WITHSCORES] :返回有序集中指定区间内的成员，通过索引，分数从高到底
16. ZREVRANGEBYSCORE key max min [WITHSCORES]  :返回有序集中指定分数区间内的成员，分数从高到低排序
17. ZREVRANK key member :返回有序集合中指定成员的排名，有序集成员按分数值递减(从大到小)排序
18. ZSCORE key member :返回有序集中，成员的分数值
19. ZUNIONSTORE destination numkeys key [key ...] :计算给定的一个或多个有序集的并集，并存储在新的 key 中
20. ZSCAN key cursor [MATCH pattern] [COUNT count] :迭代有序集合中的元素（包括元素成员和元素分值）
