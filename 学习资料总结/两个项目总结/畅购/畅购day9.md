### 1.2 单点登录

![1558173244406](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558173244406.png)

用户访问的项目中，至少有3个微服务需要识别用户身份，如果用户访问每个微服务都登录一次就太麻烦了，为了提高用户的体验，我们需要实现让用户在一个系统中登录，其他任意受信任的系统都可以访问，这个功能就叫单点登录。

单点登录（Single Sign On），简称为 SSO，是目前比较流行的企业业务整合的解决方案之一。 SSO的定义是在多个应用系统中，用户只需要登录一次就可以访问所有相互信任的应用系统    

### 2.1 单点登录技术方案

分布式系统要实现单点登录，通常将认证系统独立抽取出来，并且将用户身份信息存储在单独的存储介质，比如： MySQL、Redis，考虑性能要求，通常存储在Redis中，如下图：

![1558175040643](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558175040643.png)

单点登录的特点是： 

```
1、认证系统为独立的系统。 
2、各子系统通过Http或其它协议与认证系统通信，完成用户认证。 
3、用户身份信息存储在Redis集群。
```

 Java中有很多用户认证的框架都可以实现单点登录：

```
 1、Apache Shiro. 
 2、CAS 
 3、Spring security CAS    
```

### 2.2 Oauth2认证

OAuth（开放授权）是一个开放标准，允许用户授权第三方移动应用访问他们存储在另外的服务提供者上的信息，而不需要将用户名和密码提供给第三方移动应用或分享他们数据的所有内容，OAuth2.0是OAuth协议的延续版本。



#### 2.2.1 Oauth2认证流程

第三方认证技术方案最主要是解决认证协议的通用标准 问题，因为要实现 跨系统认证，各系统之间要遵循一定的接口协议。
OAUTH协议为用户资源的授权提供了一个安全的、开放而又简易的标准。同时，任何第三方都可以使用OAUTH认证服务，任何服务提供商都可以实现自身的OAUTH认证服务，因而OAUTH是开放的。业界提供了OAUTH的多种实现如PHP、JavaScript，Java，Ruby等各种语言开发包，大大节约了程序员的时间，因而OAUTH是简易的。互联网很多服务如Open API，很多大公司如Google，Yahoo，Microsoft等都提供了OAUTH认证服务，这些都足以说明OAUTH标准逐渐成为开放资源授权的标准。

### 2.3 Spring security Oauth2认证解决方案

本项目采用 Spring security + Oauth2完成用户认证及用户授权，Spring security 是一个强大的和高度可定制的身份验证和访问控制框架，Spring security 框架集成了Oauth2协议，下图是项目认证架构图：    

![1558176649652](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558176649652.png)

1、用户请求认证服务完成认证。 

2、认证服务下发用户身份令牌，拥有身份令牌表示身份合法。 

3、用户携带令牌请求资源服务，请求资源服务必先经过网关。 

4、网关校验用户身份令牌的合法，不合法表示用户没有登录，如果合法则放行继续访问。 

5、资源服务获取令牌，根据令牌完成授权。 

6、资源服务完成授权则响应资源信息。

### 3.3 Oauth2授权模式

#### 3.3.1 Oauth2授权模式

Oauth2有以下授权模式： 

```properties
1.授权码模式（Authorization Code）
2.隐式授权模式（Implicit） 
3.密码模式（Resource Owner Password Credentials） 
4.客户端模式（Client Credentials） 
```

其中授权码模式和密码模式应用较多，本小节介绍授权码模式。



#### 3.3.2 授权码授权实现

上边例举的黑马程序员网站使用QQ认证的过程就是授权码模式，流程如下： 

1、客户端请求第三方授权 

2、用户(资源拥有者)同意给客户端授权 

3、客户端获取到授权码，请求认证服务器申请 令牌 

4、认证服务器向客户端响应令牌 

5、客户端请求资源服务器的资源，资源服务校验令牌合法性，完成授权 

6、资源服务器返回受保护资源    



**(1)申请授权码**

请求认证服务获取授权码：

```
Get请求：
http://localhost:9001/oauth/authorize?client_id=changgou&response_type=code&scop=app&redirect_uri=http://localhost
```

参数列表如下： 

```properties
client_id：客户端id，和授权配置类中设置的客户端id一致。 
response_type：授权码模式固定为code 
scop：客户端范围，和授权配置类中设置的scop一致。 
redirect_uri：跳转uri，当授权码申请成功后会跳转到此地址，并在后边带上code参数（授权码）
```

 首先跳转到登录页面：

![1565035634997](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1565035634997.png)

输入账号和密码，账号随便填,例如szitheima，密码一定要叫做szitheima.(暂时写死，后面改造)

点击Sign in, Spring Security接收到请求会调用UserDetailsService接口的 方法进行调用并认证，只要认证通过即可进入授权页面，如下图，接下来进入授权页面：

![1565035586067](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1565035586067.png)   

点击Authorize,接下来返回授权码： 认证服务携带授权码跳转redirect_uri,code=k45iLY就是返回的授权码

![1558181855325](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558181855325.png)



**(2)申请令牌**

