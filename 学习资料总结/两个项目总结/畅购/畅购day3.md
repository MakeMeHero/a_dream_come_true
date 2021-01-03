 ## 1 SPU与SKU

  ### 1.1 SPU与SKU概念

  **SPU = Standard Product Unit  （标准产品单位）**

  - 概念 : SPU 是商品信息聚合的最小单位，是一组可复用、易检索的标准化信息的集合，该集合描述了一个产品的特性。

  - 通俗点讲，属性值、特性相同的货品就可以称为一个 SPU

    ==同款商品的公共属性抽取==

    

    例如：**华为P30 就是一个 SPU**

  **SKU=stock keeping unit( 库存量单位)**

  - SKU 即库存进出计量的单位， 可以是以件、盒、托盘等为单位。

  - SKU 是物理上不可分割的最小存货单元。在使用时要根据不同业态，不同管理模式来处理。

  - 在服装、鞋类商品中使用最多最普遍。

    例如：**华为P30 红色 64G 就是一个 SKU**

    ==某个库存单位的商品独有属性(某个商品的独有属性)==

  

  ### 1.2 表结构分析

  tb_spu  表 （SPU表）

| 字段名称       | 字段含义     | 字段类型 | 字段长度 | 备注 |
| -------------- | ------------ | -------- | -------- | ---- |
| id             | 主键         | BIGINT   |          |      |
| sn             | 货号         | VARCHAR  |          |      |
| name           | SPU名        | VARCHAR  |          |      |
| caption        | 副标题       | VARCHAR  |          |      |
| brand_id       | 品牌ID       | INT      |          |      |
| category1_id   | 一级分类     | INT      |          |      |
| category2_id   | 二级分类     | INT      |          |      |
| category3_id   | 三级分类     | INT      |          |      |
| template_id    | 模板ID       | INT      |          |      |
| freight_id     | 运费模板id   | INT      |          |      |
| image          | 图片         | VARCHAR  |          |      |
| images         | 图片列表     | VARCHAR  |          |      |
| sale_service   | 售后服务     | VARCHAR  |          |      |
| introduction   | 介绍         | TEXT     |          |      |
| spec_items     | 规格列表     | VARCHAR  |          |      |
| para_items     | 参数列表     | VARCHAR  |          |      |
| sale_num       | 销量         | INT      |          |      |
| comment_num    | 评论数       | INT      |          |      |
| is_marketable  | 是否上架     | CHAR     |          |      |
| is_enable_spec | 是否启用规格 | CHAR     |          |      |
| is_delete      | 是否删除     | CHAR     |          |      |
| status         | 审核状态     | CHAR     |          |      |

  tb_sku  表（SKU商品表）

| 字段名称      | 字段含义                        | 字段类型 | 字段长度 | 备注 |
| ------------- | ------------------------------- | -------- | -------- | ---- |
| id            | 商品id                          | BIGINT   |          |      |
| sn            | 商品条码                        | VARCHAR  |          |      |
| name          | SKU名称                         | VARCHAR  |          |      |
| price         | 价格（分）                      | INT      |          |      |
| num           | 库存数量                        | INT      |          |      |
| alert_num     | 库存预警数量                    | INT      |          |      |
| image         | 商品图片                        | VARCHAR  |          |      |
| images        | 商品图片列表                    | VARCHAR  |          |      |
| weight        | 重量（克）                      | INT      |          |      |
| create_time   | 创建时间                        | DATETIME |          |      |
| update_time   | 更新时间                        | DATETIME |          |      |
| spu_id        | SPUID                           | BIGINT   |          |      |
| category_id   | 类目ID                          | INT      |          |      |
| category_name | 类目名称                        | VARCHAR  |          |      |
| brand_name    | 品牌名称                        | VARCHAR  |          |      |
| spec          | 规格                            | VARCHAR  |          |      |
| sale_num      | 销量                            | INT      |          |      |
| comment_num   | 评论数                          | INT      |          |      |
| status        | 商品状态 1-正常，2-下架，3-删除 | CHAR     |          |      |

  2.4.1 查询分类

##### 2.4.1.1 分析

![1564377769398](E:/daybyday/04Changgou/day03/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/image/1564377769398.png)

在实现商品增加之前，需要先选择对应的分类，选择分类的时候，首选选择一级分类，然后根据选中的分类，将选中的分类作为查询的父ID，再查询对应的子分类集合，因此我们可以在后台编写一个方法，根据父类ID查询对应的分类集合即可。



##### 2.4.1.2 代码实现

