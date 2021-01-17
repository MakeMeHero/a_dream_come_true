Redis 内存淘汰机制有以下几个：

- noeviction: 当内存不足以容纳新写入数据时，新写入操作会报错，这个一般没人用吧，实在是太恶心了。

- **allkeys-lru**：当内存不足以容纳新写入数据时，在**键空间**中，移除最近最少使用的 key（这个是**最常用**的）。

- allkeys-random：当内存不足以容纳新写入数据时，在**键空间**中，随机移除某个 key，这个一般没人用吧，为啥要随机，肯定是把最近最少使用的 key 给干掉啊。

- volatile-lru：当内存不足以容纳新写入数据时，在**设置了过期时间的键空间**中，移除最近最少使用的 key（这个一般不太合适）。

- volatile-random：当内存不足以容纳新写入数据时，在**设置了过期时间的键空间**中，**随机移除**某个 key。

- volatile-ttl：当内存不足以容纳新写入数据时，在**设置了过期时间的键空间**中，有**更早过期时间**的 key 优先移除。

  ```java
  手写一个 LRU 算法
  你可以现场手写最原始的 LRU 算法，那个代码量太大了，似乎不太现实。
  
  不求自己纯手工从底层开始打造出自己的 LRU，但是起码要知道如何利用已有的 JDK 数据结构实现一个 Java 版的 LRU。
  
  class LRUCache<K, V> extends LinkedHashMap<K, V> {
      private final int CACHE_SIZE;
  
      /**
       * 传递进来最多能缓存多少数据
       *
       * @param cacheSize 缓存大小
       */
      public LRUCache(int cacheSize) {
          // true 表示让 linkedHashMap 按照访问顺序来进行排序，最近访问的放在头部，最老访问的放在尾部。
          super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true);
          CACHE_SIZE = cacheSize;
      }
  
      /**
       * 钩子方法，通过put新增键值对的时候，若该方法返回true
       * 便移除该map中最老的键和值
       */
      @Override
      protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
          // 当 map中的数据量大于指定的缓存个数的时候，就自动删除最老的数据。
          return size() > CACHE_SIZE;
      }
  }
  ```

  