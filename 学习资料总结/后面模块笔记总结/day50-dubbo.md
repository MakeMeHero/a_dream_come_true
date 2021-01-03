## 什么是RPC

Remote Procedure Call 远程过程调用，是分布式架构的核心，按响应方式分如下两种：

​	同步调用：客户端调用服务方方法，等待直到服务方返回结果或者超时，再继续自己的操作。

​	异步调用：客户端把消息发送给中间件，不再等待服务端返回，直接继续自己的操作。

- 是一种进程间的通信方式

- 它允许应用程序调用网络上的另一个应用程序中的方法

- 对于服务的消费者而言，无需了解远程调用的底层细节，是透明的

  ==需要注意的是RPC并不是一个具体的技术，而是指整个网络远程调用过程。==

  RPC是一个泛化的概念，严格来说一切远程过程调用手段都属于RPC范畴。各种开发语言都有自己的RPC框架。Java中的RPC框架比较多，广泛使用的有RMI、Hessian、==Dubbo==、spring Cloud等。

## Dubbo架构

![](E:\学习资料总结\后面模块笔记总结\assets/1562888790694.png)

节点角色说明：

| 节点      | 角色名称                               |
| --------- | -------------------------------------- |
| Provider  | 暴露服务的服务提供方                   |
| Consumer  | 调用远程服务的服务消费方               |
| Registry  | 服务注册与发现的注册中心               |
| Monitor   | 统计服务的调用次数和调用时间的监控中心 |
| Container | 服务运行容器                           |

虚线都是异步访问，实线都是同步访问
蓝色虚线:在启动时完成的功能
红色虚线(实线)都是程序运行过程中执行的功能

调用关系说明:

1. 服务容器负责启动，加载，运行服务提供者。

2. 服务提供者在启动时，向注册中心注册自己提供的服务。

3. 服务消费者在启动时，向注册中心订阅自己所需的服务。

4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于==长连接==推送变更数据给消费者。

5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。

6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

## 快速入门dubbo配置

服务方  spring-dubbo.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://dubbo.apache.org/schema/dubbo" xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- dubbo应用的名称-->
    <dubbo:application name="dubbo-provider"/>
    <!-- 服务注册中新的地址-->
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>

    <!--指定暴露的服务接口及实例-->
    <dubbo:service interface="com.itheima.service.UserService"
                   ref="userSerivce"/>
    <!--配置业务类实例-->
    <bean id="userSerivce"
          class="com.itheima.service.impl.UserServiceImpl"/>
</beans>
```

消费方  spring-dubbo.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!-- dubbo基本配置-->
    <dubbo:application name="dubbo-consumer"/>
    <dubbo:registry address="zookeeper://127.0.0.1:2181"/>

    <!--订阅远程服务对象，id的名称和Controller类中的UserService接口名称要一致-->
    <dubbo:reference id="userService" interface="com.itheima.service.UserService"/>

    <!--启动时不检查，即服务提供者没有启动，服务消费者启动的时候也不会报错-->
    <dubbo:consumer check="false"></dubbo:consumer>
</beans>
```

==注意：因为是RPC的框架，要求传递的参数和实体类要实现序列化==

## 6. Dubbo相关配置说明



### 【讲解】

### 6.1. 包扫描

```xml
<dubbo:annotation package="com.itheima.service" />
```

服务提供者和服务消费者前面章节实现都是基于配置文件进行服务注册与订阅，如果使用包扫描，可以使用注解方式实现，推荐使用这种方式。

#### 61.1. 服务提供者，使用注解实现

第一步：在spring-dubbo.xml中配置

```xml
<dubbo:annotation package="com.itheima.service" />
```

第二步：在HelloServiceImpl的类上使用注解：

```java
@Service
public class UserServiceImpl implements UserService {
}
```

==注意：其中@Service是dubbo包下（com.alibaba.dubbo.config.annotation.Service）的注解。表示提供服务==

#### 6.1.2. 服务消费者，使用注解实现

第一步：在spring-consumer.xml中配置

```xml
<dubbo:annotation package="com.itheima.controller" />
```

