1.@Controller 用于控制层注解,注解在类上的，被注解的类将被spring初始话
为一个bean，然后统一管理
2.@ResponseBody：作用于方法上，可以将整个返回结果以某种格式返回，如
json或xml格式
3.@RequestMapping：用于处理请求地址映射，可以作用于类和方法上
4.@RequestParam：作用于参数前,用于获取传入参数的值,
5.@requestBody作用于参数前,可以将请求体中的JSON字符串绑定到相应的bean
上
6@PathViriable：作用于参数前,用于定义路径参数值