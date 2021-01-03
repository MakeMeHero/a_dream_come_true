## Springboot的配置文件

SpringBoot是**约定大于配置**的，所以很多配置都有默认值。如果想修改默认配置，可以用application.properties或application.yml(application.yaml)自定义配置。SpringBoot默认从Resource目录加载自定义配置文件。

### 3.3.2 yaml或者yml文件



​	yaml文件等价于properties文件，在使用过程中都是一样的效果。但是yml文件书写的方式和properties文件不一样。更加简洁，那么我们可以根据需要选择性的使用properties和yml文件。如果同时存在两个文件，那么优先级properties要高于yml。

语法特点如下：

- 大小写敏感
- 数据值前必须有空格，作为分隔符
- 缩进的空格数目不重要，只需要对齐即可
- `#` 表示注释

书写格式如下要求如下：key和key之间需要换行以及空格两次。 简单key value之间需要冒号加空格。

```yaml
key1:
  key2:
    key3: value
key4: value4    
    
```

比如：

```yml
server:
  port: 8081
```

注意：yml语法中，相同缩进代表同一个级别

```yaml
# 基本格式 key: value
name: zhangsan
# 数组   - 用于区分
city:
  - beijing
  - tianjin
  - shanghai
  - chongqing
#集合中的元素是对象形式
students:
  - name: zhangsan
    age: 18
    score: 100
  - name: lisi
    age: 28
    score: 88
  - name: wangwu
    age: 38
    score: 90
#map集合形式
maps: {"name":"zhangsan", "age": "15"}
#参数引用
person:
  name: ${name} # 该值可以获取到上边的name定义的值	
```



### 3.3.3 获取配置文件中值

获取配置文件中的值我们一般有几种方式：

- @value注解的方式 只能获取简单值
- Environment的方式
- @ConfigurationProperties

演示如下：

yml中配置：

```yaml
# 基本格式 key: value
name: zhangsan
# 数组   - 用于区分
city:
  - beijing
  - tianjin
  - shanghai
  - chongqing
#集合中的元素是对象形式
students:
  - name: zhangsan
    age: 18
    score: 100
  - name: lisi
    age: 28
    score: 88
  - name: wangwu
    age: 38
    score: 90
#map集合形式
maps: {"name":"zhangsan", "age": "15"}
#参数引用
person:
  name: ${name} # 该值可以获取到上边的name定义的值
  age: 12
```

java代码：

controller

```java
@RestController
public class Test2Controller {
    @Value("${name}")
    private String name;

    @Value("${city[0]}")
    private String city0;

    @Value("${students[0].name}")
    private String studentname;

    @Value("${person.name}")
    private String personName;


    @Value("${maps.name}")//value注解只能获简单的值对象
    private String name1;

    @Autowired
    private Student student;

    @RequestMapping("/show")
    public String showHello() {
        System.out.println(name);
        System.out.println(city0);
        System.out.println(studentname);
        System.out.println(personName);
        
        System.out.println(">>>>"+student.getAge());

        return "hello world";
    }
}
```

pojo:

```java
@Component
@ConfigurationProperties(prefix = "person")
public class Student {
    private String name;
    private Integer age;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

}
```



### 3.3.4 profile

在开发的过程中，需要配置不同的环境，所以即使我们在application.yml中配置了相关的配置项，当时在测试是，需要修改数据源等端口路径的配置，测试完成之后，又上生产环境，这时配置又需要修改，修改起来很麻烦。



- properties配置方式

  ![1582711701050](E:/daybyday/02Web/day67%20%20springboot/%E8%AE%B2%E4%B9%89/images/1582711701050.png)

application.properties:

```properties
#通过active指定选用配置环境
spring.profiles.active=test
```

application-dev.properties:

```properties
#开发环境
server.port=8081
```



application-test.properties

```properties
server.port=8082
```



application-pro.properties

```properties
server.port=8083
```



- yml配置方式

![1582718508134](E:/daybyday/02Web/day67%20%20springboot/%E8%AE%B2%E4%B9%89/images/1582718508134.png)

application.yml:

```yaml
#通过active指定选用配置环境
spring:
  profiles:
    active: pro
```



application-dev.yml:

```yaml
#开发环境
server:
  port: 8081
```



application-test.yml:

```yaml
#测试环境
server:
  port: 8082
```



applicatioin-pro.yml

```yaml
#生产环境
server:
  port: 8083
```

 还有一种是分隔符的方式（了解）

```yml
spring:
  profiles:
    active: dev

---
#开发环境
server:
  port: 8081
spring:
  profiles: dev

---
#测试环境
server:
  port: 8082
spring:
  profiles: test

---
#生产环境
server:
  port: 8083
spring:
  profiles: pro
```



- 激活profile的方式（了解）

  - 配置文件的方式（上边已经说过）

  - 运行是指定参数 `java -jar xxx.jar --spring.profiles.active=test`

  - jvm虚拟机参数配置 -Dspring.profiles.active=dev

##Springboot版本控制的原理分析
我们知道springboot不需要添加版本。版本统一进行管理。那么原理是什么呢？
	我们自己的springboot项目继承于spring-boot-starter-parent,而他又继承与spring-boot-dependencies ，dependencies中定义了各个版本。通过maven的依赖传递特性从而实现了版本的统一管理。

## springboot的自动配置原理

####1.starter坐标注入对象原理

condition接口增加条件判断功能，用于选择性的创建Bean对象到spring容器中。

他有一个matches方法.返回值是布尔类型,如果容器中存在该类的全限定类名,则返回true,没有返回false,如果加入起步依赖就会存在该类

类会在配置类中进行注册,注册时会在方法上添加conditionoon相关注解,判断是否存在该类,存在就注入,不存在不注入

ConditionalOnBean   当spring容器中有某一个bean时使用

ConditionalOnClass  当判断当前类路径下有某一个类时使用

ConditionalOnMissingBean 当spring容器中没有某一个bean时才使用

ConditionalOnMissingClass 当当前类路径下没有某一个类的时候才使用

ConditionalOnProperty 当配置文件中有某一个key value的时候才使用



####2.@SpringBootApplication注解说明

(1)@SpringBootApplication注解里面有@EnableAutoConfiguration，那么这种@Enable*开头就是springboot中定义的一些动态启用某些功能的注解，他的底层实现原理实际上用的就是@import注解导入一些配置，自动进行配置，加载Bean.

(2)在启动类的注解@springbootapplication注解里面又修饰了@compnetScan注解，该注解的作用用于组件扫描包类似于xml中的context-componet-scan，如果不指定扫描路径，那么就扫描该注解修饰的启动类所在的包以及子包

## SpringBoot的监控

####1.用Actuator

![](E:\学习资料总结\后面模块笔记总结\assets/1583320454547.png)

####2.用spring boot admin

![](E:\学习资料总结\后面模块笔记总结\assets/1583324107358.png)