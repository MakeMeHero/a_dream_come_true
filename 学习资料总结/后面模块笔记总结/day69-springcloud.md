## 服务调用方式

常见远程调用方式：

RPC:(Remote Produce Call)远程过程调用

```properties
1.基于Socket
2.自定义数据格式
3.速度快，效率高
4.典型应用代表：Dubbo，WebService，ElasticSearch集群间互相调用
```



HTTP：网络传输协议

```properties
1.基于TCP/IP
2.规定数据传输格式
3.缺点是消息封装比较臃肿、传输速度比较慢
4.优点是对服务提供和调用方式没有任何技术限定，自由灵活，更符合微服务理念
```

RPC和HTTP的区别：RPC是根据语言API来定义，而不是根据基于网络的应用来定义。

Http客户端工具

常见Http客户端工具：HttpClient、OKHttp、URLConnection。

## Eureka 

![](E:\学习资料总结\后面模块笔记总结\assets/1563089431796.png)

Eureka：就是服务注册中心(可以是一个集群)，对外暴露自己的地址

服务提供者：启动后向Eureka注册自己的信息(地址，提供什么服务)

服务消费者：向Eureka订阅服务，Eureka会将对应服务的所有提供者地址列表发送给消费者，并且定期更新

心跳(续约)：提供者定期通过http方式向Eureka刷新自己的状态

#### 4.2.4 Eureka详解

##### 4.2.4.1 基础架构

Eureka架构中的三个核心角色

```properties
1.服务注册中心：Eureka服务端应用，提供服务注册发现功能，eureka-server
2.服务提供者：提供服务的应用
  要求统一对外提供Rest风格服务即可
  本例子：user-provider
3.服务消费者：从注册中心获取服务列表，知道去哪调用服务方，user-consumer
```



##### 4.2.4.2 Eureka客户端

服务提供者要向EurekaServer注册服务，并完成服务续约等工作

**服务注册:**

```properties
1. 当我们开启了客户端发现注解@DiscoveryClient。同时导入了eureka-client依赖坐标
2. 同时配置Eureka服务注册中心地址在配置文件中
3. 服务在启动时，检测是否有@DiscoveryClient注解和配置信息
4. 如果有，则会向注册中心发起注册请求，携带服务元数据信息(IP、端口等)
5. Eureka注册中心会把服务的信息保存在Map中。
```



**服务续约：**

服务注册完成以后，服务提供者会维持一个`心跳`，保存服务处于存在状态。这个称之为服务续约(renew)。

![1563102805802](E:/daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563102805802.png)

上图配置如下：

```yml
#租约到期，服务时效时间，默认值90秒
lease-expiration-duration-in-seconds: 150
#租约续约间隔时间，默认30秒
lease-renewal-interval-in-seconds: 30
```

参数说明：

```properties
1.两个参数可以修改服务续约行为
  lease-renewal-interval-seconds:90，租约到期时效时间，默认90秒
  lease-expiration-duration-in-seconds:30，租约续约间隔时间，默认30秒
2.服务超过90秒没有发生心跳，EurekaServer会将服务从列表移除[前提是EurekaServer关闭了自我保护]
```



**获取服务列表：**

![1563102910373](E:/daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563102910373.png)

上图配置如下：

```yml
registry-fetch-interval-seconds: 30
```

说明：

```properties
服务消费者启动时，会检测是否获取服务注册信息配置
如果是，则会从 EurekaServer服务列表获取只读备份，缓存到本地
每隔30秒，会重新获取并更新数据
每隔30秒的时间可以通过配置registry-fetch-interval-seconds修改
```



##### 4.2.4.3 失效剔除和自我保护

**服务下线：**

```
当服务正常关闭操作时，会发送服务下线的REST请求给EurekaServer。
服务中心接受到请求后，将该服务置为下线状态

```



**失效剔除：**

```
服务中心每隔一段时间(默认60秒)将清单中没有续约的服务剔除。
通过eviction-interval-timer-in-ms配置可以对其进行修改，单位是毫秒

```

