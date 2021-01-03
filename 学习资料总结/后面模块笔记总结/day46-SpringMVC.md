# 第一章-SpringMVC 实现文件上传

##springmvc 传统方式文件上传

- 创建前端页面

```html
<h1>一,springmvc传统方式文件上传</h1>
<form action="springmvc/fileUpload02" method="post" entype="multipart/form-data">
    图片： <input type="file" name="upload"/><br/>
    图片描述:<input type="text" name="pdesc"/>
    <input type="submit" value="上传"/>
</form>
```

- 控制器

```java
    @RequestMapping("fileUpload02")
    public String fileUpload02(HttpServletRequest request, String pdesc, MultipartFile upload) throws IOException {
        System.out.println("pdesc="+pdesc);
        //1. 获得文件名
        String filename = upload.getOriginalFilename();
        //2. 获得随机文件名
        String uuidName = UploadUtils.getUUIDName(filename);
        //3. 获得绝对路径
        String basePath = request.getSession().getServletContext().getRealPath("/upload");
        //4. 获得两层目录
        String dir = UploadUtils.getDir();
        //5.创建两层目录
        File fileDir = new File(basePath, dir);
        if(!fileDir.exists()){
            fileDir.mkdirs();
        }
        //6.使用 MulitpartFile 接口中方法，把上传的文件写到指定位置
        upload.transferTo(new File(fileDir,uuidName));

        return  "success";
    }
```

- 在springmvc.xml配置文件解析器 

  注意：文件上传的解析器 ==id 是固定的，不能起别的名称==，否则无法实现请求参数的绑定。（不光是文件，其他
  字段也将无法绑定） 

```xml
    <!--配置文件上传解析器-->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 设置上传文件的最大尺寸为 5MB --> 
        <property name="maxUploadSize" value="5242880"></property>
    </bean>
```

##springmvc 跨服务器方式的文件上传

- 前端页面

```html
<h1>二,springmvc 跨服务器方式的文件上传</h1>
<form action="springmvc/fileUpload03" method="post" enctype="multipart/form-data">
    图片： <input type="file" name="upload"/><br/>
    图片描述:<input type="text" name="pdesc"/>
    <input type="submit" value="上传"/>
</form>
```

- 控制器

```java
	private static final String FILESERVERURL="http://localhost:9090/upload/";   

	@RequestMapping("fileUpload03")
    public String fileUpload03(String pdesc, MultipartFile upload) throws IOException {
        System.out.println("pdesc="+pdesc);
        //1. 获得文件名
        String filename = upload.getOriginalFilename();
        //2. 获得随机文件名
        String uuidName = UploadUtils.getUUIDName(filename);
        //3.创建jersey包中提供的client对象
        Client client = Client.create();//它就可以和文件服务器建立联系
        //4.使用client和文件服务器建立联系
        WebResource resource = client.resource(FILESERVERURL+uuidName);
        //5.把web资源对象写到文件服务器
        resource.put(upload.getBytes());
        return "success";
    }
```

# 第二章-SpringMVC 中的异常处理【重点】

#### 1.自定义异常类

目的: 统一的管理异常, 方便统一管理错误提示语

```java
/**
 * 自定义异常类
 */
public class SysException extends  Exception{
    private String msg;
    public SysException(String msg) {
        this.msg = msg;
    }
    public void setMsg(String msg) {
        this.msg = msg;
    }
    public String getMsg() {
        return msg;
    }
}
```

#### 2.自定义异常处理器

```java
/**
 * 自定义异常处理器
 */
public class SysExceptionResolver implements HandlerExceptionResolver {

    @Nullable
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, @Nullable Object handler, Exception ex) {
        SysException sysException = null;
        if(ex instanceof SysException){
            sysException = (SysException) ex;
        }else{
            sysException = new SysException("服务器异常,请联系管理员");
        }
        ModelAndView mv = new ModelAndView();
        mv.setViewName("msg");
        mv.addObject("msg",sysException.getMsg());
        return mv;
    }
}
```

#### 3.配置异常处理器

- 在springmvc.xml配置

```xml
 <!--注册异常处理器-->
<bean id="sysExceptionResolver" class="com.itheima.web.SysExceptionResolver"></bean>
```

# 第三章-SpringMVC 中的拦截器

- 编写一个普通类实现 HandlerInterceptor 接口 

```java
public class Intercepter01 implements HandlerInterceptor {
    @Override
    //在达到目标方法之前执行(拦截的方法)
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle 执行了...");
        //转发到拦截后的页面
        //request.getRequestDispatcher("/intercepter01.jsp").forward(request,response);
        //转发到controller
        request.getRequestDispatcher("/demo01/fun02").forward(request,response);
        return false;//返回true放行, 返回false拦截
    }
```

- 在springmvc.xml配置拦截器 

```xml
    <!--配置拦截器-->
    <mvc:interceptors>
        <mvc:interceptor>
            <!--用于指定拦截的路径-->
            <mvc:mapping path="/**"/>
            <!--用于指定忽略(不拦截的路径)-->
            <mvc:exclude-mapping path="/demo01/fun02"/>
            <bean id="intercepter01" class="com.itheima.web.interceptor.Intercepter01"></bean>
        </mvc:interceptor>
    </mvc:interceptors>
```