拿到授权码后，申请令牌。 Post请求：http://localhost:9001/oauth/token 参数如下： 

```
grant_type：授权类型，填写authorization_code，表示授权码模式 
code：授权码，就是刚刚获取的授权码，注意：授权码只使用一次就无效了，需要重新申请。 
redirect_uri：申请授权码时的跳转url，一定和申请授权码时用的redirect_uri一致。 
```

此链接需要使用 http Basic认证。 什么是http Basic认证？ http协议定义的一种认证方式，将客户端id和客户端密码按照“客户端ID:客户端密码”的格式拼接，并用base64编 码，放在header中请求服务端，一个例子： Authorization：Basic WGNXZWJBcHA6WGNXZWJBcHA=WGNXZWJBcHA6WGNXZWJBcHA= 是用户名:密码的base64编码。 认证失败服务端返回 401 Unauthorized。

以上测试使用postman完成： 

http basic认证：    

![1558182132328](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558182132328.png)

![1558182177167](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558182177167.png)

客户端Id和客户端密码会匹配数据库oauth_client_details表中的客户端id及客户端密码。    

点击发送： 申请令牌成功    

![](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1562408718454.png)

返回信如下:

```properties
access_token：访问令牌，携带此令牌访问资源 
token_type：有MAC Token与Bearer Token两种类型，两种的校验算法不同，RFC 6750建议Oauth2采用 Bearer Token（http://www.rfcreader.com/#rfc6750）。 
refresh_token：刷新令牌，使用此令牌可以延长访问令牌的过期时间。 
expires_in：过期时间，单位为秒。 
scope：范围，与定义的客户端范围一致。    
jti：当前token的唯一标识
```



**(3)令牌校验**

Spring Security Oauth2提供校验令牌的端点，如下： 

Get: http://localhost:9001/oauth/check_token?token= [access_token]

参数： 

token：令牌 

使用postman测试如下:

 1.填写如下图所示

![1585042410531](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1585042410531.png)

2.发送请求测试

![1562409096845](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1562409096845.png)





如果令牌校验失败，会出现如下结果：

![1562409180948](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1562409180948.png)

如果令牌过期了，会如下如下结果：

![1562409507493](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1562409507493.png)



**(4)刷新令牌**

刷新令牌是当令牌快过期时重新生成一个令牌，它于授权码授权和密码授权生成令牌不同，刷新令牌不需要授权码 也不需要账号和密码，只需要一个刷新令牌、客户端id和客户端密码。 

测试如下： Post：http://localhost:9001/oauth/token 

参数：    

grant_type： 固定为 refresh_token 

refresh_token：刷新令牌（注意不是access_token，而是refresh_token）  

注意：

填写的时候不要有空格 ，另外也需要填写客户端新  

1）

![1585042410531](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1585042410531.png)

2）

![1562409616911](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1562409616911.png)







#### 3.3.3 密码授权实现

**(1)认证**

密码模式（Resource Owner Password Credentials）与授权码模式的区别是申请令牌不再使用授权码，而是直接 通过用户名和密码即可申请令牌。 

测试如下： 

Post请求：http://localhost:9001/oauth/token 

参数： 

grant_type：密码模式授权填写password 

username：账号 

password：密码 

并且此链接需要使用 http Basic认证。

![1558221388787](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558221388787.png)

![1558221423631](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558221423631.png)



测试数据如下：

![1562462930730](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1562462930730.png)



**(2)校验令牌**

Spring Security Oauth2提供校验令牌的端点，如下： 

Get: http://localhost:9001/oauth/check_token?token= 

参数： 

token：令牌 

使用postman测试如下:

![1558221673028](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558221673028.png)

返回结果：

```properties
{
    "companyId": null,
    "userpic": null,
    "scope": [
        "app"
    ],
    "name": null,
    "utype": null,
    "active": true,
    "id": null,
    "exp": 1990221534,
    "jti": "5b96666e-436b-4301-91b5-d89f9bbe6edb",
    "client_id": "changgou",
    "user_name": "szitheima"
}
```

exp：过期时间，long类型，距离1970年的秒数（new Date().getTime()可得到当前时间距离1970年的毫秒数）。 

user_name： 用户名 

client_id：客户端Id，在oauth_client_details中配置 

scope：客户端范围，在oauth_client_details表中配置 

jti：与令牌对应的唯一标识 companyId、userpic、name、utype、

id：这些字段是本认证服务在Spring Security基础上扩展的用户身份信息    



**(3)刷新令牌**

刷新令牌是当令牌快过期时重新生成一个令牌，它于授权码授权和密码授权生成令牌不同，刷新令牌不需要授权码 也不需要账号和密码，只需要一个刷新令牌、客户端id和客户端密码。 

测试如下： Post：http://localhost:9001/oauth/token 

参数：    

grant_type： 固定为 refresh_token 

refresh_token：刷新令牌（注意不是access_token，而是refresh_token）    

![1558221864957](E:/daybyday/04Changgou/day09/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/images/1558221864957.png)

刷新令牌成功，会重新生成新的访问令牌和刷新令牌，令牌的有效期也比旧令牌长。 

刷新令牌通常是在令牌快过期时进行刷新 。







## 4 资源服务授权