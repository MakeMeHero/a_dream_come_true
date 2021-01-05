
Mybatis 仅⽀持 association 关联对象和 collection 关联集合对象的延迟加载，association
指的就是⼀对⼀，collection 指的就是⼀对多查询。在 Mybatis 配置⽂件中，可以配置是否启⽤延迟
加载 lazyLoadingEnabled=true|false。
它的原理是，使⽤ CGLIB 创建⽬标对象的代理对象，当调⽤⽬标⽅法时，进⼊拦截器⽅法，⽐如调⽤
a.getB().getName() ，拦截器 invoke() ⽅法发现 a.getB() 是 null 值，那么就会单独发送事
先保存好的查询关联 B 对象的 sql，把 B 查询上来，然后调⽤ a.setB(b)，于是 a 的对象 b 属性就
有值了，接着完成 a.getB().getName() ⽅法的调⽤。这就是延迟加载的基本原理。
当然了，不光是 Mybatis，⼏乎所有的包括 Hibernate，⽀持延迟加载的原理都是⼀样的。


