## zookeeper是什么

ZooKeeper从字面意思理解，【Zoo ­ 动物园，Keeper ­ 管理员】动物园中有很
多种动物，这里的动物就可以比作分布式环境下多种多样的服务，而ZooKeeper
做的就是管理这些服务。
   ZooKeeper的系统为分布式的是用来构建分布式应用的高性能服务。
   ZooKeeper 本质上是一个分布式的小文件存储系统==。提供基于类似于文
件系统的目录树方式的数据存储，并且可以对树中的节点进行有效管理。从而用
来维护和监控你存储的数据的状态变化。通过监控这些数据状态的变化，从而可
以达到基于数据的集群管理。
   ZooKeeper 适用于存储和协同相关的关键数据，不适合用于大数据量存
储

## zookeeper数据结构

层次模型和key-value 模型是两种主流的数据模型。ZooKeeper 使用文件系统模型主要基于以下两点考虑：

1. 文件系统的树形结构便于表达数据之间的层次关系。
2. 文件系统的树形结构便于为不同的应用分配独立的命名空间（namespace）。
## 节点的分类

一：一个znode可以是持久性的，也可以是临时性的

1. 持久性znode[PERSISTENT]，这个znode一旦创建不会丢失，无论是zookeeper宕机，还是client宕机。
2. 临时性的znode[EPHEMERAL]，如果zookeeper宕机了，或者client在指定的timeout时间内没有连接server，都会被认为丢失。

二：znode也可以是顺序性的，每一个顺序性的znode关联一个唯一的单调递增整数。这个单调递增整数是znode名字的后缀。

1. 持久顺序性的znode(PERSISTENT_SEQUENTIAL):znode 处理具备持久性的znode的特点之外，znode的名称具备顺序性。
2. 临时顺序性的znode(EPHEMERAL_SEQUENTIAL):znode处理具备临时性的znode特点，znode的名称具备顺序性。
## zookeeper  的java  Api介绍

- **Apache Curator（推荐使用）**

​       Apache Curator是 Apache ZooKeeper的Java客户端库。 

​       Curator.项目的目标是简化ZooKeeper客户端的使用。

​       例如,在dubbo以前的代码展示中,我们都要自己处理ConnectionLossException. 

​               在dubbo以前的开发中，我们需要处理事务失效问题.

​               而现在都可以进行解决  

​      另外 Curator为常见的分布式协同服务提供了高质量的实现。
​      Apache Curator最初是Netfix研发的,后来捐献了 Apache基金会,目前是 Apache的顶级项目

##### 3.3.3、创建节点

```java
//1. 创建一个空节点(a)（只能创建一层节点）
//2. 创建一个有内容的b节点（只能创建一层节点）
//3. 创建多层节点
//4. 创建带有的序号的节点
//5. 创建临时节点（客户端关闭，节点消失），设置延时5秒关闭（Thread.sleep(5000)）
//6. 创建临时带序号节点（客户端关闭，节点消失），设置延时5秒关闭（Thread.sleep(5000)）
```



```java
/**
 *  RetryPolicy： 失败的重试策略的公共接口
 *  ExponentialBackoffRetry是 公共接口的其中一个实现类
 *      参数1： 初始化sleep的时间，用于计算之后的每次重试的sleep时间
 *      参数2：最大重试次数
        参数3（可以省略）：最大sleep时间，如果上述的当前sleep计算出来比这个大，那么sleep用这个时间
 */
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000,3,10);
//创建客户端
/**
 * 参数1：连接的ip地址和端口号
 * 参数2：会话超时时间，单位毫秒
 * 参数3：连接超时时间，单位毫秒
 * 参数4：失败重试策略
 */
CuratorFramework client =  CuratorFrameworkFactory.newClient("127.0.0.1:2181",3000,1000,retryPolicy);
//开启客户端(会阻塞到会话连接成功为止)
client.start();
/**
 * 创建节点
 */
//1. 创建一个空节点(a)（只能创建一层节点）
// client.create().forPath("/a");
//2. 创建一个有内容的b节点（只能创建一层节点）
// client.create().forPath("/b", "这是b节点的内容".getBytes());
//3. 创建多层节点
// （creatingParentsIfNeeded）是否需要递归创建节点
// withMode(CreateMode.PERSISTENT)  创建持久性 b节点
// client.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT).forPath("/g");
//4. 创建带有的序号的节点
//  client.create().creatingParentsIfNeeded().withMode(CreateMode.PERSISTENT_SEQUENTIAL).forPath("/e");
//5. 创建临时节点（客户端关闭，节点消失），设置延时5秒关闭
// client.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL).forPath("/f");
//6. 创建临时带序号节点（客户端关闭，节点消失），设置延时5秒关闭
client.create().creatingParentsIfNeeded().withMode(CreateMode.EPHEMERAL_SEQUENTIAL).forPath("/f");
Thread.sleep(5000);
//关闭客户端
client.close();
```

