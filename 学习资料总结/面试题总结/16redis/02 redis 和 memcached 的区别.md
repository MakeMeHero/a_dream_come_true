对于 redis 和 memcached 我总结了下⾯四点。现在公司⼀般都是⽤ redis 来实现缓存，⽽且 redis ⾃身也越来越强⼤了！ 

1. redis⽀持更丰富的数据类型（⽀持更复杂的应⽤场景）：Redis不仅仅⽀持简单的k/v类型的数 据，同时还提供list，set，zset，hash等数据结构的存储。memcache⽀持简单的数据类型， String。

2. Redis⽀持数据的持久化，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进⾏使 ⽤,⽽Memecache把数据全部存在内存之中。

3. 集群模式：memcached没有原⽣的集群模式，需要依靠客户端来实现往集群中分⽚写⼊数据；但 是 redis ⽬前是原⽣⽀持 cluster 模式的. 

4. Memcached是多线程，⾮阻塞IO复⽤的⽹络模型；Redis使⽤单线程的多路 IO 复⽤模型。 \

   

![](E:\学习资料总结\面试题总结\16redis\assets/QQ截图20201227121033.png)