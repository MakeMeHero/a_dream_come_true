#### 3.2.1 Config简介

分布式系统中，由于服务数量非常多，配置文件分散在不同微服务项目中，管理极其不方便。为了方便配置文件集中管理，需要分布式配置中心组件。在Spring Cloud中，提供了Spring Cloud Config，它支持配置文件放在配置服务的本地，也支持配置文件放在远程仓库Git(GitHub、码云)。配置中心本质上是一个微服务，同样需要注册到Eureka服务中心！

文件命名有规则：

```properties
配置文件的命名方式：{application}-{profile}.yml或{application}-{profile}.properties
application为应用名称
profile用于区分开发环境dev，测试环境test，生产环境pro等
  开发环境 user-dev.yml
  测试环境 user-test.yml
  生产环境 user-pro.yml
```

创建一个user-provider-dev.yml文件

将user-provider工程里的配置文件application.yml内容复制进去。

####搭建配置中心微服务

1.在`config-server`工程中创建启动类`com.itheima.ConfigServerApplication添加@EnableConfigServer//开启配置服务支持

(4)application.yml配置文件

```properties
# 注释版本
server:
  port: 18085 # 端口号
spring:
  application:
    name: config-server # 应用名
  cloud:
    config:
      server:
        git:
          # 配置gitee的仓库地址
          uri: https://gitee.com/skllll/config.git
# Eureka服务中心配置
eureka:
  client:
    service-url:
      # 注册Eureka Server集群
      defaultZone: http://127.0.0.1:7001/eureka
# com.itheima 包下的日志级别都为Debug
logging:
  level:
    com: debug
```

注意：上述spring.cloud.config.server.git.uri是在码云创建的仓库地址

#### 服务去获取配置中心配置

(2)修改配置

删除user-provider工程的application.yml文件

创建user-provider工程bootstrap.yml配置文件，配置内容如下

```yml
# 注释版本
spring:
  cloud:
    config:
      name: user-provider # 与远程仓库中的配置文件的application保持一致，{application}-{profile}.yml
      profile: dev # 远程仓库中的配置文件的profile保持一致
      label: master # 远程仓库中的版本保持一致
      discovery:
        enabled: true # 使用配置中心
        service-id: config-server # 配置中心服务id
#向Eureka服务中心集群注册服务
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:7001/eureka
```

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

修改码云上的配置后，发现项目中的数据仍然没有变化,只有项目重启后才会变化。