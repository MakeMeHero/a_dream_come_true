#### 2.2.3 系统架构图

![1560090475333](E:/daybyday/04Changgou/day01/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/image/1560090475333.png)



数据库脚本：`资料\数据库脚本`

![1564094555825](E:/daybyday/04Changgou/day01/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/image/1564094555825.png)



### 3.2 项目结构说明

![1584505209554](E:/daybyday/04Changgou/day01/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/image/1584505209554.png)



分析:

```properties
+ changgou-parent 管理所有的版本统一  pom
    +changgou-common 工具类 jar
    +changgou-common-db 微服务使用到的jar
    
    +changgou-service  聚合工程 管理微服务工程 pom
	  
        + changgou-service-goods  
        + changgou-service-order  依赖于API
        + changgou-service-user	  依赖于api

    +changgou-service-api 聚合工程 统一管理fegin pojo  pom
        +changgou-service-goods-api (有feign 有POJO)   jar 
        +changgou-service-order-api(有feign 有POJO)   JAR
    +changgou-gateway	 聚合工程 统一管理网关系统
        + changgou-gateway-admin   后台相关
        + changgou-gateway-web	   前端相关
        + ....

    +changgou-euereka-server

        +changgou-web 聚合工程 统一管理web相关的微服务 
        +changgou-web-item
        +changgou-web-manager: 调用订单微服务调用商品微服务 调用用户微服务聚合数据统一返回给页面。
        +....
        +...
```



结构说明：

changgou-gateway

```properties
网关模块，根据网站的规模和需要，可以将综合逻辑相关的服务用网关路由组合到一起。在这里还可以做鉴权和限流相关操作。
```

changgou-service

```properties
微服务模块，该模块用于存放所有独立的微服务工程。
```

changgou-service-api

```properties
对应工程的JavaBean、Feign、以及Hystrix配置，该工程主要对外提供依赖。
```

changgou-web

```properties
web服务工程，对应功能模块如需要调用多个微服务，可以将他们写入到该模块中，例如网站后台、网站前台等
```

## 4 商品微服务-品牌增删改查

### 4.2 表结构分析

品牌表：tb_brand

| 字段名称 | 字段含义     | 字段类型 | 字段长度 | 备注 |
| -------- | ------------ | -------- | -------- | ---- |
| id       | 品牌id       | INT      |          |      |
| name     | 品牌名称     | VARCHAR  |          |      |
| image    | 品牌图片地址 | VARCHAR  |          |      |
| letter   | 品牌的首字母 | CHAR     |          |      |
| seq      | 排序         | INT      |          |      |



### 4.3 代码实现

上面品牌表对应Brand实体类

```java
@Table(name="tb_brand")
public class Brand implements Serializable{
	@Id
	private Integer id;//品牌id
	private String name;//品牌名称
	private String image;//品牌图片地址
	private String letter;//品牌的首字母
	private Integer seq;//排序
	
	// getter and setter  .....(省略)
}
```

@Table和@Id都是JPA注解，@Table用于配置表与实体类的映射关系，@Id用于标识主键属性。



#### 4.3.1 品牌列表

(1)Dao创建

在changgou-service-goods微服务下创建com.changgou.goods.dao.BrandMapper接口，代码如下：

```java
public interface BrandMapper extends Mapper<Brand> {
}
```

继承了Mapper接口，就自动实现了增删改查的常用方法。

#### 4.3.1 品牌列表

(1)Dao创建

在changgou-service-goods微服务下创建com.changgou.goods.dao.BrandMapper接口，代码如下：

```java
public interface BrandMapper extends Mapper<Brand> {
}
```

继承了Mapper接口，就自动实现了增删改查的常用方法。



(2)业务层

创建com.changgou.goods.service.BrandService接口，代码如下：

```java
public interface BrandService {

    /***
     * 查询所有品牌
     * @return
     */
    List<Brand> findAll();
}
```



创建com.changgou.goods.service.impl.BrandServiceImpl实现类，代码如下：

```java
@Service
public class BrandServiceImpl {

    @Autowired
    private BrandMapper brandMapper;

    /**
     * 全部数据
     * @return
     */
    public List<Brand> findAll(){
        return brandMapper.selectAll();
    }
}
```

(3)控制层

控制层  com.changgou.goods包下创建controller包  ，包下创建类