剔除时间配置

![1563103529735](E:/daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563103529735.png)

上图代码如下：

```properties
eviction-interval-timer-in-ms: 5000
```



**自我保护：**

Eureka会统计服务实例最近15分钟心跳续约的比例是否低于85%，如果低于则会触发自我保护机制。

服务中心页面会显示如下提示信息

![1558056004897](E:/daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1558056004897.png)

含义：紧急情况！Eureka可能错误地声称实例已经启动，而事实并非如此。续约低于阈值，因此实例不会为了安全而过期。

```properties
1.自我保护模式下，不会剔除任何服务实例
2.自我保护模式保证了大多数服务依然可用
3.通过enable-self-preservation配置可用关停自我保护，默认值是打开
```

关闭自我保护

![1563103437025](E:/daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563103437025.png)

上图配置如下：

```properties
enable-self-preservation: false
```

## 负载均衡 Spring Cloud Ribbon

什么是Ribbon？

Ribbon是Netflix发布的负载均衡器，有助于控制HTTP客户端行为。为Ribbon配置服务提供者地址列表后，Ribbon就可基于负载均衡算法，自动帮助服务消费者请求。

Ribbon默认提供的负载均衡算法：轮询，随机,重试法,加权。当然，我们可用自己定义负载均衡算法

##### 5.2.2.2 开启负载均衡

(1)客户端开启负载均衡

Eureka已经集成Ribbon，所以无需引入依赖,要想使用Ribbon，直接在RestTemplate的配置方法上添加`@LoadBalanced`注解即可

修改`user-consumer`的`com.itheima.UserConsumerApplication`启动类，在`restTemplate()`方法上添加`@LoadBalanced`注解，代码如下：

![1563112003073](E:/daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563112003073.png)

##### 5.2.2.3 其他负载均衡策略配置

配置修改轮询策略：Ribbon默认的负载均衡策略是轮询，通过如下

```yml
# 修改服务地址轮询策略，默认是轮询，配置之后变随机
user-provider:
  ribbon:
    #轮询
    #NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RoundRobinRule
    #随机算法
    #NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
    #重试算法,该算法先按照轮询的策略获取服务,如果获取服务失败则在指定的时间内会进行重试，获取可用的服务
    #NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RetryRule
    #加权法,会根据平均响应时间计算所有服务的权重，响应时间越快服务权重越大被选中的概率越大。刚启动时如果同统计信息不足，则使用轮询的策略，等统计信息足够会切换到自身规则。
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.ZoneAvoidanceRule
```

SpringBoot可以修改负载均衡规则，配置为`ribbon.NFLoadBalancerRuleClassName`

格式{服务名称}.ribbon.NFLoadBalancerRuleClassName

##Hystrix 简介

![hystrix-logo-tagline-640](E:/daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/hystrix-logo-tagline-640.png)

Hystrix，英文意思是豪猪，全身是刺，刺是一种保护机制。Hystrix也是Netflix公司的一款组件。

Hystrix的作用是什么？

Hystrix是Netflix开源的一个延迟和容错库，用于隔离访问远程服务、第三方库、防止出现级联失败也就是雪崩效应。



#### 6.2.2 雪崩效应

什么是雪崩效应？

```properties
1.微服务中，一个请求可能需要多个微服务接口才能实现，会形成复杂的调用链路。
2.如果某服务出现异常，请求阻塞，用户得不到响应，容器中线程不会释放，于是越来越多用户请求堆积，越来越多线程阻塞。
3.单服务器支持线程和并发数有限，请求如果一直阻塞，会导致服务器资源耗尽，从而导致所有其他服务都不可用，从而形成雪崩效应；
```

Hystrix解决雪崩问题的手段，主要是服务降级**(兜底)**，线程隔离；

#### 熔断器状态机有3个状态：