##### 3.3.4、修改节点数据

```java
//创建失败策略对象
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000,1);
//
CuratorFramework client = CuratorFrameworkFactory.newClient("127.0.0.1:2181",1000,1000,retryPolicy);
client.start();
//修改节点
client.setData().forPath("/a/b", "abc".getBytes());
client.close();
```

##### 3.3.5、节点数据查询

```java
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 1);
CuratorFramework client = 
        CuratorFrameworkFactory.newClient("127.0.0.1",1000,1000, retryPolicy);
client.start();
// 查询节点数据
byte[] bytes = client.getData().forPath("/a/b");
System.out.println(new String(bytes));
```

##### 3.3.6、删除节点

```java
//重试策略
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000,1);
//创建客户端
CuratorFramework client =
        CuratorFrameworkFactory.newClient("127.0.0.1",1000,1000, retryPolicy);
//启动客户端
client.start();
//删除一个子节点
  client.delete().forPath("/a");
// 删除节点并递归删除其子节点
  client.delete().deletingChildrenIfNeeded().forPath("/a");
//强制保证删除一个节点
//只要客户端会话有效，那么Curator会在后台持续进行删除操作，直到节点删除成功。
// 比如遇到一些网络异常的情况，此guaranteed的强制删除就会很有效果。
client.delete().guaranteed().deletingChildrenIfNeeded().forPath("/a");
//关闭客户端
client.close();
```

##### 3.4.1、什么是watch机制

​	zookeeper作为一款成熟的分布式协调框架，订阅-发布功能是很重要的一个。所谓订阅发布功能，其实说白了就是观察者模式。观察者会订阅一些感兴趣的主题，然后这些主题一旦变化了，就会自动通知到这些观察者。

​	zookeeper的订阅发布也就是watch机制，是一个轻量级的设计。因为它采用了一种推拉结合的模式。一旦服务端感知主题变了，那么只会发送一个事件类型和节点信息给关注的客户端，而不会包括具体的变更内容，所以事件本身是轻量级的，这就是所谓的“推”部分。然后，收到变更通知的客户端需要自己去拉变更的数据，这就是“拉”部分。watche机制分为添加数据和监听节点。

​	Curator在这方面做了优化，Curator引入了Cache的概念用来实现对ZooKeeper服务器端进行事件监听。Cache是Curator对事件监听的包装，其对事件的监听可以近似看做是一个本地缓存视图和远程ZooKeeper视图的对比过程。而且Curator会自动的再次监听，我们就不需要自己手动的重复监听了。

Curator中的cache共有三种

- NodeCache
- PathChildrenCache
- TreeCache

下面我们分别对三种cache详解

##### 3.4.2、 NodeCache

- **介绍**

  NodeCache是用来监听本节点的数据变化的，当监听的节点的数据发生变化的时候就会回调对应的函数。

##### 3.4.3、 PathChildrenCache

- **介绍**

  PathChildrenCache是用来监听指定节点 的子节点变化情况

##### 3.4.4、TreeCache

- **介绍**

  TreeCache有点像上面两种Cache的结合体，NodeCache能够监听自身节点的数据变化（或者是创建该节点），PathChildrenCache能够监听自身节点下的子节点的变化，而TreeCache既能够监听自身节点的变化、也能够监听子节点的变化。

- **添加监听**

```java
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000,1);
CuratorFramework client = CuratorFrameworkFactory.newClient("127.0.0.1:2181", 1000, 1000, retryPolicy);
client.start();
TreeCache treeCache = new TreeCache(client,"/hello");
treeCache.start();
System.out.println(treeCache.getCurrentData("/hello"));
treeCache.getListenable().addListener(new TreeCacheListener() {
    @Override
    public void childEvent(CuratorFramework client, TreeCacheEvent event) throws Exception {
            if(event.getType() == TreeCacheEvent.Type.NODE_ADDED){
                System.out.println(event.getData().getPath() + "节点添加");
            }else if (event.getType() == TreeCacheEvent.Type.NODE_REMOVED){
                System.out.println(event.getData().getPath() + "节点移除");
            }else if(event.getType() == TreeCacheEvent.Type.NODE_UPDATED){
                System.out.println(event.getData().getPath() + "节点修改");
            }else if(event.getType() == TreeCacheEvent.Type.INITIALIZED){
                System.out.println("初始化完成");
            }else if(event.getType() == TreeCacheEvent.Type.CONNECTION_SUSPENDED){
                System.out.println("连接过时");
            }else if(event.getType() == TreeCacheEvent.Type.CONNECTION_RECONNECTED){
                System.out.println("重新连接");
            }else if(event.getType() == TreeCacheEvent.Type.CONNECTION_LOST){
                System.out.println("连接过时一段时间");
            }
    }
});
System.in.read();
```

### 