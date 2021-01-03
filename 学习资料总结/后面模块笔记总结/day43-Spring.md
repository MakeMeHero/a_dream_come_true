# 第一章-Spring的IOC注解开发

## 1.使用@Component注解配置管理的资源

```java
@Component("accountService")
public class AccountServiceImpl implements AccountService {} // 等同于 在xml里面,<bean id="accountService" class="具体的类">
```

## 2.引入context的命名空间

applicationContext.xml中需要引入context的命名空间,可在xsd-configuration.html中找到

## 3.配置扫描

在bean标签内部，使用context:component-scan ，让spring扫描该基础包下的所有子包注解

```xml
<context:component-scan base-package="com.itheima"></context:component-scan>
```

##注解开发进阶

#### @Component

```java
作用：	

	把资源让 spring 来管理。相当于在 xml 中配置一个 bean。

属性：

	value：指定 bean 的 id。如果不指定 value 属性，默认 bean 的 id 是当前类的类名。首字母小写。 
	
@Controller             :修饰WEB层类	 --->action | SpringMVC 

@Service                :修饰业务层类	 --->service

@Repository             :修饰DAO层类	 --->dao

在需要spring创建对象的类上面使用注解 @Component(“us”) 即可.Spring看类上是否有该注解，如果有该注解，生成这个类的实例。如果不指定value值， 那么默认的id值就是类名的名字， 第一个字母小写.
```

####@scope

```txt
singleton:单例(默认)

prototype:多例

	@Scope注解用来描述类的作用范围的，默认值singleton。如同xml中bean标签的属性scope <bean scope=""/>.如果配置成多例的使用prototype。

```

####初始化和销毁回调方法对应的注解

```
@PostConstrut:如同xml中bean标签的属性init-method <bean init-method=""/>,用来设置spring框架初始化此类实例时调用的初始化方法,标注在此类的初始化方法上

@PreDestroy:如同xml中bean标签的属性init-method <bean destroy-method=""/>,用来设置spring框架销毁此类实例时调用的销毁方法,标注在此类的销毁方法上

```

##使用注解注入属性

####1@Value

- 作用：

  注入基本数据类型和 String 类型数据的

- 属性：

  value：用于指定值 , 可以通过表达式动态获得内容再赋值

- 实例:

```java
@Value("奥巴马")
private String name;  
```

#### 2@Autowired

- 作用：

  ​	自动按照类型注入。当使用注解注入属性时， set 方法可以省略。它只能注入其他 bean 类型。

  ​	如果只有一个实现类, 可以自动注入成功

  ​	如果有两个或者两个以上的实现类, 找到变量名一致的id对象给注入进去, 如果找不到,就报错 

- 实例:

```java
@Component("accountService")
public class AccountServiceImpl implements AccountService {
    @Autowired
    //当有多个AccountDao实现类时候, @Autowired会在在Spring容器里面找id为accountDao的对象注入,找不到就报错
    private AccountDao accountDao;
    @Override
    public void save() {
        System.out.println("AccountServiceImpl---save()");
        accountDao.save();
    }
}
```

#### 3@Qualifier

- 作用

      在自动按照类型注入(@Autowired)的基础之上，再按照Bean的id注入。

  ​	它在给字段注入时不能独立使用，必须和@Autowire一起使用；

  ​	但是给方法参数注入时，可以独立使用。

- 属性

  ​	value：指定bean的id。

- 实例

```java
@Component("accountService")
public class AccountServiceImpl implements AccountService {
    @Autowired
    @Qualifier(value = "accountDao02")
    private AccountDao accountDao;
    @Override
    public void save() {
        System.out.println("AccountServiceImpl---save()");
        accountDao.save();
    }
}
```

#### 4@Resource

如果上面一个接口有多种实现，那么现在需要指定找具体的某一个实现，那么可以使用@Resource



```java
@Component("accountService")
public class AccountServiceImpl implements AccountService {
    @Resource(name = "accountDao02")
    private AccountDao accountDao;
    @Override
    public void save() {
        System.out.println("AccountServiceImpl---save()");
        accountDao.save();
    }
}
```

## 纯注解开发

#### 1 @Configuration

- 作用：

  用于指定当前类是一个spring配置类，当创建容器时会从该类上加载注解。

  获取容器时需要使用AnnotationApplicationContext(类.class)。

- 属性：

  value:用于指定配置类的字节码

- 示例代码:

```java
@Configuration
public class SpringConfiguration {

}
```