```properties
1.Closed：关闭状态，所有请求正常访问
2.Open：打开状态，所有请求都会被降级。
  Hystrix会对请求情况计数，当一定时间失败请求百分比达到阈(yu：四声)值(极限值)，则触发熔断，断路器完全关闭
  默认失败比例的阈值是50%，请求次数最低不少于20次
3.Half Open：半开状态
  Open状态不是永久的，打开一会后会进入休眠时间(默认5秒)。休眠时间过后会进入半开状态。
  半开状态：熔断器会判断下一次请求的返回状况，如果成功，熔断器切回closed状态。如果失败，熔断器切回open状态。
threshold reached 到达阈(yu：四声)值
under threshold  阈值以下
```

![](E:\学习资料总结\后面模块笔记总结\assets/1563120878233.png)

**熔断器的核心：线程隔离和服务降级。**

```properties
1.线程隔离：是指Hystrix为每个依赖服务调用一个小的线程池，如果线程池用尽，调用立即被拒绝，默认不采用排队。
2.服务降级(兜底方法)：优先保证核心服务，而非核心服务不可用或弱可用。触发Hystrix服务降级的情况：线程池已满、请求超时。
```

线程隔离和服务降级之后，用户请求故障时，线程不会被阻塞，更不会无休止等待或者看到系统奔溃，至少可以看到执行结果(熔断机制)。

(2)开启熔断的注解

修改`user-consumer`的`com.itheima.UserConsumerApplication`,在该类上添加`@EnableCircuitBreaker`

#### 6.2.5 扩展-服务降级的fallback方法：

<!--注意：因为熔断的降级逻辑方法跟正常逻辑方法一样，必须保证相同的参数列表和返回值相同-->

两种编写方式：编写在类上，编写在方法上。在类的上边对类的所有方法都生效。在方法上，仅对当前方法有效。

- 能实现一个局部方法熔断案例

  ```properties
  1.定义一个局部处理熔断的方法failBack()
  2.在指定方法上使用@HystrixCommand(fallbackMethod = "failBack")配置调用
  ```

- 能实现全局方法熔断案例

  ```properties
  1.定义一个全局处理熔断的方法defaultFailBack()
  2.在类上使用@DefaultProperties(defaultFallback = "defaultFailBack")配置调用
  3.在指定方法上使用@HystrixCommand
  ```

##Feign

Feign [feɪn] 译文 伪装。Feign是一个声明式WebService客户端.使用Feign能让编写WebService客户端更加简单,它的使用方法是定义一个接口，然后在上面添加注解。不再需要拼接URL，参数等操作。

#### 1.2.2 快速入门

使用Feign替代RestTemplate发送Rest请求。

**实现步骤：**

```properties
1. 导入feign依赖
2. 编写Feign客户端接口
3. 消费者启动引导类开启Feign功能注解
4. 访问接口测试
```



**实现过程：**

(1)导入依赖

在`user-consumer`中添加`spring-cloud-starter-openfeign`依赖

```xml
<!--配置feign-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```



(2)创建Feign客户端

在`user-consumer`中创建`com.itheima.feign.UserClient`接口，代码如下：

![1563260831222](E:/daybyday/02Web/day70%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563260831222.png)



解释：

```properties
Feign会通过动态代理，帮我们生成实现类。
注解@FeignClient声明Feign的客户端，注解value指明服务名称
接口定义的方法，采用SpringMVC的注解。Feign会根据注解帮我们生成URL地址
注解@RequestMapping中的/user，不要忘记。因为Feign需要拼接可访问地址
```



(3)编写控制层

在`user-consumer`中创建`com.itheima.controller.ConsumerFeignController`，在Controller中使用@Autowired注入FeignClient,代码如下：

```java
@RestController
@RequestMapping(value = "/feign")
public class ConsumerFeignController {

    @Autowired
    private UserClient userClient;

    /****
     * 使用Feign调用user-provider的方法
     */
    @RequestMapping(value = "/{id}")
    public User queryById(@PathVariable(value = "id")Integer id){
        return userClient.findById(id);
    }
}
```



