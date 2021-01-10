#### 6.2.1 Hystrix 简介

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

#### 6.2.3 熔断原理分析

熔断器状态机有3个状态：

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

**翻译之后的图：**

![1563120878233](../../../daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563120878233.png)



**熔断器的核心：线程隔离和服务降级。**

```properties
1.线程隔离：是指Hystrix为每个依赖服务调用一个小的线程池，如果线程池用尽，调用立即被拒绝，默认不采用排队。
2.服务降级(兜底方法)：优先保证核心服务，而非核心服务不可用或弱可用。触发Hystrix服务降级的情况：线程池已满、请求超时。
```

线程隔离和服务降级之后，用户请求故障时，线程不会被阻塞，更不会无休止等待或者看到系统奔溃，至少可以看到执行结果(熔断机制)。

##### 6.2.4 局部熔断案例

启动类上添加`@EnableCircuitBreaker`

在`user-consumer`的`com.itheima.controller.UserController`中添加降级处理方法，方法如下：

```java
/****
 * 服务降级处理方法
 * @return
 */
public User failBack(Integer id){
    User user = new User();
    user.setUsername("服务降级,默认处理！");
    return  user;
}
```

在有可能发生问题的方法上添加降级处理调用，例如在`queryById`方法上添加降级调用，代码如下：

![1563122266205](../../../daybyday/02Web/day69%20SpringCloud/%E8%AE%B2%E4%B9%89/images/1563122266205.png)

#### 6.2.5 其他熔断策略配置

```properties
1. 熔断后休眠时间：sleepWindowInMilliseconds
2. 熔断触发最小请求次数：requestVolumeThreshold
3. 熔断触发错误比例阈值：errorThresholdPercentage
4. 熔断超时时间：timeoutInMilliseconds
```

配置如下：

```yml
# 配置熔断策略：
hystrix:
  command:
    default:
      circuitBreaker:
        # 强制打开熔断器 默认false关闭的。测试配置是否生效
        forceOpen: false
        # 触发熔断错误比例阈值，默认值50%
        errorThresholdPercentage: 50
        # 熔断后休眠时长，默认值5秒
        sleepWindowInMilliseconds: 10000
        # 熔断触发最小请求次数，默认值是20
        requestVolumeThreshold: 10
      execution:
        isolation:
          thread:
            # 熔断超时设置，默认为1秒
            timeoutInMilliseconds: 2000
```

#### 6.2.5 扩展-服务降级的fallback方法(全局熔断)：

<!--注意：因为熔断的降级逻辑方法跟正常逻辑方法一样，必须保证相同的参数列表和返回值相同-->

两种编写方式：编写在类上，编写在方法上。在类的上边对类的所有方法都生效。在方法上，仅对当前方法有效。

(1)方法上服务降级的fallback兜底方法

```properties
使用HystrixCommon注解，定义
@HystrixCommand(fallbackMethod="failBack")用来声明一个降级逻辑的fallback兜底方法
```

(2)类上默认服务降级的fallback兜底方法

```properties
刚才把fallback写在了某个业务方法上，如果方法很多，可以将FallBack配置加在类上，实现默认FallBack
@DefaultProperties(defaultFallback=”defaultFailBack“)，在类上，指明统一的失败降级方法；
```

