### 1.2 讲解

#### 1.2.1 Feign简介

Feign [feɪn] 译文 伪装。Feign是一个声明式WebService客户端.使用Feign能让编写WebService客户端更加简单,它的使用方法是定义一个接口，然后在上面添加注解。不再需要拼接URL，参数等操作。项目主页：https://github.com/OpenFeign/feign 。

- 集成Ribbon的负载均衡功能

- 集成了Hystrix的熔断器功能

- 支持请求压缩

- 大大简化了远程调用的代码，同时功能还增强啦

- Feign以更加优雅的方式编写远程调用代码，并简化重复代码

#### 1.2.2 快速入门

1.在`user-consumer`中创建`com.itheima.feign.UserClient`接口添加@FeignClient(value="微服务名字")

2.(3)编写控制层

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

修改`user-consumer`的启动类，在启动类上添加`@EnableFeignClients`注解，开启Feign,

Feign内置的ribbon默认设置了请求超时时长，默认是1000，可以修改

ribbon内部有重试机制，一旦超时，会自动重新发起请求。如果不希望重试可以关闭配置：

```yml
# 修改服务地址轮询策略，默认是轮询，配置之后变随机
user-provider:
  ribbon:
    #轮询
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RoundRobinRule
    ConnectTimeout: 10000 # 连接超时时间
    ReadTimeout: 2000 # 数据读取超时时间
    MaxAutoRetries: 1 # 最大重试次数(第一个服务)
    MaxAutoRetriesNextServer: 0 # 最大重试下一个服务次数(集群的情况才会用到)
    OkToRetryOnAllOperations: false # 无论是请求超时或者socket read timeout都进行重试
```



#### 1.2.4 熔断器支持

feign整合Hystrix熔断器

Feign默认也有对Hystrix的集成!

![1563263001726](../../../daybyday/02Web/day70%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563263001726.png)

实现步骤：

```properties
1. 在配置文件application.yml中开启feign熔断器支持
2. 编写FallBack处理类，实现FeignClient客户端
3. 在@FeignClient注解中，指定FallBack处理类。
4. 测试
```



(1)开启Hystrix

在配置文件application.yml中开启feign熔断器支持：默认关闭

```properties
feign:
  hystrix:
    enabled: true # 开启Feign的熔断功能
```

(2)熔断降级类创建

修改`user-consumer`,创建一个类`com.itheima.feign.fallback.UserClientFallback`，实现刚才编写的UserClient，作为FallBack的处理类,代码如下：

```java
@Component
public class UserClientFallback implements UserClient{

    /***
     * 服务降级处理方法
     * @param id
     * @return
     */
    @Override
    public User findById(Integer id) {
        User user = new User();
        user.setUsername("Fallback，服务降级。。。");
        return user;
    }
}
```



(3)指定Fallback处理类

在@FeignClient注解中，指定FallBack处理类

![1563263626561](../../../daybyday/02Web/day70%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563263626561.png)

#### 1.2.6 Feign的日志级别配置

通过loggin.level.xx=debug来设置日志级别。然而这个对Feign客户端不会产生效果。因为@FeignClient注解修饰的客户端在被代理时，都会创建一个新的Feign.Logger实例。我们需要额外通过配置类的方式指定这个日志的级别才可以。

**实现步骤：**

```properties
1. 在application.yml配置文件中开启日志级别配置
2. 编写配置类，定义日志级别bean。
3. 在接口的@FeignClient中指定配置类
4. 重启项目，测试访问
```



**实现过程：**

(1)普通日志等级配置

在`user-consumer`的配置文件中设置com.itheima包下的日志级别都为debug

```properties
# com.itheima 包下的日志级别都为Debug
logging:
  level:
    com.itheima: debug
```

(2)Feign日志等级配置

在`user-consumer`中创建`com.itheima.feign.util.FeignConfig`,定义日志级别

```java
@Configuration
public class FeignConfig {

    /***
     * 日志级别
     * @return
     */
    @Bean
    public Logger.Level feignLoggerLevel(){
        return Logger.Level.FULL;
    }
}
```

日志级别说明：

```properties
Feign支持4中级别：
	NONE：不记录任何日志，默认值
	BASIC：仅记录请求的方法，URL以及响应状态码和执行时间
	HEADERS：在BASIC基础上，额外记录了请求和响应的头信息
	FULL：记录所有请求和响应的明细，包括头信息、请求体、元数据
```



(3)指定配置类

修改`user-consumer`的`com.itheima.feign.UserClient`指定上面的配置类，代码如下：

![1563267991954](../../../daybyday/02Web/day70%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563267991954.png)

