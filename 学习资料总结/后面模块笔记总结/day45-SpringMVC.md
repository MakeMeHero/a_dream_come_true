# 第一章-SpringMVC介绍

##什么是SpringMVC

SpringMVC 是一种基于Java实现的MVC设计模型的请求驱动类型的轻量级WEB层框架

## SpringMVC 的优点

1.清晰的角色划分:前端控制器、请求到处理器映射、处理器适配器、处理器、视图解析器

2.使用简单，提供的注解能帮助我们简单的获取各种数据

3.和 Spring 其他框架无缝集成，是其它 Web 框架所不具备的。

# 第二章-SpringMVC入门

## SpringMVC 的请求响应流程

![](img\tu_7.jpg)
##SpringMVC组件

- DispatcherServlet：前端控制器 

  ​	用户请求到达前端控制器，它就相当于 mvc 模式中的 c， dispatcherServlet 是整个流程控制的中心，由
  它调用其它组件处理用户的请求， dispatcherServlet 的存在降低了组件之间的耦合性。 

- HandlerMapping：处理器映射器    

  ​	HandlerMapping 负责根据用户请求找到 Handler 即处理器， SpringMVC 提供了不同的映射器实现不同的
  映射方式，例如：配置文件方式，实现接口方式，注解方式等。 

- Handler：处理器 (自己写的Controller类)

  ​	它就是我们开发中要编写的具体业务控制器。由 DispatcherServlet 把用户请求转发到 Handler。由
  Handler 对具体的用户请求进行处理。 

- HandlAdapter：处理器适配器 

  ​	通过 HandlerAdapter 对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。 

- View Resolver：视图解析器  

  ​	View Resolver 负责将处理结果生成 View 视图， View Resolver 首先根据逻辑视图名解析成物理视图名
  即具体的页面地址，再生成 View 视图对象，最后对 View 进行渲染将处理结果通过页面展示给用户。 

- View：视图 

  ​	SpringMVC 框架提供了很多的 View 视图类型的支持，包括：jsp ,  jstlView、 freemarkerView、 pdfView等。我们最常用的视图就是 jsp。
  ​	一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由程序员根据业务需求开发具体的页面。 

`<mvc:annotation-driven>` 注解说明 :

​	在 SpringMVC 的各个组件中，处理器映射器、处理器适配器、视图解析器称为 SpringMVC 的三大组件。使 用 	`<mvc:annotation-driven>`  自 动 加 载 RequestMappingHandlerMapping （ 处 理 映 射 器 ）RequestMappingHandlerAdapter （ 处 理 适 配 器 ） ， 可 用 在 SpringMVC.xml 配 置 文 件 中 使 用
  `<mvc:annotation-driven/>` 替代注解处理器和适配器的配置(默认情况下不配置也是可以使用的)。 

## RequestMapping作用

​	RequestMapping注解的作用是建立请求URL和处理方法之间的对应关系

​	RequestMapping注解可以作用在方法和类上

​	参数：path\value:指定请求路径的url   method :    指定该方法的请求方式  params:   指定限制请求参数的条件  headers:  发送的请求中必须包含的请求头

# 第三章-SpringMVC进阶

## 知识点-请求参数的绑定【重点】

支持的数据类型

1. 请求参数是简单类型
   - 方法的形参名必须和请求参数中的name一致
2. 请求参数是pojo类型
   - 形参直接使用pojo
   - pojo的属性名必须和请求参数中的name一致
3. 请求参数是pojo包含集合类型
   - 形参直接使用pojo
   - pojo的属性名必须和请求参数中的name一致
   - 集合是list，list属性名[下标].pojo属性名
   - 集合是map，map属性名[key].pojo属性名
## 自定义类型转换器

- 定义一个类，实现 Converter 接口

  该接口有两个泛型,,S:表示接受的类型， T：表示目标类型(需要转的类型)

```java
public class StringToDateConverter implements Converter<String,Date> {
    @Nullable
    @Override
    public Date convert(String s) {
        try {
            if(StringUtils.isEmpty(s)){
                throw  new RuntimeException("字符串不能为null");
            }
            SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
            return dateFormat.parse(s);
        } catch (ParseException e) {
            e.printStackTrace();
            return  null;
        }
    }
}
```

- 在springmvc.xml里面配置转换器

  spring 配置类型转换器的机制是，将自定义的转换器注册到类型转换服务中去 

```xml
  <!-- 配置类型转换器 -->
    <bean id="converterService"
          class="org.springframework.context.support.ConversionServiceFactoryBean">
        <!-- 给工厂注入一个新的类型转换器 -->
        <property name="converters">
            <array>
                <!-- 配置自定义类型转换器 -->
                <bean class="com.itheima.web.StringToDateConverter"></bean>
            </array>
        </property>
    </bean>
```

- 在 annotation-driven 标签中引用配置的类型转换服务 

```xml
<!--配置Spring开启mvc注解-->
<mvc:annotation-driven conversion-service="converterService"></mvc:annotation-driven>
```

## 知识点-常用注解

#### 1.RequestParam

- 作用：

  把请求中指定名称的参数给控制器中的形参赋值。 

- 属性

  value： 请求参数中的名称。
  required：请求参数中是否必须提供此参数。 默认值： true。表示必须提供，如果不提供将报错。 

  defaultValue:默认值

#### 2.RequestBody【重点】

- 作用

  1.用于获取请求体内容。 直接使用得到是 key=value&key=value...结构的字符串。

  2.把获得json类型的数据转成pojo对象(后面再讲)

  注意: get 请求方式不适用。 

- 属性

  required：是否必须有请求体。默认值是:true。当取值为 true 时,get 请求方式会报错。如果取值为 false， get 请求得到是 null。

#### 3.PathVariable【重点】

- 作用：

  用于绑定 url 中的占位符。 例如：请求 url 中 /delete/{id}， 这个{id}就是 url 占位符。
  url 支持占位符是 spring3.0 之后加入的。是 springmvc 支持 rest 风格 URL 的一个重要标志。

- 属性：

  value： 用于指定 url 中占位符名称。
  required：是否必须提供占位符。 

#第四章-响应数据和结果视图

## 返回值分类

1. 返回String.  返回值是逻辑视图, 通过视图解析器解析成物理视图
2. 返回void【了解】
3. 返回ModelAndView  
   - 设置数据  向request存
   - 设置视图  逻辑视图
##转发和重定向

#### 转发

```java
//转发到页面
return "forward:/WEB-INF/pages/success.jsp";
//转发到controller
return "forward:/response/testReturnModelAndView";
//相当于
request.getRequestDispatcher("url").forward(request,response)
```

#### 重定向

```java
//重定向到页面
return "redirect:/redirect.jsp";
//重定向到controller
return "redirect:/response/testReturnModelAndView";
//相当于
response.sendRedirect(url)
```

#### 转发和重定向区别

1. 转发是一次请求, 重定向是两次请求
2. 转发路径不会变化, 重定向的路径会改变
3. 转发只能转发到内部的资源,重定向可以重定向到内部的(当前项目里面的)也可以是外部的(项目以外的)
4. 转发可以转发到web-inf里面的资源, 重定向不可以重定向到web-inf里面的资

## ResponseBody响应 json数据【重点】

- 作用

将 controller 方法返回对象转换为 json 响应给客户端