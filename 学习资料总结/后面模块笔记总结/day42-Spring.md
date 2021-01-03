# 第一章-Spring概述

## 知识点-Spring介绍

#### 1.1什么是Spring

Spring 是一个开源框架，是为了解决企业应用程序开发复杂性而创建的。框架的主要优势之一就是其分层架构，分层架构允许我们选择使用哪一个组件，同时是集成性框架,为我们集成各种优秀的框架。Spring是一个分层的JavaSE/EE 轻量级开源框架。

#### 1.2Spring优点

1.方便解耦，简化开发

通过Spring提供的IoC容器，我们可以将对象之间的依赖关系交由Spring进行控制，避免硬编码所造成的过度程序耦合

2.AOP编程的支持、声明式事务的支持 

通过Spring提供的AOP功能，帮助我们简单处理日志打印、权限管理，同时我们可以从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活地进行事务的管理

3.方便集成各种优秀框架 

集成类似SpringMVC、MyBatis等优秀框架简化我们对控制层和持久层代码编写

4.降低Java EE API的使用难度 

提供类似JDBCTemplate等模板封装，减少我们对模板代码的编写

# 第二章-IOC 

## 手写ioc思路

1.xml文件包含id:接口的名字, class:实现类的全限定名

2.工厂类使用demo4j解析,将得到id、class

3.根据class全限定类名使用反射机制创建对象，将id和对象作为静态的HashMap的Kye-Value存储

4.使用对象时可以通过静态方法getBean，根据id获取对象

面临问题解决

 1.每次getBean都需要读取xml：xml读取一次 (定义在static{})
 2.临时的时候才反射创建：程序初始化的时候, 解析好 反射创建好 存到容器【map key是id, value是对应的对象】里面. getBean()的时候直接从容器里面取

## OC概述

IOC(inversion of control)的中文解释是“==控制反转”，对象的使用者不是创建者.  作用是将对象的创建 反转给spring框架来创建和管理。==

控制反转怎么去理解呢。 其实它反转的是什么呢，是对象的创建工作。 以前我们在servlet或者service里面创建对象，都是使用new 的方式来直接创建对象，现在有了spring之后，我们就再也不new对象了，而是把对象创建的工作交给spring容器去维护。我们只需要问spring容器要对象即可

IOC 的作用：削减计算机程序的耦合(解除我们代码中的依赖关系)。 

## 配置文件详解(Bean标签)

 id/name属性: 随便写,作为bean的唯一标识,不要重复(建议写接口的名字,首字母小写)
 class属性:  类的全限定名

 scope属性:  singleton 单例,  不管获得多少次 都是同一个, 创建出来存到Spring容器里面  【默认】
                      prototype 多例,  每获得一次 就创建一个新的对象,创建出来不会存到Spring容器里面

   			request 把创建的对象存到request域,针对web项目【了解】
                    	session 把创建的对象存到session域,针对web项目【了解】

 init-method属性: 指定初始化方法,写方法名
 destroy-method属性: 指定销毁的方法, 象征着当前对象从Spring容器里面移除了 写方法名              

## bean的作用范围和生命周期   

单例对象： scope="singleton"

​	一个应用只有一个对象的实例。它的作用范围就是整个引用。
	生命周期：
		对象出生：当应用加载，创建容器时，对象就被创建了。
		对象活着：只要容器在，对象一直活着。
		对象死亡：当应用卸载，销毁容器时，对象就被销毁了。

多例对象： scope="prototype"

​	每次访问对象时，都会重新创建对象实例。
​	生命周期：
​		对象出生：当使用对象时，创建新的对象实例。
​		对象活着：只要对象在使用中，就一直活着。
		对象死亡：当对象长时间不用时，被 java 的垃圾回收器回收了.

## spring 中工厂的类

ClassPathXmlApplicationContext：它是从类的根路径下加载配置文件 

FileSystemXmlApplicationContext：它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置。

AnnotationConfigApplicationContext:当我们使用注解配置容器对象时，需要使用此类来创建 spring 容器。它用来读取注解。

## BeanFactory 和 ApplicationContext 的区别

ApplicationContext加载方式是框架启动时就开始创建所有单例的bean,存到了容器里面

​	BeanFactory加载方式是用到bean时再加载(目前已经被废弃)

# 第三章-依赖注入

##依赖注入定义

依赖注入全称是 dependency Injection 翻译过来是依赖注入.其实就是如果我们托管的某一个类中存在属性，需要spring在创建该类实例的时候，顺便给这个对象里面的属性进行赋值。 这就是依赖注入。

我们交给Spring创建的Bean里面可能有一些属性(字段), Spring帮我创建的同时也把Bean的一些属性(字段)给赋值, 这个赋值就是注入.

## 依赖注入实现方式

1. 构造方法方式注入

2. set方法方式的注入

3. P名称空间注入

