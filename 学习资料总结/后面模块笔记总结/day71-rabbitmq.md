## 1. 消息队列概述

### 1.1. 消息队列MQ

MQ全称为Message Queue，消息队列是应用程序和应用程序之间的通信方法。

 为什么使用MQ

```properties
在项目中，可将一些无需即时返回且耗时的操作提取出来，进行异步处理，而这种异步处理的方式大大的节省了服务器的请求响应时间，从而提高了系统的吞吐量。
```

开发中消息队列通常有如下应用场景：

```properties
1、任务异步处理
	将不需要同步处理的并且耗时长的操作由消息队列通知消息接收方进行异步处理。提高了应用程序的响应时间。
	
2、应用程序解耦合
	MQ相当于一个中介，生产方通过MQ与消费方交互，它将应用程序进行解耦合。
```

#### 1.2.3. AMQP 与 JMS 区别

- JMS是定义了统一的接口，来对消息操作进行统一；AMQP是通过规定协议来统一数据交互的格式

- JMS限定了必须使用Java语言；AMQP只是协议，不规定实现方式，因此是跨语言的。

- JMS规定了两种消息模式；而AMQP的消息模式更加丰富

  ```
  JMS
  ①订阅模式
  ②点对点消息模式
  ```

###1.4. RabbitMQ

RabbitMQ提供了6种模式：简单模式，work模式，Publish/Subscribe发布与订阅模式，Routing路由模式，Topics主题模式，RPC远程调用模式（远程调用，不太算MQ；不作介绍）；

### 3.4. 简单模式

上述的入门案例中中其实使用的是如下的简单模式：

![1555991074575](E:/daybyday/02Web/day71%20%20RabbitMQ/%E8%AE%B2%E4%B9%89/assets/1555991074575.png)

在上图的模型中，有以下概念：

```properties
P：生产者，也就是要发送消息的程序
C：消费者：消息的接受者，会一直等待消息到来。
queue：消息队列，图中红色部分。类似一个邮箱，可以缓存消息；生产者向其中投递消息，消费者从其中取出消息。
```

在rabbitMQ中消息者是一定要到某个消息队列中去获取消息的



## 4. RabbitMQ工作模式

### 4.1. Work queues工作队列模式

#### 4.1.1. 模式说明

![1562516450360](E:/daybyday/02Web/day71%20%20RabbitMQ/%E8%AE%B2%E4%B9%89/assets/1562516450360.png)

`Work Queues`与入门程序的`简单模式`相比，多了一个或一些消费端，多个消费端共同消费同一个队列中的消息。

**应用场景**：对于 任务过重或任务较多情况使用工作队列可以提高任务处理的速度。

#### 4.1.4. 小结

在一个队列中如果有多个消费者，那么消费者之间对于同一个消息的关系是**竞争**的关系。

### 4.2. 订阅模式类型

订阅模式示例图：

![1556014499573](E:/daybyday/02Web/day71%20%20RabbitMQ/%E8%AE%B2%E4%B9%89/assets/1556014499573.png)

前面2个案例中，只有3个角色：

```properties
P：生产者，也就是要发送消息的程序
C：消费者：消息的接受者，会一直等待消息到来。
Queue：消息队列，图中红色部分
```

而在订阅模型中，多了一个exchange角色，而且过程略有变化：

### 4.3. Publish/Subscribe发布与订阅模式

#### 4.3.1. 模式说明

![1562518625240](E:/daybyday/02Web/day71%20%20RabbitMQ/%E8%AE%B2%E4%B9%89/assets/1562518625240.png)

发布订阅模式：

```properties
1.每个消费者监听自己的队列。
2.生产者将消息发给broker，由交换机将消息转发到绑定此交换机的每个队列，每个绑定交换机的队列都将接收
到消息
```

### 4.4. Routing路由模式

#### 4.4.1. 模式说明

路由模式特点：

```properties
1.队列与交换机的绑定，不能是任意绑定了，而是要指定一个RoutingKey（路由key）
2.消息的发送方在 向 Exchange发送消息时，也必须指定消息的 RoutingKey。
3.Exchange不再把消息交给每一个绑定的队列，而是根据消息的Routing Key进行判断，只有队列的Routingkey与消息的 Routing key完全一致，才会接收到消息
```

![1562522054280](E:/daybyday/02Web/day71%20%20RabbitMQ/%E8%AE%B2%E4%B9%89/assets/1562522054280.png)