这里的dubbo注解扫描

第二步：在Controller类中使用

@Reference注解

```java
@Controller
@ResponseBody
@RequestMapping("/user")
public class UserController {

    //@Autowired
    @Reference
    UserService userService;
}
```

==注意：其中@Reference是dubbo包下（com.alibaba.dubbo.config.annotation.Reference）的注解。表示订阅服务==

### 6.2. 服务接口访问协议

```xml
<dubbo:protocol name="dubbo" port="20881"></dubbo:protocol>
```

一般在服务提供者一方配置，可以指定使用的协议名称和端口号。

其中Dubbo支持的协议有：dubbo、rmi、hessian、http、webservice、rest、redis等。

推荐使用的是dubbo协议，默认端口号：20880。

dubbo 协议采用单一长连接和 NIO 异步通讯，适合于小数据量、大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况。不适合传送大数据量的服务，比如传文件，传视频等，除非请求量很低。

也可以在同一个工程中配置多个协议，不同服务可以使用不同的协议，例如：

```xml
<!-- 多协议配置 -->
<dubbo:protocol name="dubbo" port="20881" />
<dubbo:protocol name="rmi" port="1099" />
<!-- 使用dubbo协议暴露服务 -->
<dubbo:service interface="com.itheima.service.HelloService" ref="helloService" protocol="dubbo" />
<!-- 使用rmi协议暴露服务 -->
<dubbo:service interface="com.itheima.service.DemoService" ref="demoService" protocol="rmi" />
```

也可以使用注解配置多个协议

```java
@Service(protocol = "dubbo")
public class HelloServiceImpl implements HelloService {
}
```

 

```java
@Service(protocol = "rmi")
public class DemoServiceImpl implements DemoService {
}
```

 ==dubbo协议：==

- 连接个数：单连接
- 连接方式：长连接
- 传输协议：TCP
- 传输方式：NIO异步传输
- 序列化：Hessian二进制序列化
- 适用范围：传入传出参数数据包较小（建议小于100K），消费者比提供者个数多，单一消费者无法压满提供者，尽量不要用dubbo协议传输大文件或超大字符串。
- 适用场景：常规远程服务方法调用

==rmi协议：==

- 连接个数：多连接
- 连接方式：短连接
- 传输协议：TCP
- 传输方式：同步传输
- 序列化：Java标准二进制序列化
- 适用范围：传入传出参数数据包大小混合，消费者与提供者个数差不多，可传文件。
- 适用场景：常规远程服务方法调用，与原生RMI服务互操作

详情使用可通过博客文章：<https://www.cnblogs.com/duanxz/p/3555876.html>了解

### 6.3. **启动时检查**

```xml
<dubbo:consumer check="false"/>
```

==上面这个配置需要配置在服务消费者一方==

如果设置为true，启动服务消费者，会抛出异常，表示没有服务提供者

### 6.4. 超时调用

==默认的情况下，dubbo调用的时间为一秒钟，如果超过一秒钟就会报错==

（1）修改消费者 配置文件,增加如下配置：

```xml
<!--超时时间为10秒钟-->
<dubbo:consumer timeout="10000"></dubbo:consumer>
```

（2）修改提供者配置文件，增加如下配置

```xml
<!--超时时间设置为10秒钟-->
<dubbo:provider timeout="10000"></dubbo:provider>
```

## 负载均衡

负载均衡（Load Balance）：其实就是将请求分摊到多个操作单元上进行执行，从而共同完成工作任务。

​	在集群负载均衡时，Dubbo 提供了多种均衡策略（包括随机random、轮询roundrobin、最少活跃调用数leastactive），缺省【默认】为random随机调用。

配置负载均衡策略，既可以在服务提供者一方配置，也可以在服务消费者一方配置，两者取一

- 如下在服务消费者指定负载均衡策略，可在@Reference添加@Reference(loadbalance = "roundrobin")

  ```java
  @RestController
  @RequestMapping("/user")
  public class UserController {
  
  	@Reference(loadbalance = "roundrobin")
  	private UserSerivce userService;
  ```

