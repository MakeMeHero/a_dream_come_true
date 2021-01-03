# 第一章-Spring中的AOP

##基于XML的AOP配置

####1.定义被增强的业务逻辑类和切面类(增强的)

```java
//被增强的类
public class AccountDaoImpl implements AccountDao {

    @Override
    public void save() {
        System.out.println("save()...");
    }
}

//增强的类(切面类)
public class MyAscept {
    public void checkPrivilege() {// 通知
        System.out.println("权限的校验...");
    }
}
```

####2.xml中声明 增强的bean （扩展的bean） 和 被增强的bean (被扩展的bean)

```xml
<!--注册accountDao-->
<bean id="accountDao" class="com.itheima.dao.impl.AccountDaoImpl"/>
<!--注册增强(切面)类-->
<bean id="myAscept" class="com.itheima.aspect.MyAscept"/>
```

####3.定义aop的配置

```xml
        <!--配置切入点(哪个包下的哪个类哪个方法需要增强)
       expression="execution(* com.xyz.myapp.service.*.*(..))"
       第一个* 表示返回值 ，不管任何返回值。 匹配任意返回值
       com.xyz.myapp.service : 表示这个包下
       第二个* 表示这个包下的所有类
       第三个* 表示这个包下的所有类中的所有方法
       (..) : 这个表示方法的参数 .. 任意参数
       spring 根据这一套规则，能够找到具体哪一个方法来做增强了。
    -->
        -->
     <!--配置AOP-->
    <aop:config>
        <aop:pointcut id="pointCut01" expression="execution(* com.itheima.dao.impl.AccountDaoImpl.save(..))"/>
        <!--配置切面(切入点和通知的结合)-->
        <aop:aspect ref="myAscept">
            <!--前置通知-->
            <aop:before method="checkPrivilege" pointcut-ref="pointCut01"></aop:before>
        </aop:aspect>
    </aop:config>      
</aop:config>
```

​        <aop:before method="" pointcut-ref="">
        <aop:after-returing method="" pointcut-ref="">
        <aop:around method="" pointcut-ref="">

## 基于注解的AOP配置

- 定义被增强的业务逻辑类

  ```java
  //被增强的类
  public class AccountDaoImpl implements AccountDao {
      @Override
      public void save() {
          System.out.println("save()...");
      }
  }
  ```

- 定义切面类,在切面增强类上面使用注解 @Aspect并且在切面类中定义切入点方法

  ```java
  @Aspect
  public class MyAscept {
      @Before(value = "execution(* com.itheima.dao.impl.AccountDaoImpl.save(..))")
      public void checkPrivilege() {// 通知
          System.out.println("权限的校验...");
      }
  }
  ```

- 创建配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
  
      <!--注册accountDao-->
      <bean id="accountDao" class="com.itheima.dao.impl.AccountDaoImpl"/>
      <!--注册切面类-->
      <bean id="myAscept" class="com.itheima.aspect.MyAscept"/>
      <!--开启AOP注解-->
      <aop:aspectj-autoproxy/>
  </beans>
  ```

- @Before()
- @AfterReturning()
- @Around()
- @AfterThrowing()
- @After()

# 第二章-Spring管理事务

## Spring声明式事务-xml配置方式【重点】

- 配置事务管理器

  ```xml
  <bean id="transactionManager" 		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  		<property name="dataSource" ref="dataSource"></property>
  </bean>
  ```

- 配置事务建议(事务的规则)

  ```xml
  <tx:advice id="adviceId" transaction-manager="transactionManager">
    <!--在tx:advice标签内部 配置事务的属性 -->
    <tx:attributes>
    <!-- 指定方法名称：是业务核心方法 
        read-only：是否是只读事务。默认false，不只读。
        isolation：指定事务的隔离级别。默认值是使用数据库的默认隔离级别。 
        propagation：指定事务的传播行为。
        timeout：指定超时时间。默认值为：-1。永不超时。
        rollback-for：用于指定一个异常，当执行产生该异常时，事务回滚。产生其他异常，事务不回滚。没有默认值，任何异常都回滚。
        no-rollback-for：用于指定一个异常，当产生该异常时，事务不回滚，产生其他异常时，事务回滚。没有默认值，任何异常都回滚。
        -->
        <tx:method name="*" read-only="false" propagation="REQUIRED"/>
        <tx:method name="find*" read-only="true" propagation="SUPPORTS"/>
    </tx:attributes>
  </tx:advice>
  ```

- 配置事务的AOP

  ```xml
  <aop:config>
  	<!-- 定义切入点 ，也就是到底给那些类中的那些方法加入事务的管理 -->
  	<aop:pointcut expression="execution(* com.itheima.service.impl.*.*(..))" id="pointCat"/>
  	<!-- 让切入点和事务的建议绑定到一起 -->
  	<aop:advisor advice-ref="adviceId" pointcut-ref="pointCat"/>
  </aop:config>
  ```

#### 3.2事务的传播行为

##### 3.2.1事务的传播行为的作用

​	我们一般都是将事务设置在Service层,那么当我们调用Service层的一个方法的时候, 它能够保证我们的这个方法中,执行的所有的对数据库的更新操作保持在一个事务中， 在事务层里面调用的这些方法要么全部成功，要么全部失败。

​	如果你的Service层的这个方法中，除了调用了Dao层的方法之外， 还调用了本类的其他的Service方法，那么在调用其他的Service方法的时候， 我必须保证两个service处在同一个事务中，确保事物的一致性。

​	 事务的传播特性就是解决这个问题的。

##### 3.2.2 事务的传播行为的取值

保证在同一个事务里面:

- **PROPAGATION_REQUIRED:默认值，也是最常用的场景.**

  如果当前没有事务，就新建一个事务，
  如果已经存在一个事务中，加入到这个事务中。

- PROPAGATION_SUPPORTS：

  如果当前没有事务，就以非事务方式执行。

  如果已经存在一个事务中，加入到这个事务中。

- PROPAGATION_MANDATORY

  如果当前没有有事务，就抛出异常; 

  如果已经存在一个事务中，加入到这个事务中。

保证不在同一个事物里:

- **PROPAGATION_REQUIRES_NEW**

  如果当前有事务，把当前事务挂起,创建新的事务但独自执行

- PROPAGATION_NOT_SUPPORTED

  如果当前存在事务，就把当前事务挂起。不创建事务

- PROPAGATION_NEVER

  如果当前存在事务，抛出异常

  ```的
  
  ```

## spring声明式事务-注解方式【重点】

  

- 在applicationContext里面打开注解驱动

  ```xml
  <!--一,配置事务管理器  -->
  	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
  		<property name="dataSource" ref="dataSource"></property>
  	</bean>
  	
  <!--二配置事务注解  -->
  <tx:annotation-driven transaction-manager="transactionManager"/>
  ```

- 在业务逻辑类上面使用注解

  @Transactional ： 如果在类上声明，那么标记着该类中的所有方法都使用事务管理。也可以作用于方法上，	那么这就表示只有具体的方法才会使用事务。

