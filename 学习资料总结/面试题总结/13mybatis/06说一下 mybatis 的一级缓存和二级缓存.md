一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。 

二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储 源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属 性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的 映射文件中配置 

EhCache 是一个纯Java的进程内缓存框架 