#### 2 @ComponentScan

- 作用：

  用于指定spring在初始化容器时要扫描的包。作用和在spring的xml配置文件中的： `<context:component-scan base-package="com.itheima"/>`是一样的。

- 属性：

  basePackages：用于指定要扫描的包。和该注解中的value属性作用一样。

- 示例代码:

```java
@Configuration
@ComponentScan("com.itheima")
public class SpringConfiguration {

}
```

#### 3@Bean

- 作用：

  该注解只能写在方法上，表明使用此方法创建一个对象，并且放入spring容器。

- 属性：

  name：给当前@Bean注解方法创建的对象指定一个名称(即bean的id）。

- 示例代码:

```java
/**
 * 该类是一个配置类，它的作用和bean.xml是一样的
 */
@Configuration
@ComponentScan("com.itheima")
public class SpringConfiguration {

    private String driver = "com.mysql.jdbc.Driver";
    private String url = "jdbc:mysql:///spring_day02";
    private String username = "root";
    private String password = "123456";
    /**
     * 用于创建一个QueryRunner对象
     * @param dataSource
     * @return
     */
    @Bean("runner")
    public QueryRunner createQueryRunner(@Qualifier(value ="dataSource") DataSource dataSource){
        return new QueryRunner(dataSource);
    }
    /**
     * 用于创建DataSource
     * @return
     * @throws PropertyVetoException
     */
    @Bean(name = "dataSource")
    public DataSource createDataSource() throws PropertyVetoException {
        ComboPooledDataSource ds = new ComboPooledDataSource();
        ds.setDriverClass(driver);
        ds.setJdbcUrl(url);
        ds.setUser(username);
        ds.setPassword(password);
        return ds;
    }

}
```

#### 4@Import

- 作用：

  用于导入其他配置类，在引入其他配置类时，可以不用再写@Configuration注解。当然，写上也没问题。

- 属性：

  value[]：用于指定其他配置类的字节码。

- 示例代码:

```java
/**
 * 该类是一个配置类，它的作用和bean.xml是一样的
 */
@Configuration
@ComponentScan("com.itheima")
@Import({JdbcConfig.class})
public class SpringConfiguration {


}
```

```java
public class JdbcConfig {
    private String driver = "com.mysql.jdbc.Driver";
    private String url = "jdbc:mysql:///spring_day02";
    private String username = "root";
    private String password = "123456";
    /**
     * 用于创建一个QueryRunner对象
     * @param dataSource
     * @return
     */
    @Bean("runner")
    @Scope("prototype")
    public QueryRunner createQueryRunner(@Qualifier(value ="dataSource") DataSource dataSource){
        return new QueryRunner(dataSource);
    }
    /**
     * 用于创建DataSource
     * @return
     * @throws PropertyVetoException
     */
    @Bean(name = "dataSource")
    public DataSource createDataSource() throws PropertyVetoException {
        ComboPooledDataSource ds = new ComboPooledDataSource();
        ds.setDriverClass(driver);
        ds.setJdbcUrl(url);
        ds.setUser(username);
        ds.setPassword(password);
        return ds;
    }


}
```

#### 5@PropertySource

- 作用：

  用于加载.properties文件中的配置。例如我们配置数据源时，可以把连接数据库的信息写到properties配置文件中，就可以使用此注解指定properties配置文件的位置。

- 属性：

  value[]：用于指定properties文件位置。如果是在类路径下，需要写上classpath:

- 示例代码:

jdbc.properties

```properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring_day02
jdbc.username=root
jdbc.password=123456
```

JdbcConfig.java

```java
@PropertySource(value = {"classpath:jdbc.properties"})
public class JdbcConfig {

    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;

    @Bean("queryRunner")
    public QueryRunner createQueryRunner(DataSource dataSource){
        QueryRunner queryRunner = new QueryRunner(dataSource);
        return  queryRunner;
    }

    @Bean("dataSource")
    public  DataSource createDataSource(){
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setUrl(url);
        dataSource.setDriverClassName(driver);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return  dataSource;
    }
}
```

# 第二章-Spring整合测试

- 导入spring整合Junit的坐标

  ```xml
    	<dependency>
    		<groupId>org.springframework</groupId>
    		<artifactId>spring-test</artifactId>
    		<version>5.0.2.RELEASE</version>
    	</dependency>
  ```

- 在测试类上面标记注解

  ```java
  @RunWith(SpringJUnit4ClassRunner.class)//指明运行的测试环境
  //@ContextConfiguration("classpath:applicationContext.xml")//指明spring框架的加载的配置文件【有applicationContext.xml解使用这种】
  @ContextConfiguration(classes = SpringConfiguration.class)//指明spring框架的加载的配置类【纯注解使用这种】
  public class DbTest
  {
      @Autowired
      private AccountService accountService;
  
      @Test
      public void testSaveAccount() throws Exception {
          Account account = new Account();
          account.setName("王二麻子");
          account.setMoney(100f);
          accountService.save(account);
      }
  
  }
  ```

# 第三章-AOP相关的概念

## 什么是AOP

AOP：全称是AspectOriented Programming, 即面向切面编程。在不修改源码的基础上，对我们的已有方法进行增强。

说白了就是把我们程序重复的代码抽取出来，在需要执行的时候，使用动态代理的技术，进行增强

## 使用AOP优势

  减少重复代码      

    提高开发效率      

    维护方便

## AOP实现原理

​	使用动态代理技术 

- JDK动态代理: 必须需要接口的

- Cglib动态代理: 不需要接口的,只需要类就好了 

  #### 1.JDK方式

  ==要求: 必须有接口==   

  ```java
  public class B_JDKProxy {
  
      @Test
      public void fun01(){
           AccountDao accountDao = new AccountDaoImpl();
  
          AccountDao proxyDao =  (AccountDao) Proxy.newProxyInstance(accountDao.getClass().getClassLoader(), accountDao.getClass().getInterfaces(), new InvocationHandler() {
              @Override
              public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                  if("save".equals(method.getName())){
                      System.out.println("权限校验...");
                      return method.invoke(accountDao,args);
                  }
                  return method.invoke(accountDao, args);
              }
          });
          proxyDao.save();
      }
  }
  ```

  #### 2.CgLib方式【了解】

  - 添加坐标

  ```xml
    <dependencies>
      <!--Spring核心容器-->
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.0.2.RELEASE</version>
      </dependency>
      <!--SpringAOP相关的坐标-->
      <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>1.8.7</version>
      </dependency>
      
      <!--Spring整合单元测试-->
      <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-test</artifactId>
        <version>5.0.2.RELEASE</version>
      </dependency>
      <!--单元测试-->
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
      </dependency>
    </dependencies>
  ```

  - 使用CgLib方式实现: 第三方的代理机制，不是jdk自带的.  没有实现接口的类产生代理，使用的是字节码的增强技术，其实就是产生这个类的子类。

    ==不需要有接口==

  ```java
  public class C_CglibProxy {
      @Test
      public void fun01(){
           AccountDaoImpl accountDao = new AccountDaoImpl();
  
          //创建enhancer对象
          Enhancer enhancer  = new Enhancer();
          //设置代理的父类
          enhancer.setSuperclass(AccountDaoImpl.class);
  
          enhancer.setCallback(new MethodInterceptor() {
  
              @Override
              public Object intercept(Object obj, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
                  if("save".equals(method.getName())){
                      System.out.println("权限校验...");
                      return method.invoke(accountDao, args);
                  }
                  return method.invoke(accountDao, args);
              }
          });
  
          AccountDaoImpl accountDaoProxy = (AccountDaoImpl) enhancer.create();
          accountDaoProxy.save();
      }
  }
  ```

==在spring中，框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式。==

## AOP术语

- JoinPoint: 连接点

  ​	类里面哪些方法可以被增强，这些方法称为连接点. 在spring的AOP中，指的是所有现有的方法。

- Pointcut: 切入点

  ​	 在类里面可以有很多方法被增强，但是实际开发中，我们只对具体的某几个方法而已，那么这些实际增强的方法就称之为切入点

- Advice: 通知/增强 

  ​	增强的逻辑、称为增强，比如给某个切入点(方法) 扩展了校验权限的功能，那么这个校验权限即可称之为增强 或者是通知

  ​	通知分为:

  ​		前置通知： 在原来方法之前执行. 

  ​		后置通知： 在原来方法之后执行. 特点: 可以得到被增强方法的返回值

  ​		环绕通知：在方法之前和方法之后执行. 特点:可以阻止目标方法执行

  ​		异常通知： 目标方法出现异常执行. 如果方法没有异常,不会执行. 特点:可以获得异常的信息

  ​		最终通知： 指的是无论是否有异常，总是被执行的。

- Aspect: 切面

  ​	切入点和通知的结合。