图解：

```properties
P：生产者，向Exchange发送消息，发送消息时，会指定一个routing key。
X：Exchange（交换机），接收生产者的消息，然后把消息递交给 与routing key完全匹配的队列
C1：消费者，其所在队列指定了需要routing key 为 error 的消息
C2：消费者，其所在队列指定了需要routing key 为 info、error、warning 的消息
```

### 4.5. Topics通配符模式

#### 4.5.1. 模式说明

![1562524697140](E:/daybyday/02Web/day71%20%20RabbitMQ/%E8%AE%B2%E4%B9%89/assets/1562524697140.png)

`Topic`类型与`Direct`相比，都是可以根据`RoutingKey`把消息路由到不同的队列。只不过`Topic`类型`Exchange`可以让队列在绑定`Routing key` 的时候**使用通配符**！

`Routingkey` 一般都是有一个或多个单词组成，多个单词之间以”.”分割，例如： `item.insert`

 通配符规则：

`#`：匹配一个或多个词

`*`：匹配不多不少恰好1个词



举例：

`item.#`：能够匹配`item.insert.abc` 或者 `item.insert`

`item.*`：只能匹配`item.insert`

![1562524972321](E:/daybyday/02Web/day71%20%20RabbitMQ/%E8%AE%B2%E4%B9%89/assets/1562524972321.png)

图解：

- 红色Queue：绑定的是`usa.#` ，因此凡是以 `usa.`开头的`routing key` 都会被匹配到
- 黄色Queue：绑定的是`#.news` ，因此凡是以 `.news`结尾的 `routing key` 都会被匹配



##1 RabbitMq高级特性

### 1.1 生产者可靠性消息投递

可靠性消息

在使用 RabbitMQ 的时候，作为消息发送方希望杜绝任何消息丢失或者投递失败场景。RabbitMQ 为我们提供了两种方式用来控制消息的投递可靠性模式，mq提供了如下两种模式：

```
+ confirm模式
	生产者发送消息到交换机的时机 
+ return模式
    交换机转发消息给queue的时机
```



MQ投递消息的流程如下：



![1583026649199](E:/daybyday/02Web/day72%20RabbitMQ/%E8%AE%B2%E4%B9%89/images/1583026649199.png)

```
1.生产者发送消息到交换机
2.交换机根据routingkey 转发消息给队列
3.消费者监控队列，获取队列中信息
4.消费成功删除队列中的消息
```

- 消息从 product 到 exchange 则会返回一个 confirmCallback 。

- 消息从 exchange 到 queue 投递失败则会返回一个 returnCallback 。

####1.1.1 confirmcallback代码实现

(5)创建controller 发送消息



```java
@RestController
@RequestMapping("/test")
public class TestController {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Autowired
    private RabbitTemplate.ConfirmCallback myConfirmCallback;

    /**
     * 发送消息
     *
     * @return
     */
    @RequestMapping("/send1")
    public String send1() {
        //设置回调函数
        rabbitTemplate.setConfirmCallback(myConfirmCallback);
        //发送消息
        rabbitTemplate.convertAndSend("exchange_direct_demo01", "item.insert", "hello insert");
        return "ok";
    }
}
```

(6)创建回调函数

```java
package com.itheima.confirm;

import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.lang.Nullable;
import org.springframework.stereotype.Component;

@Component
public class MyConfirmCallback implements RabbitTemplate.ConfirmCallback {

    /**
     *
     * @param correlationData 消息信息
     * @param ack  确认标识：true,MQ服务器exchange表示已经确认收到消息 false 表示没有收到消息
     * @param cause  如果没有收到消息，则指定为MQ服务器exchange消息没有收到的原因，如果已经收到则指定为null
     */
    @Override
    public void confirm(@Nullable CorrelationData correlationData, boolean ack, @Nullable String cause) {
        if(ack){
            System.out.println("发送消息到交换机成功,"+cause);
        }else{
            System.out.println("发送消息到交换机失败,原因是："+cause);
        }
    }
}
```

#### 1.1.2 returncallback代码实现

(2)编写returncallback代码：