修改`com.changgou.goods.service.impl.CategoryServiceImpl`添加上面的实现，代码如下：

```java
/***
 * 根据分类的父节点ID查询所有子节点
 * @param pid
 * @return
 */
@Override
public List<Category> findByParentId(Integer pid) {
    //SELECT * FROM tb_category WHERE parent_id=?
    Category category = new Category();
    category.setParentId(pid);
    return categoryMapper.select(category);
}
```

#### 2.4.2 模板查询(规格参数组)

同学作业



##### 2.4.2.1 分析

![1564350522922](E:/daybyday/04Changgou/day03/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/image/qqqqqq.png)

如上图，当用户选中了分类后，需要根据分类的ID查询出对应的模板数据，并将模板的名字显示在这里，模板表结构如下：

```sql
CREATE TABLE `tb_template` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `name` varchar(50) DEFAULT NULL COMMENT '模板名称',
  `spec_num` int(11) DEFAULT '0' COMMENT '规格数量',
  `para_num` int(11) DEFAULT '0' COMMENT '参数数量',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=44 DEFAULT CHARSET=utf8;
```

修改`com.changgou.goods.service.impl.TemplateServiceImpl`添加上面方法的实现：

```java
@Autowired
private CategoryMapper categoryMapper;

/***
 * 根据分类ID查询模板信息
 * @param id
 * @return
 */
@Override
public Template findByCategoryId(Integer id) {
    //查询分类信息
    Category category = categoryMapper.selectByPrimaryKey(id);

    //根据模板Id查询模板信息
    return templateMapper.selectByPrimaryKey(category.getTemplateId());
}
```

#### 2.4.3 查询分类品牌数据

##### 2.4.3.1 分析

![1564378095781](E:/daybyday/04Changgou/day03/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/image/1564378095781.png)

用户每次选择了分类之后，可以根据用户选择的分类到`tb_category_brand`表中查询指定的品牌集合ID,然后根据品牌集合ID查询对应的品牌集合数据，再将品牌集合数据拿到这里来展示即可实现上述功能。



##### 2.4.3.2 代码实现

(1)Dao实现

修改`com.changgou.goods.dao.BrandMapper`添加根据分类ID查询对应的品牌数据，代码如下：

```java
public interface BrandMapper extends Mapper<Brand> {

    /***
     * 查询分类对应的品牌集合
     */
    @Select("SELECT tb.* FROM tb_category_brand tcb,tb_brand tb WHERE tcb.category_id=#{categoryid} AND tb.id=tcb.brand_id")
    List<Brand> findByCategory(Integer categoryid);
}
```

#### 2.4.4 规格查询

##### 2.4.4.1 分析

![1564350812642](E:/daybyday/04Changgou/day03/%E4%B8%8A%E8%AF%BE%E8%B5%84%E6%96%99/%E8%AE%B2%E4%B9%89/image/1564350812642.png)

用户选择分类后，需要根据所选分类对应的模板ID查询对应的规格，规格表结构如下：

```sql
CREATE TABLE `tb_spec` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT 'ID',
  `name` varchar(50) DEFAULT NULL COMMENT '名称',
  `options` varchar(2000) DEFAULT NULL COMMENT '规格选项',
  `seq` int(11) DEFAULT NULL COMMENT '排序',
  `template_id` int(11) DEFAULT NULL COMMENT '模板ID',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=40 DEFAULT CHARSET=utf8;
```

修改`com.changgou.goods.service.impl.SpecServiceImpl`添加上面方法的实现，代码如下：

```java
@Autowired
private CategoryMapper categoryMapper;

/***
 * 根据分类ID查询规格列表
 * @param categoryid
 * @return
 */
@Override
public List<Spec> findByCategoryId(Integer categoryid) {
    //查询分类
    Category category = categoryMapper.selectByPrimaryKey(categoryid);
    //根据分类的模板ID查询规格
    Spec spec = new Spec();
    spec.setTemplateId(category.getTemplateId());
    return specMapper.select(spec);
}
```

#### 2.4.6 SPU+SKU保存

##### 2.4.6.1 分析

保存商品数据的时候，需要保存Spu和Sku，一个Spu对应多个Sku，我们可以先构建一个Goods对象，将`Spu`和`List<Sku>`组合到一起,前端将2者数据提交过来，再实现添加操作。



##### 2.4.62 代码实现

(1)Pojo改造

修改changgou-service-goods-api工程创建组合实体类，创建com.changgou.goods.pojo.Goods,代码如下：

