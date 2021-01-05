### String 

常⽤命令: set,get,decr,incr,mget 等。 

String数据结构是简单的key-value类型，value其实不仅可以是String，也可以是数字。 常规keyvalue缓存应⽤； 常规计数：微博数，粉丝数等。 

### Hash 

常⽤命令： hget,hset,hgetall 等。 

hash 是⼀个 string 类型的 field 和 value 的映射表，hash 特别适合⽤于存储对象，后续操作的时 候，你可以直接仅仅修改这个对象中的某个字段的值。 ⽐如我们可以 hash 数据结构来存储⽤户信 息，商品信息等等。⽐如下⾯我就⽤ hash 类型存放了我本⼈的⼀些信息： 

```json
key=JavaUser293847
value={
 “id”: 1,
 “name”: “SnailClimb”,
 “age”: 22,
 “location”: “Wuhan, Hubei”
}
```

### List 

常⽤命令: lpush,rpush,lpop,rpop,lrange等 

list 就是链表，Redis list 的应⽤场景⾮常多，也是Redis最重要的数据结构之⼀，⽐如微博的关注 列表，粉丝列表，消息列表等功能都可以⽤Redis的 list 结构来实现。

 Redis list 的实现为⼀个双向链表，即可以⽀持反向查找和遍历，更⽅便操作，不过带来了部分额外 的内存开销。

 另外可以通过 lrange 命令，就是从某个元素开始读取多少个元素，可以基于 list 实现分⻚查询，这 个很棒的⼀个功能，基于 redis 实现简单的⾼性能分⻚，可以做类似微博那种下拉不断分⻚的东⻄ （⼀⻚⼀⻚的往下⾛），性能⾼。 

### Set

常⽤命令： sadd,spop,smembers,sunion 等 

set 对外提供的功能与list类似是⼀个列表的功能，特殊之处在于 set 是可以⾃动排重的。 

当你需要存储⼀个列表数据，⼜不希望出现重复数据时，set是⼀个很好的选择，并且set提供了判断某 个成员是否在⼀个set集合内的重要接⼝，这个也是list所不能提供的。可以基于 set 轻易实现交集、 并集、差集的操作。 

⽐如：在微博应⽤中，可以将⼀个⽤户所有的关注⼈存在⼀个集合中，将其所有粉丝存在⼀个集合。 Redis可以⾮常⽅便的实现如共同关注、共同粉丝、共同喜好等功能。这个过程也就是求交集的过程， 具体命令如下：  

```
sinterstore key1 key2 key3 将交集存在key1内
```

### Sorted Set 

常⽤命令： zadd,zrange,zrem,zcard等 

和set相⽐，sorted set增加了⼀个权重参数score，使得集合中的元素能够按score进⾏有序排列。 

举例： 在直播系统中，实时排⾏信息包含直播间在线⽤户列表，各种礼物排⾏榜，弹幕消息（可以理 解为按消息维度的消息排⾏榜）等信息，适合使⽤ Redis 中的 Sorted Set 结构进⾏存储。 