(4)开启Feign

修改`user-consumer`的启动类，在启动类上添加`@EnableFeignClients`注解，开启Feign,代码如下：

![1563261614474](E:\学习资料总结\后面模块笔记总结\assets/1563261614474.png)

Feign自身已经集成了Ribbon   

feign已整合Hystrix熔断器

## Spring Cloud Gateway

Gateway 简介

==旨在为微服务架构提供一种简单有效统一的API路由管理方式。==

==基于Filter链的方式提供网关的基本功能。例如：安全，监控/指标，和限流。==

**网关的核心功能：**过滤、路由

```yaml
spring:
  application:
    # 应用名
    name: api-gateway
  cloud:
    gateway:
      routes:
        #id唯一标识，可自定义
        - id: user-service-route
          #路由的服务地址
          #uri: http://localhost:18081
          #lb协议表示从Eureka注册中心获取服务请求地址
          #user-provider访问的服务名称。
          #路由地址如果通过lb协议加服务名称时，会自动使用负载均衡访问对应服务
          uri: lb://user-provider
          # 路由拦截的地址配置（断言）
          predicates:
            - Path=/**
          filters:
            # 请求地址添加路径前缀过滤器
            #- PrefixPath=/user
            # 去除路径前缀过滤器
            - StripPrefix=1
      default-filters:
        - AddResponseHeader=X-Response-Default-MyName,itheima
```

除此之外可以自定义全局过滤器与局部过滤器

- 网关的作用

  ```properties
  1.为微服务架构提供一种简单有效统一的API路由管理方式
  2.可以在网关中实现微服务鉴权、安全控制、请求监控、限流
  ```

##Spring Cloud Config

Config简介

分布式系统中，由于服务数量非常多，配置文件分散在不同微服务项目中，管理极其不方便。为了方便配置文件集中管理，需要分布式配置中心组件。在Spring Cloud中，提供了Spring Cloud Config，它支持配置文件放在配置服务的本地，也支持配置文件放在远程仓库Git(GitHub、码云)。配置中心本质上是一个微服务，同样需要注册到Eureka服务中心！

@EnableConfigServer    //在启动类开启配置服务支持

服务去获取配置中心配置

删除user-provider工程的application.yml文件

创建user-provider工程bootstrap.yml配置文件，配置内容如下

关于application.yml和bootstrap.yml文件的说明：

```
- bootstrap.yml文件是SpringBoot的默认配置文件，而且其加载时间相比于application.yml更早。
- bootstrap.yml和application.yml都是默认配置文件，但定位不同
  - bootstrap.yml可以理解成系统级别的一些参数配置，一般不会变动
  - application.yml用来定义应用级别的参数
- 搭配spring-cloud-config使用application.yml的配置可以动态替换。
- bootstrap.yml相当于项目启动的引导文件，内容相对固定
- application.yml文件是微服务的常规配置参数，变化比较频繁

```

配置中心存在的问题: 修改码云上的配置后，发现项目中的数据仍然没有变化,只有项目重启后才会变化。

## 消息总线 Spring Cloud Bus

SpringCloud Bus，解决上述问题，实现配置自动更新。

注意：SpringCloudBus基于RabbitMQ实现，默认使用本地的消息队列服务，所以需要提前安装并启动RabbitMQ。

Bus简介

Bus是用轻量的消息代理将分布式的节点连接起来，可以用于**广播配置文件的更改**或者服务的监控管理。

Bus可以为微服务做监控，也可以实现应用程序之间互相通信。Bus可选的消息代理**RabbitMQ**和Kafka。

广播出去的配置文件服务会进行本地缓存。

(3)添加刷新配置

修改`user-provider`的`com.itheima.controller.LoadConfigController`，添加一个`@RefreshScope`注解刷新配置信息，代码如下：

![1563278214919](E:/daybyday/02Web/day70%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563278214919.png)

@RefreshScope：用于启用刷新配置文件的信息。