```java
public class Goods implements Serializable {
    //SPU
    private Spu spu;
    //SKU集合
    private List<Sku> skuList;

    //..get..set..toString
}
```



(2) 业务层

修改com.changgou.goods.service.SpuService接口，添加保存Goods方法，代码如下：

```java
/**
 * 保存商品
 * @param goods
 */
void saveGoods(Goods goods);
```



修改com.changgou.goods.service.impl.SpuServiceImpl类，添加保存Goods的方法实现，代码如下：

```java
@Autowired
private IdWorker idWorker;

@Autowired
private CategoryMapper categoryMapper;

@Autowired
private BrandMapper brandMapper;

@Autowired
private SkuMapper skuMapper;

/***
 * 保存Goods
 * @param goods
 */
@Override
public void saveGoods(Goods goods) {
    //增加Spu
    Spu spu = goods.getSpu();
    spu.setId(idWorker.nextId());
    spuMapper.insertSelective(spu);

    //增加Sku
    Date date = new Date();
    Category category = categoryMapper.selectByPrimaryKey(spu.getCategory3Id());
    Brand brand = brandMapper.selectByPrimaryKey(spu.getBrandId());
    //获取Sku集合
    List<Sku> skus = goods.getSkus();
    //循环将数据加入到数据库
    for (Sku sku : skus) {
        //构建SKU名称，采用SPU+规格值组装
        if(StringUtils.isEmpty(sku.getSpec())){
            sku.setSpec("{}");
        }
        //获取Spu的名字
        String name = spu.getName();

        //将规格转换成Map
        Map<String,String> specMap = JSON.parseObject(sku.getSpec(), Map.class);
        //循环组装Sku的名字
        for (Map.Entry<String, String> entry : specMap.entrySet()) {
            name+="  "+entry.getValue();
        }
        sku.setName(name);
        //ID
        sku.setId(idWorker.nextId());
        //SpuId
        sku.setSpuId(spu.getId());
        //创建日期
        sku.setCreateTime(date);
        //修改日期
        sku.setUpdateTime(date);
        //商品分类ID
        sku.setCategoryId(spu.getCategory3Id());
        //分类名字
        sku.setCategoryName(category.getName());
        //品牌名字
        sku.setBrandName(brand.getName());
        //增加
        skuMapper.insertSelective(sku);
    }
}
```



(3)控制层

修改com.changgou.goods.controller.SpuController，增加保存Goods方法，代码如下：

```java
/***
 * 添加Goods
 * @param goods
 * @return
 */
@PostMapping("/save")
public Result save(@RequestBody Goods goods){
    spuService.saveGoods(goods);
    return new Result(true,StatusCode.OK,"保存成功");
}
```

#### 2.4.8 保存修改

修改changgou-service-goods的SpuServiceImpl的saveGoods方法，修改添加SPU部分代码：

如果传入数据没有ID表示修改,有ID表示新增

上图代码如下：

```java
if(spu.getId()==null){
    //增加
    spu.setId(idWorker.nextId());
    spuMapper.insertSelective(spu);
}else{
    //修改数据
    spuMapper.updateByPrimaryKeySelective(spu);
    //删除该Spu的Sku
    Sku sku = new Sku();
    sku.setSpuId(spu.getId());
    skuMapper.delete(sku);
}
```

## 3 商品审核与上下架

### 3.1 需求分析

商品新增后，审核状态为0（未审核），默认为下架状态。

审核商品，需要校验是否是被删除的商品，如果未删除则修改审核状态为1

下架商品，需要校验是否是被删除的商品，如果未删除则修改上架状态为0

上架商品，需要校验是否被删除的商品,如果未被删除,则需要审核通过的商品,才能上架.



### 3.2 实现思路

（1）按照ID查询SPU信息

（2）判断修改审核、上架和下架状态

（3）保存SPU



### 3.3 代码实现

#### 3.3.1 商品审核

实现审核通过，自动上架。

修改changgou-service-goods工程的com.changgou.goods.service.impl.SpuServiceImpl类，添加audit方法，代码如下：

```java
/***
 * 商品审核
 * @param spuId
 */
@Override
public void audit(Long spuId) {
    //查询商品
    Spu spu = spuMapper.selectByPrimaryKey(spuId);
    //判断商品是否已经删除
    if(spu.getIsDelete().equalsIgnoreCase("1")){
        throw new RuntimeException("该商品已经删除！");
    }
    //实现审核
    spu.setStatus("1"); //审核通过    
    spuMapper.updateByPrimaryKeySelective(spu);
}
```