4. SpEL的属性注入

   #### 1构造方法方式注入【掌握】

   - Java代码

     ```java
     public class AccountServiceImpl implements AccountService {
         private String name;
         public AccountServiceImpl(String name) {
             this.name = name;
         }
         @Override
         public void save() {
             System.out.println("AccountServiceImpl... save()"+name);
     
         }
     }
     ```

   - 配置文件

     ```xml
     <!--注册AccountService-->
      <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
         <constructor-arg name="name" value="张三"></constructor-arg>
     </bean>
     ```

   

   #### 2set方法方式的注入【重点】

   ##### 2.1注入简单类型

   - java代码

     ```java
     public class AccountServiceImpl implements AccountService {
         private String name;
     
         public void setName(String name) {
             this.name = name;
         }
     
         @Override
         public void save() {
             System.out.println("AccountServiceImpl... save()"+name);
     
         }
     }
     ```

   - 配置文件

     ```xml
      <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
            <property name="name" value="李四"></property>
     </bean>
     ```

   ##### 2.2注入数组类型

   - java代码

     ```java
     public class AccountServiceImpl implements AccountService {
         private String[] hobbys;
     
         public void setHobbys(String[] hobbys) {
             this.hobbys = hobbys;
         }
     
         @Override
         public void save() {
             System.out.println("AccountServiceImpl... save()"+ Arrays.toString(hobbys));
     
         }
     }
     ```

   - 配置文件

     ```xml
         <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
             <property name="hobbys">
                 <array>
                     <value>篮球</value>
                     <value>足球</value>
                     <value>乒乓球</value>
                     <value>排球</value>
                 </array>
             </property>
         </bean>
     ```

   ##### 2.3注入Map类型

   - Java代码

     ```java
     public class AccountServiceImpl implements AccountService {
         private Map<String,String> map;
     
         public void setMap(Map<String, String> map) {
             this.map = map;
         }
     
         @Override
         public void save() {
             Set<Map.Entry<String, String>> set = map.entrySet();
             for (Map.Entry<String, String> entry : set) {
                 System.out.println(entry.getKey()+":"+entry.getValue());
             }
             System.out.println("AccountServiceImpl... save()");
     
         }
     }
     ```

   - 配置文件

     ```xml
     <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
          <property name="map">
              <map>
                   <entry key="akey" value="aaa"/>
                   <entry key="bkey" value="bbb"/>
                   <entry key="ckey" value="ccc"/>
              </map>
          </property>
     </bean>
     或者
     
     <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
         <property name="map">
     		<props>
     			<prop key="akey">aaa</prop>
     			<prop key="bkey">bbb</prop>
     			<prop key="ckey">ccc</prop>
     		</props>
     	</property>
     </bean>
     ```

   ##### 2.4注入Java对象类型

   - Java代码

     ```java
     public class AccountServiceImpl implements AccountService {
         private AccountDao accountDao;
     
         public void setAccountDao(AccountDao accountDao) {
             this.accountDao = accountDao;
         }
         @Override
         public void save() {
     
             System.out.println("AccountServiceImpl... save()");
             accountDao.save();
     
         }
     }
     ```

   - 配置文件

     ```xml
     <!--注册AccountService-->
     <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
         <property name="accountDao" ref="accountDao"></property>
     </bean>
     <!--注册accountDao-->
     <bean id="accountDao" class="com.itheima.dao.impl.AccountDaoImpl">
     </bean>
     ```

   #### 3.3P名称空间注入【了解】

   ##### 3.3.1p名称空间的方式

   - 提供属性的set方法

   - 在applicationContext.xml引入p命名空间`xmlns:p="http://www.springframework.org/schema/p"`

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
   </beans>
   ```

   - 使用

   ```xml
   <bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl" p:name="张三">
     	
   </bean>
   ```

   #### 3.4 Spring3.0之后  SpEL的属性注入【了解】

   ​	这种方式是使用spring 提供的一种表达式方式去赋值， 如果是给前面提过的几个类型注入赋值，那么使用spEL 表达式来注入赋值倒显得繁琐。但是这个spEL 方式赋值，最大的好处在于，它能像EL表达式一般，在里面进行运算、 逻辑判断,还可以调用其它Bean的属性和方法给当前属性赋值

   - 语法格式 ：  #{spEL} 
   - 类中存在一个属性 name  , 并且给定 set方法。 

   ```xml
     <property name="name" value="#{'张三'}" ></property>
   ```

#第四章-IOC练习

# 第五章-Spring5 的新特性【了解】

## 利用 jdk8 版本更新的内容

1.8相对于1.7性能提升

创建对象、给同一对象赋值、反射创建对象时间大大缩短

@NonNull 注解和@Nullable 注解的使用 

​	用 @Nullable 和 @NotNull 注解来显示表明可为空的参数和以及返回值。这样就够在编译的时候处理空值而不是在运行时抛出 NullPointerExceptions。 