```java
@RestController
@RequestMapping("/brand")
@CrossOrigin
public class BrandController {

    @Autowired
    private BrandService brandService;

    /***
     * 查询全部数据
     * @return
     */
    @GetMapping
    public Result<Brand> findAll(){
        List<Brand> brandList = brandService.findAll();
        return new Result<Brand>(true, StatusCode.OK,"查询成功",brandList) ;
    }
}
```

4.3.2 根据ID查询品牌       brandMapper.selectByPrimaryKey(id);

4.3.4 修改品牌                    brandMapper.updateByPrimaryKeySelective(brand);

4.3.5 删除品牌               brandMapper.deleteByPrimaryKey(id);

修改com.changgou.goods.service.impl.BrandServiceImpl，添加根据多条件搜索品牌方法的实现，代码如下：

```java
/**
 * 条件查询
 * @param brand
 * @return
 */
@Override
public List<Brand> findList(Brand brand){
    //构建查询条件
    Example example = createExample(brand);
    //根据构建的条件查询数据
    return brandMapper.selectByExample(example);
}


/**
 * 构建查询对象
 * @param brand
 * @return
 */
public Example createExample(Brand brand){
    Example example=new Example(Brand.class);
    Example.Criteria criteria = example.createCriteria();
    if(brand!=null){
        // 品牌名称
        if(!StringUtils.isEmpty(brand.getName())){
            criteria.andLike("name","%"+brand.getName()+"%");
        }
        // 品牌图片地址
        if(!StringUtils.isEmpty(brand.getImage())){
            criteria.andLike("image","%"+brand.getImage()+"%");
        }
        // 品牌的首字母
        if(!StringUtils.isEmpty(brand.getLetter())){
            criteria.andLike("letter","%"+brand.getLetter()+"%");
        }
        // 品牌id
        if(!StringUtils.isEmpty(brand.getLetter())){
            criteria.andEqualTo("id",brand.getId());
        }
        // 排序
        if(!StringUtils.isEmpty(brand.getSeq())){
            criteria.andEqualTo("seq",brand.getSeq());
        }
    }
    return example;
}
```

#### 4.3.7 品牌列表分页查询

(1)业务层

修改com.changgou.goods.service.BrandService添加分页方法，代码如下：

```java
/***
 * 分页查询
 * @param page
 * @param size
 * @return
 */
PageInfo<Brand> findPage(int page, int size);
```



修改com.changgou.goods.service.impl.BrandServiceImpl添加分页方法实现，代码如下：

```java
/**
 * 分页查询
 * @param page
 * @param size
 * @return
 */
@Override
public PageInfo<Brand> findPage(int page, int size){
    //静态分页
    PageHelper.startPage(page,size);
    //分页查询
    return new PageInfo<Brand>(brandMapper.selectAll());
}
```



(2)控制层

BrandController新增方法

```java
/***
 * 分页搜索实现
 * @param page:当前页
 * @param size:每页显示多少条
 * @return
 */
@GetMapping(value = "/search/{page}/{size}" )
public Result<PageInfo> findPage(@PathVariable  int page, @PathVariable  int size){
    //分页查询
    PageInfo<Brand> pageInfo = brandService.findPage(page, size);
    return new Result<PageInfo>(true,StatusCode.OK,"查询成功",pageInfo);
}
```

####4.3.8 品牌列表条件+分页查询

修改com.changgou.goods.service.impl.BrandServiceImpl，添加多条件分页查询方法代码如下：

```java
/**
 * 条件+分页查询
 * @param brand 查询条件
 * @param page 页码
 * @param size 页大小
 * @return 分页结果
 */
@Override
public PageInfo<Brand> findPage(Brand brand, int page, int size){
    //分页
    PageHelper.startPage(page,size);
    //搜索条件构建
    Example example = createExample(brand);
    //执行搜索
    return new PageInfo<Brand>(brandMapper.selectByExample(example));
}
```

#### 4.3.9 公共异常处理

为了使我们的代码更容易维护，我们创建一个类集中处理异常,该异常类可以创建在changgou-common工程中，创建com.changgou.framework.exception.BaseExceptionHandler，代码如下：

```java
@ControllerAdvice
public class BaseExceptionHandler {

    /***
     * 异常处理
     * @param e
     * @return
     */
    @ExceptionHandler(value = Exception.class)
    @ResponseBody
    public Result error(Exception e) {
        e.printStackTrace();
        return new Result(false, StatusCode.ERROR, e.getMessage());
    }
}
```

注意：@ControllerAdvice注解，全局捕获异常类，只要作用在@RequestMapping上，所有的异常都会被捕获。