#### 3.3.4 批量上架

前端传递一组商品ID，后端进行批量上下架处理

修改com.changgou.goods.service.impl.SpuServiceImpl，添加批量上架方法实现，代码如下：

```java
/***
 * 批量上架
 * @param ids:需要上架的商品ID集合
 * @return
 */
@Override
public int putMany(Long[] ids) {
    Spu spu=new Spu();
    spu.setIsMarketable("1");//上架
    //批量修改
    Example example=new Example(Spu.class);
    Example.Criteria criteria = example.createCriteria();
    criteria.andIn("id", Arrays.asList(ids));//id
    //下架
    criteria.andEqualTo("isMarketable","0");
    //审核通过的
    criteria.andEqualTo("status","1");
    //非删除的
    criteria.andEqualTo("isDelete","0");
    return spuMapper.updateByExampleSelective(spu, example);
}
```

## 4 删除与还原商品

### 4.1 需求分析

请看管理后台的静态原型

商品列表中的删除商品功能，并非真正的删除，而是将删除标记的字段设置为1，

在回收站中有恢复商品的功能，将删除标记的字段设置为0

在回收站中有删除商品的功能，是真正的物理删除。



### 4.2 实现思路

逻辑删除商品，修改spu表is_delete字段为1

商品回收站显示spu表is_delete字段为1的记录

回收商品，修改spu表is_delete字段为0



### 4.3 代码实现

#### 4.3.1 逻辑删除商品

(1)业务层

修改com.changgou.goods.service.SpuService接口，增加logicDelete方法，代码如下：

```java
/***
 * 逻辑删除
 * @param spuId
 */
void logicDelete(Long spuId);
```



修改com.changgou.goods.service.impl.SpuServiceImpl，添加logicDelete方法实现，代码如下：

```java
/***
 * 逻辑删除
 * @param spuId
 */
@Override
@Transactional
public void logicDelete(Long spuId) {
    Spu spu = spuMapper.selectByPrimaryKey(spuId);
    //检查是否下架的商品
    if(!spu.getIsMarketable().equals("0")){
        throw new RuntimeException("必须先下架再删除！");
    }
    //删除
    spu.setIsDelete("1");
    //未审核
    spu.setStatus("0");
    spuMapper.updateByPrimaryKeySelective(spu);
}
```



(2)控制层

修改com.changgou.goods.controller.SpuController，添加logicDelete方法，如下：

```java
/**
 * 逻辑删除
 * @param id
 * @return
 */
@DeleteMapping("/logic/delete/{id}")
public Result logicDelete(@PathVariable Long id){
    spuService.logicDelete(id);
    return new Result(true,StatusCode.OK,"逻辑删除成功！");
}
```



#### 4.3.2 还原被删除的商品

(1)业务层

修改com.changgou.goods.service.SpuService接口，添加restore方法代码如下：

```java
/***
 * 还原被删除商品
 * @param spuId
 */
void restore(Long spuId);
```

修改com.changgou.goods.service.impl.SpuServiceImpl类，添加restore方法，代码如下：

```java
/**
 * 恢复数据
 * @param spuId
 */
@Override
public void restore(Long spuId) {
    Spu spu = spuMapper.selectByPrimaryKey(spuId);
    //检查是否删除的商品
    if(!spu.getIsDelete().equals("1")){
        throw new RuntimeException("此商品未删除！");
    }
    //未删除
    spu.setIsDelete("0");
    //未审核
    spu.setStatus("0");
    spuMapper.updateByPrimaryKeySelective(spu);
}
```



(2)控制层

修改com.changgou.goods.controller.SpuController，添加restore方法，代码如下：

```java
/**
 * 恢复数据
 * @param id
 * @return
 */
@PutMapping("/restore/{id}")
public Result restore(@PathVariable Long id){
    spuService.restore(id);
    return new Result(true,StatusCode.OK,"数据恢复成功！");
}
```





#### 4.3.3 物理删除商品

修改com.changgou.goods.service.impl.SpuServiceImpl的delete方法,代码如下：

```java
/**
 * 删除
 * @param id
 */
@Override
public void delete(Long id){
    Spu spu = spuMapper.selectByPrimaryKey(id);
    //检查是否被逻辑删除  ,必须先逻辑删除后才能物理删除
    if(!spu.getIsDelete().equals("1")){
        throw new RuntimeException("此商品不能删除！");
    }
    spuMapper.deleteByPrimaryKey(id);
}
```

