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

  