```java
@Component
public class MyReturnCallBack implements RabbitTemplate.ReturnCallback {
    /**
     *
     * @param message 消息信息
     * @param replyCode 退回的状态码
     * @param replyText 退回的信息
     * @param exchange 交换机
     * @param routingKey 路由key
     */
    @Override
    public void returnedMessage(Message message, int replyCode, String replyText, String exchange, String routingKey) {
        System.out.println("退回的消息是："+new String(message.getBody()));
        System.out.println("退回的replyCode是："+replyCode);
        System.out.println("退回的replyText是："+replyText);
        System.out.println("退回的exchange是："+exchange);
        System.out.println("退回的routingKey是："+routingKey);

    }
}
```
(5)创建controller 发送消息
```java
@Autowired
private RabbitTemplate.ReturnCallback myReturnCallback;


@RequestMapping("/send2")
public String send2() {
    //设置回调函数
    rabbitTemplate.setReturnCallback(myReturnCallback);
    //发送消息
    rabbitTemplate.convertAndSend("exchange_direct_demo01", "item.insert1234", "hello insert");
    return "ok";
}
```

### 1.2 消费者确认机制（ACK）

上边我们学习了发送方的可靠性投递，但是在消费方也有可能出现问题，比如没有接受消息，比如接受到消息之后，在代码执行过程中出现了异常，这种情况下我们需要额外的处理，那么就需要手动进行确认签收消息。rabbtimq给我们提供了一个机制：ACK机制。

ACK机制：有三种方式

- 自动确认  acknowledge="**none**"
- 手动确认  acknowledge="**manual**"
- 根据异常情况来确认（暂时不怎么用） acknowledge="**auto**"



解释：

```properties
其中自动确认是指:
	当消息一旦被Consumer接收到，则自动确认收到，并将相应 message 从 RabbitMQ 的消息缓存中移除。但是在实际业务处理中，很可能消息接收到，业务处理出现异常，那么该消息就会丢失。
其中手动确认方式是指：
	则需要在业务处理成功后，调用channel.basicAck()，手动签收，如果出现异常，则调用channel.basicNack()等方法，让其按照业务功能进行处理，比如：重新发送，比如拒绝签收进入死信队列等等。
```



#### 1.2.1 ACK代码实现

```java
第一种：签收

channel.basicAck()

第二种：拒绝签收 批量处理 

channel.basicNack() 

第三种：拒绝签收 不批量处理

channel.basicReject()

```

#### 1.3 消费端限流

### 1.4 TTL

TTL 全称 Time To Live（存活时间/过期时间）。当消息到达存活时间后，还没有被消费，会被自动清除。

RabbitMQ设置过期时间有两种：

- 针对某一个队列设置过期时间 ；队列中的所有消息在过期时间到之后，如果没有被消费则被全部清除
- 针对某一个特定的消息设置过期时间；队列中的消息设置过期时间之后，如果这个消息没有被消息则被清除。

```properties
需要注意一点的是：
 	针对某一个特定的消息设置过期时间时，一定是消息在队列中在队头的时候进行计算，如果某一个消息A 设置过期时间5秒，消息B在队头，消息B没有设置过期时间，B此时过了已经5秒钟了还没被消费。注意，此时A消息并不会被删除，因为它并没有再队头。

一般在工作当中，单独使用TTL的情况较少。我们后面会讲到延时队列。在这里有用处。
```



演示TTL 代码步骤：

```
1.创建配置类配置 过期队列 交换机 和绑定
2.创建controller 测试发送消息
```

### 1.5 死信队列

#### 1.5.1 死信队列的介绍

​	死信队列：当消息成为Dead message后，可以被重新发送到另一个交换机，这个交换机就是Dead Letter Exchange（死信交换机 简写：DLX）。

如下图的过程：

![1583045913151](E:/daybyday/02Web/day72%20RabbitMQ/%E8%AE%B2%E4%B9%89/images/1583045913151.png)

成为死信的三种条件：

1. 队列消息长度到达限制；
2. 消费者拒接消费消息，basicNack/basicReject,并且不把消息重新放入原目标队列,requeue=false；
3. 原队列存在消息过期设置，消息到达超时时间未被消费；

#### 1.5.2 死信的处理过程



DLX也是一个正常的Exchange，和一般的Exchange没有区别，它能在任何的队列上被指定，实际上就是设置某个队列的属性。

当这个队列中有死信时，RabbitMQ就会自动的将这个消息重新发布到设置的Exchange上去，进而被路由到另一个队列。

可以监听这个队列中的消息做相应的处理。



#### 1.5.3 死信队列的设置

刚才说到死信队列也是一个正常的exchange.只需要设置一些参数即可。

给队列设置参数： x-dead-letter-exchange 和 x-dead-letter-routing-key。