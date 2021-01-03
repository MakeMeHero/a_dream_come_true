1.线程是否安全： HashMap 是⾮线程安全的，HashTable 是线程安全的；HashTable 内部的⽅法 基本都经过 synchronized 修饰。（如果你要保证线程安全的话就使⽤ ConcurrentHashMap 吧！）； 

2. 效率： 因为线程安全的问题，HashMap 要⽐ HashTable 效率⾼⼀点。另外，HashTable 基本被 淘汰，不要在代码中使⽤它；
3. 对Null key 和Null value的⽀持： HashMap 中，null 可以作为键，这样的键只有⼀个，可以 有⼀个或多个键所对应的值为 null。。但是在 HashTable 中 put 进的键值只要有⼀个 null， 直接抛出 NullPointerException。
4. 初始容量⼤⼩和每次扩充容量⼤⼩的不同 ： ①创建时如果不指定容量初始值，Hashtable 默认 的初始⼤⼩为11，之后每次扩充，容量变为原来的2n+1。HashMap 默认的初始化⼤⼩为16。之后 每次扩充，容量变为原来的2倍。②创建时如果给定了容量初始值，那么 Hashtable 会直接使⽤ 你给定的⼤⼩，⽽ HashMap 会将其扩充为2的幂次⽅⼤⼩（HashMap 中的 tableSizeFor() ⽅ 法保证，下⾯给出了源代码）。也就是说 HashMap 总是使⽤2的幂作为哈希表的⼤⼩,后⾯会介 绍到为什么是2的幂次⽅。 
5. 底层数据结构： JDK1.8 以后的 HashMap 在解决哈希冲突时有了较⼤的变化，当链表⻓度⼤于 阈值（默认为8）时，将链表转化为红⿊树，以减少搜索时间。Hashtable 没有这样的机制。 