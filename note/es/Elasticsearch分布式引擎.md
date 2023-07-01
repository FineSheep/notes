# Elasticsearch分布式引擎

## 相关概念

> [elasticsearch](https://so.csdn.net/so/search?q=elasticsearch&spm=1001.2101.3001.7020)是面向**文档（Document）**存储的，可以是数据库中的一条商品数据，一个订单信息。文档数据会被序列化为json格式后存储在elasticsearch中。

### 倒排索引

**首先，倒排索引的概念是基于 MySQL 这样的正向索引而言的。**

那么我们先讲何为正向索引。例如给下表（tb_goods）中的 id 创建索引

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561826536-ee5a9a2b-3c28-4727-afa8-d4967d4da36c.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918202527.png)

如果是根据 id 查询，那么直接走索引，查询速度非常快。

但如果是基于 title 做模糊查询，只能是逐行扫描数据，流程如下：

1. 用户搜索数据，条件是 title 符合 "%手机%"
2. 逐行获取数据，比如 id 为 1 的数据
3. 判断数据中的 title 是否符合用户搜索条件
4. 如果符合则放入结果集，不符合则丢弃。然后回到步骤1

逐行扫描，也就是全表扫描，随着数据量增加，其查询效率也会越来越低。当数据量达到数百万时，就是。。。

而倒排索引中有两个非常重要的概念：

- 文档（Document）：用来搜索的数据，其中的每一条数据就是一个文档。例如一个网页、一个商品信息
- 词条（Term）：对文档数据或用户搜索数据，利用某种算法分词，得到的具备含义的词语就是词条。例如：我是中国人，就可以分为：我、是、中国人、中国、国人这样的几个词条

**创建倒排索引**是对正向索引的一种特殊处理，流程如下：

- 将每一个文档的数据利用算法分词，得到一个个词条
- 创建表，每行数据包括词条、词条所在文档 id、位置等信息
- 因为词条唯一性，可以给词条创建索引，例如 hash 表结构索引

如图：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561827128-ca1ed985-a18d-4869-9242-f02277d2eb90.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918203514.png)

**倒排索引的搜索流程**如下（以搜索"华为手机"为例）

1. 用户输入条件"华为手机"进行搜索
2. 对用户输入内容**分词**，得到词条：华为、手机
3. 拿着词条在倒排索引中查找，可以得到包含词条的文档 id 有 1、2、3
4. 拿着文档 id 到正向索引中查找具体文档

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561828860-d8df9284-42b4-4bb3-b11d-8dc99f834320.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918203815.png)

**虽然要先查询倒排索引，再查询正向索引，但是词条和文档id 都建立了索引，查询速度非常快！无需全表扫描。**

为什么一个叫做正向索引，一个叫做倒排索引呢？

**正向索引**是最传统的，根据 id 索引的方式。但根据词条查询时，必须先逐条获取每个文档，然后判断文档中是否包含所需要的词条，是**根据文档找词条的过程**

**倒排索引**则相反，是先找到用户要搜索的词条，根据得到的文档 id 获取该文档。是**根据词条找文档的过程**

### 索引和映射

**索引（Index）**，就是相同类型的文档的集合。

例如：

- 所有用户文档，就可以组织在一起，称为用户的索引；
- 所有商品的文档，可以组织在一起，称为商品的索引；
- 所有订单的文档，可以组织在一起，称为订单的索引；

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561829926-bedebc42-a2b7-4186-95fb-79b54fae5369.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918213357.png)

因此，我们可以把索引当做是数据库中的表。

数据库的表会有约束信息，用来定义表的结构、字段的名称、类型等信息。因此，索引库中就有**映射（mapping）**，是索引中文档的字段约束信息，类似表的结构约束。

**mysql 与 elasticsearch**

| **MySQL** | **Elasticsearch** | **说明**                                                     |
| --------- | ----------------- | ------------------------------------------------------------ |
| Table     | Index             | 索引(index)，就是文档的集合，类似数据库的表(table)           |
| Row       | Document          | 文档（Document），就是一条条的数据，类似数据库中的行（Row），文档都是JSON格式 |
| Column    | Field             | 字段（Field），就是JSON文档中的字段，类似数据库中的列（Column） |
| Schema    | Mapping           | Mapping（映射）是索引中文档的约束，例如字段类型约束。类似数据库的表结构（Schema） |
| SQL       | DSL               | DSL是elasticsearch提供的JSON风格的请求语句，用来操作elasticsearch，实现CRUD |

- Mysql：擅长事务类型操作，可以确保数据的安全和一致性
- Elasticsearch：擅长海量数据的搜索、分析、计算

因此在企业中，往往是两者结合使用：

- 对安全性要求较高的写操作，使用 MySQL 实现
- 对查询性能要求较高的搜索需求，使用 ELasticsearch 实现
- 两者再基于某种方式，实现数据的同步，保证一致性

![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561830404-00d733e5-c773-4d58-8bb2-3b80b205838b.png)

### 文档和字段

elasticsearch 是面向**文档（Document）**存储的，可以是数据库中的一条商品数据，一个订单信息。文档数据会被序列化为 json 格式后存储在 elasticsearch

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561829068-97296066-454c-4d91-b7d1-207085229b9a.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918212707.png)

而 JSON 文档中往往包含很多的**字段（Field）**，类似于数据库中的列。

### ElasticStack 

ElasticStack 是一系列开源产品的合集，包括 **Elasticsearch**、**Kibana**、**Logstash** 以及 **Beats** 等，能够安全可靠地获取任何来源、任何格式的数据，并且能够实时地对数据进行搜索、分析和可视化。其中，Logstash和Beats负责数据的收集，Kibana负责结果数据的可视化展现，Elasticsearch作为核心部分用于数据的分布式存储以及索引。

### 分词器

https://www.elastic.co/guide/en/elasticsearch/reference/7.17/test-analyzer.html

分词的一种规则

内置分词器：https://www.elastic.co/guide/en/elasticsearch/reference/7.17/analysis-analyzers.html

空格分词器：whitespace，结果：The、quick、brown、fox.

```json
POST _analyze
{
  "analyzer": "whitespace",
  "text":     "The quick brown fox."
}
```



标准分词规则，结果：is、this、deja、vu

```json
POST _analyze
{
  "tokenizer": "standard",
  "filter":  [ "lowercase", "asciifolding" ],
  "text":      "Is this déja vu?"
}
```



关键词分词器，就是不分词，整句话当做专业术语

```json
POST _analyze
{
  "analyzer": "keyword",
  "text":     "The quick brown fox."
}
```



### IK 分词器（ES 插件）

中文友好：https://github.com/medcl/elasticsearch-analysis-ik

下载地址：https://github.com/medcl/elasticsearch-analysis-ik/releases/tag/v7.17.7（注意版本一致）



思考：怎么样让 ik 按自己的想法分词？

回答：自定义词典

#### 步骤

1. 在 elasticsearch-7.17.9 目录下新建 plugins 目录
2. 在 plugins 目录下新建 ik 目录
3. 将下载的 zip 包解压到 ik 目录下
4. 将 elasticsearch-analysis-ik-7.17.7 目录中的所有内容移到 ik 目录下

![image-20230327221242198](https://typora-1313423481.cos.ap-guangzhou.myqcloud.com/typora/image-20230327221242198.png)



5. 重启 ES，发生错误：插件版本不匹配

![image-20230327221456663](https://typora-1313423481.cos.ap-guangzhou.myqcloud.com/typora/image-20230327221456663.png)



解决方案：https://github.com/medcl/elasticsearch-analysis-ik/issues/996

下载相近的版本，解压后修改plugin-descriptor.properties文件里面的elasticsearch.version就可以。

![image-20230327221812061](https://typora-1313423481.cos.ap-guangzhou.myqcloud.com/typora/image-20230327221812061.png)



修改后，ES 启动成功，重新在 kibana 中运行分词器测试，成功。



命令行中显示加载 ik 插件

![image-20230327222213384](https://typora-1313423481.cos.ap-guangzhou.myqcloud.com/typora/image-20230327222213384.png)



6. 测试 ik_smart 分词器，结果：你好、我、是、鱼皮、我、不是、小、黑子、我、是、困、困、真爱、粉

```json
POST _analyze
{
  "analyzer": "ik_smart",
  "text":     "你好我是鱼皮，我不是小黑子，我是困困真爱粉"
}
```



7. 测试 ik_max_word 分词器，结果：你好、我、是、鱼皮、我、不是、小黑、黑子、我、是、困、困、真爱、粉

```json
POST _analyze
{
  "analyzer": "ik_max_word",
  "text":     "你好我是鱼皮，我不是小黑子，我是困困真爱粉"
}

```



ik_smart 和 ik_max_word 的区别？举例：“小黑子”

ik_smart 是智能分词，尽量选择最像一个词的拆分方式，比如“小”、“黑子”

ik_max_word 尽可能地分词，可以包括组合词，比如 “小黑”、“黑子”

## 安装（windows）

> windows直接下载就可以了，安装简单:jack_o_lantern:(官网下载)

## 索引库操作

### Mapping属性映射

索引库==>表    mapping映射==>表结构

所以向es中存储数据，必须创建库和表

mapping 是对索引库中文档的约束，常见的 mapping 属性包括：

- type：字段数据类型，常见的简单类型有：
  - 字符串：text（可分词的文本）、keyword（精确值，例如：品牌、国家、ip地址）
  - 数值：long、integer、short、byte、double、float、
  - 布尔：boolean
  - 日期：date
  - 对象：object
- **index：是否创建索引，默认为 true**
- analyzer：使用哪种分词器
- properties：该字段的子字段

例：

```json
{    
"age": 21,    
"weight": 52.1,     
"isMarried": false,    
"info": "钟老师真菜",    
"email": "jialna@qq.com",     
"score": [99.1, 99.5, 98.9],    
"name":
  {         
    "firstName": "湖",         
    "lastName": "心"   
  } 
}
```

首先对应的每个字段映射（mapping）情况如下：

- age：类型为 integer；参与搜索，index 为 true；无需分词器
- weight：类型为 float；参与搜索，index 为 true；无需分词器
- isMarried：类型为boolean；参与搜索，index 为 true；无需分词器
- info：类型为字符串，需要分词，因此是 text；参与搜索，index为true；分词器可以用 ik_smart
- email：类型为字符串，但是不需要分词，因此是 keyword；不参与搜索，index 为 false；无需分词器
- score：虽然是数组，**但是我们只看元素的类型**，类型为 float；参与搜索，index 为 true；无需分词器
- name：类型为 object，需要定义多个子属性

- - name.firstName：类型为字符串，不需要分词，keyword；参与搜索，index 为 true；无需分词器
  - name.lastName：类型为字符串，不需要分词，keyword；参与搜索，index 为 true；无需分词器

### 创建索引库和映射

```json
PUT /索引库名称
{   "mappings":
  {     
    "properties": 
      {       
        "字段名":
          {         
            "type": "text",         
            "analyzer": "ik_smart"       
            },
         "字段名2":
           {         
             "type": "keyword",         
             "index": "false"       
             },       
          "字段名3":
            {         
              "properties": 
              {           
                "子字段": 
                    {             
                  "type": "keyword"           
                    }        
              }      
            }      
          // ...略     }
   }
}


PUT /xn2001 
  {   "mappings":
    {     "properties": 
      {       "info":
        {         "type": "text",         
                   "analyzer": "ik_smart"      
                   },       
              "email":{         
                  "type": "keyword",         
                  "index": "false"       
                    },      
                  "name":
                    {        
                    "properties": 
                        {          
                        "firstName": 
                        {           
                           "type": "keyword"          
                           },       
                        "lastName":
                        {            
                      "type": "keyword"           
                        }         
                        }       
                     }  
        }	   
      }
}
```

![image-20230406105444246](D:\图\typora\Elasticsearch分布式引擎\image-20230406105444246.png)

数据库表：

![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561837506-7b2816ed-b305-4aa7-a6d8-c423f09bd0ba.png)

- 字段名、字段数据类型，可以参考数据表结构的名称和类型
- 是否参与搜索要分析业务来判断，例如图片地址，就无需参与搜索
- 是否分词呢要看内容，内容如果是一个整体就无需分词
- 分词器，我们可以统一使用 ik_max_word

```json
PUT /hotel
{
  "mappings": {
    "properties": {
      "id": {
        "type": "keyword"
      },
      "name": {
        "type": "text",
        "analyzer": "ik_max_word",
        "copy_to": "all"
      },
      "address": {
        "type": "keyword",
        "index": false
      },
      "price": {
        "type": "integer"
      },
      "score": {
        "type": "integer"
      },
      "brand": {
        "type": "keyword",
        "copy_to": "all"
      },
      "city": {
        "type": "keyword",
        "copy_to": "all"
      },
      "starName": {
        "type": "keyword"
      },
      "business": {
        "type": "keyword"
      },
      "location": {
        "type": "geo_point"
      },
      "pic": {
        "type": "keyword",
        "index": false
      },
      "all": {
        "type": "text",
        "analyzer": "ik_max_word"
      }
    }
  }
}
```

特殊字段说明：

- location：地理坐标，里面包含精度、纬度
- all：一个组合字段，其目的是将多字段的值利用 copy_to 合并，提供给用户搜索，这样一来就只需要搜索一个字段就可以得到结果，性能更好。

ES中支持两种地理坐标数据类型：

- geo_point：由纬度（latitude）和经度（longitude）确定的一个点。例如："32.8752345, 120.2981576"
- geo_shape：有多个 geo_point 组成的复杂几何图形。例如一条直线，"LINESTRING (-77.03653 38.897676, -77.009051 38.889939)"

### 修改索引库

倒排索引结构虽然不复杂，但是一旦数据结构改变（比如改变了分词器），就需要重新创建倒排索引，这简直是灾难。因此索引库**一旦创建，无法修改 mapping**

虽然无法修改 mapping 中已有的字段，但是却**允许添加新的字段**到 mapping 中，不会对倒排索引产生影响。

```json
PUT /索引库名/_mapping {
    "properties": {
        "新字段名": {
            "type": "integer"
        }
    }
}
```

### 删除索引库

DELETE /索引库名

### 查询索引库

GET /数据库名

## DSL文档操作

### 新增文档

```json
POST /索引库名/_doc/文档id 
{
    "字段1": "值1",
    "字段2": "值2",
    "字段3": {
        "子属性1": "值3",
        "子属性2": "值4"
    }     * // ...* }
```

```json
POST /xn2001/_doc/1 
{
    "info": "我不会Java",
    "email": "jialna@qq.com",
    "name": {
        "firstName": "钟",
        "lastName": "弟弟"
    }
```

### 修改文档

修改文档有两种方式：

- 全量修改：直接覆盖原来的文档
- 增量修改：修改文档中的部分字段

**全量修改**是覆盖原来的文档，其本质是：

- 根据指定的 id 删除文档
- 新增一个相同 id 的文档

```json
PUT /{索引库名}/_doc/id 
{
    "字段1": "值1",
    "字段2": "值2",     * // ... 略* }

PUT /xn2001/_doc/1 
    {
        "info": "我也不会敲代码",
        "email": "3300123589@qq.com",
        "name": {
            "firstName": "弟弟",
            "lastName": "钟"
        }
    }
```

**增量修改**是只修改指定 id 匹配的文档中的部分字段

```json
POST /{索引库名}/_update/文档id 
{
    "doc": {
        "字段名": "新的值",
    }
}

POST /heima/_update/1 
{
    "doc": {
        "email": "update@qq.com"
    }
}
```

### 查询文档

**GET** /{索引库名称}/_doc/{id}

### 删除文档

**DELETE** /{索引库名}/_doc/{id}

## DSL文档查询

Elasticsearch 提供了基于 JSON 的 DSL([Domain Specific Language](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html))来定义查询。常见的查询类型包括：

**查询所有**：查询出所有数据，一般测试用。例如：match_all

**全文检索（full text）查询**：利用分词器对用户输入内容分词，然后去倒排索引库中匹配。例如：

- match_query
- multi_match_query

**精确查询**：根据精确词条值查找数据，一般是查找 keyword、数值、日期、boolean 等类型字段。例如：

- ids
- range
- term

**地理（geo）查询**：根据经纬度查询。例如：

- geo_distance
- geo_bounding_box

**复合（compound）查询**：复合查询可以将上述各种查询条件组合起来，合并查询条件。例如：

- bool
- function_score

---

*// 查询所有* GET /indexName/_search {   "query": {     "match_all": {     }   } }

```json
GET /xn2001/_search
{
  "query": {
    "match_all": {}
  }
}
```

### 全文检索

使用场景：全文检索查询的基本流程如下：

1. 对用户搜索的内容做分词，得到词条

2. 根据词条去倒排索引库中匹配，得到文档id

3. 根据文档id找到文档，返回给用户

比较常用的场景包括：

- 商城的输入框搜索
- 百度输入框搜索

> `可分词类型`

常见的全文检索查询包括：

- match 查询：单字段查询
- multi_match 查询：多字段查询，**任意一个字段**符合条件就算符合查询条件

match语法：

```json
GET /indexName/_search 
{
    "query": {
        "match": {
            "FIELD": "TEXT"
        }
    }
}
```

mulit_match 查询语法如下：

```json
GET /indexName/_search 
{
    "query": {
        "multi_match": {
            "query": "TEXT",
            "fields": [
                "FIELD1",
                " FIELD12"
            ]
        }
    }
}
```

因为我们将 brand、name、business 值都利用 **copy_to** 复制到了 **all** 字段中，所以根据三个字段搜索和根据 all字段搜索效果是一样的。

```json
GET /hotel/_search 
{
    "query": {
        "match": {
            "all": "7天酒店"
        }
    }
}

GET /hotel/_search 
{
    "query": {
        "multi_match": {
            "query": "7天酒店",
            "fields": [
                "brand",
                "name"
            ]
        }
    }
}
```

### 精准查询

精确查询一般是查找 keyword、数值、日期、boolean 等类型字段。所以**不会**对搜索条件分词。

- term：根据词条精确值查询
- range：根据值的范围查询

#### term查询

因为精确查询的字段搜是不分词的字段，因此查询的条件也必须是**不分词**的词条。查询时，用户输入的内容跟自动值完全匹配时才认为符合条件。如果用户输入的内容过多，反而搜索不到数据。

```json
GET /indexName/_search
{
    "query": {
        "term": {
            "FIELD": {
                "value": "VALUE"
            }
        }
    }
}

GET /hotel/_search
{
    "query": {
        "term": {
            "brand": {
                "value": "7天酒店"
            }
        }
    }
}
```



#### range查询

范围查询，一般应用在对数值类型做范围过滤的时候。比如做价格范围过滤。

```json
GET /indexName/_search 
{
    "query": {
        "range": {
            "FIELD": {
                "gte": 10, *// 这里的gte代表大于等于，gt则代表大于*
                "lte": 20*// lte代表小于等于，lt则代表小于* 
            }
        }
    }
}
```

精确查询常见的有哪些？

- term 查询：根据词条精确匹配，一般搜索 keyword 类型、数值类型、布尔类型、日期类型字段
- range 查询：根据数值范围查询，可以是数值、日期的范围

### 地理坐标查询

地理坐标查询，其实就是根据经纬度查询，官方文档：https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-queries.html

常见的使用场景包括：

- 携程：搜索我附近的酒店
- 滴滴：搜索我附近的出租车
- 微信：搜索我附近的人

#### 矩形范围查询

矩形范围查询，也就是 `geo_bounding_box `查询，查询坐标落在某个矩形范围的所有文档

查询时，需要指定矩形的**左上**、**右下**两个点的坐标，然后画出一个矩形，落在该矩形内的都是符合条件的点。

```json
{
    "query": {
        "geo_bounding_box": {
            "FIELD": {
                "top_left": { // 左上点
                    "lat": 31.1,
                    "lon": 121.5
                },
                "bottom_right": {// 右下点     
                    "lat": 30.9,
                    "lon": 121.7
                }
            }
        }
    }
}
```

#### 附近查询

附近查询，也叫做距离查询（geo_distance）：查询到指定中心点小于某个距离值的所有文档

在地图上找一个点作为圆心，以指定距离为半径，画一个圆，落在圆内的坐标都算符合条件：

```json
GET /indexName/_search {
    "query": {
        "geo_distance": {
            "distance": "15km", // 半径
            "FIELD": "31.21,121.5"// 圆心
        }
    }
}
```

中心排序：

```json
GET /hotel/_search {
    "query": {
        "match_all": {}
    },
    "sort": [
        {
            "_geo_distance": {
                "location": "31.034661,121.612282",
                "order": "asc",//排序
                "unit": "km"//单位
            }
        }
    ]
}
```

排序完成后，页面还要获取我附近每个酒店的具体**距离**值，这个值在响应结果中是独立的：

![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561856333-567937e6-257a-4076-9978-e4f1a7bd1635.png)

### 复合查询

复合（compound）查询：复合查询可以将其它简单查询组合起来，实现更复杂的搜索逻辑。

- fuction score：算分函数查询，可以控制文档相关性算分，控制文档排名
- bool query：布尔查询，利用逻辑关系组合多个其它的查询，实现复杂搜索

#### 相关性分析

在elasticsearch中，早期使用的打分算法是TF-IDF算法，公式如下：

![img](https://img-blog.csdnimg.cn/240af83fd9474fc89676c5e8a5b2441d.png)

在后来的5.1版本升级中，elasticsearch将算法改进为BM25算法，公式如下：

![img](https://img-blog.csdnimg.cn/240af83fd9474fc89676c5e8a5b2441d.png)

TF-IDF算法有一各缺陷，就是词条频率越高，文档得分也会越高，单个词条对文档影响较大。而BM25则会让单个词条的算分有一个上限，曲线更加平滑：

小结：elasticsearch会根据词条和文档的相关度做打分，算法由两种：

![](https://img-blog.csdnimg.cn/bc85076167e946caaa8c9fd5612e18a8.png)

- TF-IDF算法
- BM25算法，elasticsearch5.1版本后采用的算法

#### 算分函数查询

根据相关度打分是比较合理的需求，但**合理的不一定是产品经理需要**的。

以百度为例，你搜索的结果中，并不是相关度越高排名越靠前，而是谁掏的钱多排名就越靠前。

要想认为控制相关性算分，就需要利用elasticsearch中的function score 查询了。

![](https://img-blog.csdnimg.cn/fa3cec30079a4c358bbb6d9b5e0e88cf.png)

- 原始查询条件：query部分，基于这个条件搜索文档，并且基于BM25算法给文档打分，原始算分（query score)
- 过滤条件：filter部分，符合该条件的文档才会重新算分
- 算分函数：符合filter条件的文档要根据这个函数做运算，得到的函数算分（function score），有四种函数
  - weight：函数结果是常量
  - field_value_factor：以文档中的某个字段值作为函数结果
  - random_score：以随机数作为函数结果
  - script_score：自定义算分函数算法
- 运算模式：算分函数的结果、原始查询的相关性算分，两者之间的运算方式，包括：
  - multiply：相乘
  - replace：用function score替换query score
  - 其它，例如：sum、avg、max、min

function score的运行流程如下：

1. 根据`原始条件`查询搜索文档，并且计算相关性算分，称为`原始算分`（query score）
2. 根据`过滤条件`，过滤文档
3. 符合过滤条件的文档，基于算分函数运算，得到`函数算分`（function score）
4. 将原始算分（query score）和函数算分（function score）基于`运算模式`做运算，得到最终结果，作为相关性算分。

> 需求：给“如家”这个品牌的酒店排名靠前一些
>
> - 原始条件：不确定，可以任意变化
> - 过滤条件：brand = “如家”
> - 算分函数：可以简单粗暴，直接给固定的算分结果，weight
> - 运算模式：比如求和

例如：

```json
GET /hotel/_search
{
  "query": {
    "function_score": {
      "query": {  .... }, // 原始查询，可以是任意条件
      "functions": [ // 算分函数
        {
          "filter": { // 满足的条件，品牌必须是如家
            "term": {
              "brand": "如家"
            }
          },
          "weight": 2 // 算分权重为2
        }
      ],
      "boost_mode": "sum" // 加权模式，求和
    }
  }
}
```

原始：

![](https://img-blog.csdnimg.cn/fd584baf37954ea3b09c1d0e1b8d6fcc.png)

算分：

![](https://img-blog.csdnimg.cn/b6ba26dcddf343debc8da25e85d95794.png)

#### 布尔查询

布尔查询是一个或多个查询子句的组合，每一个子句就是一个**子查询**。子查询的组合方式有：

- must：必须匹配每个子查询，类似“与”
- should：选择性匹配子查询，类似“或”
- must_not：必须不匹配，**不参与算分**，类似“非”
- filter：必须匹配，**不参与算分**

每一个不同的字段，其查询的条件、方式都不一样，必须是多个不同的查询，而要组合这些查询，就必须用bool查询了。
需要注意的是，搜索时，参与**打分的字段越多，查询的性能也越差**。因此这种多条件查询时，建议这样做：

- 搜索框的关键字搜索，是全文检索查询，使用must查询，参与算分
- 其它过滤条件，采用filter查询。不参与算分

```json
GET /hotel/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {"city": "上海" }}
      ],
      "should": [
        {"term": {"brand": "皇冠假日" }},
        {"term": {"brand": "华美达" }}
      ],
      "must_not": [
        { "range": { "price": { "lte": 500 } }}
      ],
      "filter": [
        { "range": {"score": { "gte": 45 } }}
      ]
    }
  }
}

```

需求：搜索名字包含“如家”，价格不高于400，在坐标31.21,121.5周围10km范围内的酒店。

- 名称搜索，属于全文检索查询，应该参与算分。放到must中
- 价格不高于400，用range查询，属于过滤条件，不参与算分。放到must_not中
- 周围10km范围内，用geo_distance查询，属于过滤条件，不参与算分。放到filter中

![](https://img-blog.csdnimg.cn/92faa7deb0b54d6d9631a088faa7f926.png)

## DSL数据聚合

[聚合（](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)[aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)[）](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)可以让我们极其方便的实现对数据的统计、分析、运算。

- 什么品牌的手机最受欢迎？
- 这些手机的平均价格、最高价格、最低价格？
- 这些手机每月的销售情况如何？

在 Elasticsearch 实现这些统计功能比数据库的 sql 要方便的多，而且查询速度非常快，可以实现近实时搜索效果。

聚合常见的有三类

- **桶（Bucket）**聚合：用来对文档做分组
  - TermAggregation：按照文档字段值分组，例如按照品牌值分组、按照国家分组
  - Date Histogram：按照日期阶梯分组，例如一周为一组，或者一月为一组
- **度量（Metric）**聚合：用以计算一些值，比如：最大值、最小值、平均值等
  - Avg：求平均值
  - Max：求最大值
  - Min：求最小值
  - Stats：同时求 max、min、avg、sum 等
- **管道（pipeline）**聚合：其它聚合的结果为基础做聚合

**注意：**参加聚合的字段必须是keyword、日期、数值、布尔类型

### Bucket聚合语法

**例如**：我们要统计所有数据中的酒店品牌有几种，其实就是按照品牌对数据分组。此时可以根据酒店品牌的名称做聚合，也就是 Bucket 聚合。

```json
GET /hotel/_search
{
  "size": 0,  // 设置size为0，结果中不包含文档，只包含聚合结果
  "aggs": { // 定义聚合
    "brandAgg": { //给聚合起个名字(自定义）
      "terms": { // 聚合的类型，按照品牌值聚合，所以选择term
        "field": "brand", // 参与聚合的字段
        "size": 20 // 希望获取的聚合结果数量
      }
    }
  }
}

```

![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561943455-cfaf667f-1665-4774-bf6c-b9041603bae9.png)

#### 聚合结果排序

默认情况下，Bucket聚合会统计Bucket内的文档数量，记为_count，并且按照_count降序排序。

我们可以指定order属性，自定义聚合的排序方式：

```json
GET /hotel/_search
{
  "size": 0, 
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "order": {
          "_count": "asc" // 按照_count升序排列
        },
        "size": 20
      }
    }
  }
}

```

#### 限定聚合范围

默认情况下，Bucket聚合是对索引库的所有文档做聚合，但真实场景下，用户会输入搜索条件，因此聚合必须是对搜索结果聚合。那么聚合必须添加限定条件。

我们可以`限定要聚合的文档范围`，只要添加query条件即可：

```json
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "lte": 200 // 只对200元以下的文档聚合
      }
    }
  }, 
  "size": 0, 
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 20
      }
    }
  }
}

```

### Metric聚合语法

我们对酒店按照品牌分组，形成了一个个桶。现在我们需要对桶内的酒店做运算，获取每个品牌的用户评分的min、max、avg等值。

这就要用到Metric聚合了，例如`stat聚合：就可以获取min、max、avg等结果`。

```json
GET /hotel/_search
{
  "size": 0, 
  "aggs": {
    "brandAgg": { 
      "terms": { 
        "field": "brand", 
        "size": 20
      },
      "aggs": { // 是brands聚合的子聚合，也就是分组后对每组分别计算
        "score_stats": { // 聚合名称
          "stats": { // 聚合类型，这里stats可以计算min、max、avg等
            "field": "score" // 聚合字段，这里是score
          }
        }
      }
    }
  }
}

```

这次的score_stats聚合是在brandAgg的聚合内部嵌套的子聚合。因为我们需要在每个桶分别计算。

另外，我们还可以给聚合结果做个排序，例如按照每个桶的酒店平均分做排序：

![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561964090-fa8b802c-3b7e-4260-a197-660b1d3a3940.png)

## 结果处理

### 排序

elasticsearch默认是根据相关度算分（_score）来排序，但是也支持自定义方式对搜索[结果排序](https://www.elastic.co/guide/en/elasticsearch/reference/current/sort-search-results.html)。可以排序字段类型有：keyword类型、数值类型、地理坐标类型、日期类型等。

####  普通字段排序

keyword、数值、日期类型排序的语法基本一致。

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "FIELD": "desc"  // 排序字段、排序方式ASC、DESC
    }
  ]
}
```

排序条件是一个数组，也就是可以写多个排序条件。按照声明的顺序，当第一个条件相等时，再按照第二个条件排序，以此类推

---

需求描述：酒店数据按照用户评价（score)降序排序，评价相同的按照价格(price)升序排序

![](https://img-blog.csdnimg.cn/f98670f4e0694243b270863d8c06398a.png)

#### 地理坐标排序

```json
GET /indexName/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "_geo_distance" : {
          "FIELD" : "纬度，经度", // 文档中geo_point类型的字段名、目标坐标点
          "order" : "asc", // 排序方式
          "unit" : "km" // 排序的距离单位
      }
    }
  ]
}

```

- 指定一个坐标，作为目标点
- 计算每一个文档中，指定字段（必须是geo_point类型）的坐标 到目标点的距离是多少
- 根据距离排序

需求描述：实现对酒店数据按照到你的位置坐标的距离升序排序

假设我的位置是：31.034661，121.612282，寻找我周围距离最近的酒店。

![](https://img-blog.csdnimg.cn/de4dfeb90fa5409c962fa050f346cda2.png)

### 分页

elasticsearch 默认情况下只返回`top10`的数据。而如果要查询更多数据就需要修改分页参数了。elasticsearch中通过修改from、size参数来控制要返回的分页结果：

- from：从第几个文档开始
- size：总共查询几个文档

类似于mysql中的`limit ?, ?`

#### 基本分页

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "from": 0, // 分页开始的位置，默认为0
  "size": 10, // 期望获取的文档总数
  "sort": [
    {"price": "asc"}
  ]
}

```

####  深度分页

查询990~1000的数据

```json
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "from": 990, // 分页开始的位置，默认为0
  "size": 10, // 期望获取的文档总数
  "sort": [
    {"price": "asc"}
  ]
}
```

当查询分页深度较大时，汇总数据过多，对内存和CPU会产生非常大的压力，因此elasticsearch会禁止from+ size 超过10000的请求。

针对深度分页，ES提供了两种解决方案，[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/paginate-search-results.html)：

> 使用方式可见：[(23条消息) Elasticsearch 三种分页方式_elasticsearch 分页_zhexiao27的博客-CSDN博客](https://blog.csdn.net/andybegin/article/details/83864171)

- search after：分页时需要排序，原理是从上一次的排序值开始，查询下一页数据。官方推荐使用的方式。
- scroll：原理将排序后的文档id形成快照，保存在内存。官方已经不推荐使用。

---

- `from + size`：
  - 优点：支持随机翻页
  - 缺点：深度分页问题，默认查询上限（from + size）是10000
  - 场景：百度、京东、谷歌、淘宝这样的随机翻页搜索
- `after search`：
  - 优点：没有查询上限（单次查询的size不超过10000）
  - 缺点：只能向后逐页查询，不支持随机翻页
  - 场景：没有随机翻页需求的搜索，例如手机向下滚动翻页
- `scroll`：
  - 优点：没有查询上限（单次查询的size不超过10000）
  - 缺点：会有额外内存消耗，并且搜索结果是非实时的
  - 场景：海量数据的获取和迁移。从ES7.1开始不推荐，建议用 after search方案。

### 高亮

我们在百度，京东搜索时，关键字会变成红色，比较醒目，这叫高亮显示：

高亮显示的实现分为两步：

- 给文档中的所有关键字都添加一个标签，例如`<em>`标签
- 页面给`<em>`标签编写CSS样式

```json
GET /hotel/_search
{
  "query": {
    "match": {
      "FIELD": "TEXT" // 查询条件，高亮一定要使用全文检索查询
    }
  },
  "highlight": {
    "fields": { // 指定要高亮的字段
      "FIELD": {
        "pre_tags": "<em>",  // 用来标记高亮字段的前置标签
        "post_tags": "</em>" // 用来标记高亮字段的后置标签
      }
    }
  }
}

```

- 高亮是对关键字高亮，因此**搜索条件必须带有关键字**，而不能是范围这样的查询。
- 默认情况下，**高亮的字段，必须与搜索指定的字段一致**，否则无法高亮
- 如果要对非搜索字段高亮，则需要添加一个属性：required_field_match=false

## JAVA操作ES

### MySQL数据库

```sql
-- 帖子表
create table if not exists post
(
    id         bigint auto_increment comment 'id' primary key,
    title      varchar(512)                       null comment '标题',
    content    text                               null comment '内容',
    tags       varchar(1024)                      null comment '标签列表（json 数组）',
    thumbNum   int      default 0                 not null comment '点赞数',
    favourNum  int      default 0                 not null comment '收藏数',
    userId     bigint                             not null comment '创建用户 id',
    createTime datetime default CURRENT_TIMESTAMP not null comment '创建时间',
    updateTime datetime default CURRENT_TIMESTAMP not null on update CURRENT_TIMESTAMP comment '更新时间',
    isDelete   tinyint  default 0                 not null comment '是否删除',
    index idx_userId (userId)
) comment '帖子' collate = utf8mb4_unicode_ci;
```

### ES索引

ES-mapping:

- **aliases**：别名，方便数据迁移
- **text**：字段类型是 text，这个字段是可被分词的、可模糊查询的；而如果是 keyword，只能完全匹配、精确查询。
- **analyzer（存储时生效的分词器）**：用 ik_max_word，拆的更碎、索引更多，更有可能被搜出来
- **search_analyzer（查询时生效的分词器）**：用 ik_smart，更偏向于用户想搜的分词
- **精确查找**：如果想要让 text 类型的分词字段也支持精确查询，可以创建 keyword 类型的子字段

```json
"fields": {
  	"keyword": {
    	"type": "keyword",
    	"ignore_above": 256 // 超过字符数则忽略查询
  }
```

**mapping结构**：

```json
PUT post_v1
{
  "aliases": {
    "post": {}
  },
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "analyzer": "ik_max_word",
        "search_analyzer": "ik_smart",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "content": {
        "type": "text",
        "analyzer": "ik_max_word",
        "search_analyzer": "ik_smart",
        "fields": {
          "keyword": {
            "type": "keyword",
            "ignore_above": 256
          }
        }
      },
      "tags": {
        "type": "keyword"
      },
      "userId": {
        "type": "keyword"
      },
      "createTime": {
        "type": "date"
      },
      "updateTime": {
        "type": "date"
      },
      "isDelete": {
        "type": "keyword"
      }
    }
  }
} 
```

### 步骤

1. 引包

```xml
<!-- elasticsearch-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
</dependency>
```

2. 配置

```yml
spring:
  elasticsearch:
    uris: http://localhost:9200
    username: root
    password: 123456
```

3. 实体类

> 日期有大坑！！！

```java
@Document(indexName = "post")
@Data
public class PostEsDTO implements Serializable {

    private static final String DATE_TIME_PATTERN = "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'";

    /**
     * id
     */
    @Id
    private Long id;

    /**
     * 标题
     */
    private String title;

    /**
     * 内容
     */
    private String content;

    /**
     * 标签列表
     */
    private List<String> tags;

    /**
     * 创建用户 id
     */
    private Long userId;

    /**
     * 创建时间
     */
    @Field(index = false, store = true, type = FieldType.Date, format = {}, pattern = DATE_TIME_PATTERN)
    private Date createTime;

    /**
     * 更新时间
     */
    @Field(index = false, store = true, type = FieldType.Date, format = {}, pattern = DATE_TIME_PATTERN)
    private Date updateTime;

    /**
     * 是否删除
     */
    private Integer isDelete;

    private static final long serialVersionUID = 1L;

    private static final Gson GSON = new Gson();

    /**
     * 对象转包装类
     *
     * @param post
     * @return
     */
    public static PostEsDTO objToDto(Post post) {
        if (post == null) {
            return null;
        }
        PostEsDTO postEsDTO = new PostEsDTO();
        BeanUtils.copyProperties(post, postEsDTO);
        String tagsStr = post.getTags();
        if (StringUtils.isNotBlank(tagsStr)) {
            postEsDTO.setTags(GSON.fromJson(tagsStr, new TypeToken<List<String>>() {
            }.getType()));
        }
        return postEsDTO;
    }

    /**
     * 包装类转对象
     *
     * @param postEsDTO
     * @return
     */
    public static Post dtoToObj(PostEsDTO postEsDTO) {
        if (postEsDTO == null) {
            return null;
        }
        Post post = new Post();
        BeanUtils.copyProperties(postEsDTO, post);
        List<String> tagList = postEsDTO.getTags();
        if (CollectionUtils.isNotEmpty(tagList)) {
            post.setTags(GSON.toJson(tagList));
        }
        return post;
    }
}
```

### 简单操作

ElasticsearchRepository<PostEsDTO, Long>，默认提供了简单的增删改查，多用于可预期的、相对没那么复杂的查询、自定义查询，返回结果相对简单直接。

```java
public interface PostEsDao extends ElasticsearchRepository<PostEsDTO, Long> {

    List<PostEsDTO> findByUserId(Long userId);
}
```

### 复杂查询

Spring 默认给我们提供的操作 es 的客户端对象：ElasticsearchRestTemplate，也提供了简单的增删改查，它的增删改查更灵活，适用于更复杂的操作，返回结果更完整，但需要自己解析。

使用elasticsearchTemplate

```
TermsBuilder: 构造聚合函数

AggregationBuilders: 创建聚合函数工具类

BoolQueryBuilder: 拼装连接(查询)条件 

QueryBuilders: 简单的静态工厂”导入静态”使用。主要作用是查询条件(关系),如区间\精确\多值等条件 

NativeSearchQueryBuilder: 将连接条件和聚合函数等组合

SearchQuery:生成查询

elasticsearchTemplate.query:进行查询 

Aggregations:Represents a set of computed addAggregation.代表一组添加聚合函数统计后的数据
```

> [(34条消息) ElasticsearchTemplate的详细使用，完成多条件查询、匹配度查询等_elasticsearchtemplate使用_天涯泪小武的博客-CSDN博客](https://blog.csdn.net/tianyaleixiaowu/article/details/77965257)
> 找了篇有数据的

#### 文档集合+分页

```java
{
  "query": {
    "match_all": {}
  },
  "from":1,     // 从第几条开始    (从0开始)
  "size":10      // 大小
}

@Test
void matchAll() {
    MatchAllQueryBuilder matchAllQuery = QueryBuilders.matchAllQuery();
    //默认应该是第0页
    PageRequest pageRequest = PageRequest.of(1, 10);
    NativeSearchQuery searchQuery = new NativeSearchQueryBuilder()
            .withQuery(matchAllQuery)
            .withPageable(pageRequest).build();
    SearchHits<Post> searchHits = elasticsearchRestTemplate.search(searchQuery, Post.class);
    searchHits.getSearchHits().forEach(item -> {
        log.info("{}", item);
    });

}
```

![image-20230428153152691](D:\图\typora\Elasticsearch分布式引擎\image-20230428153152691.png)

#### 单字匹配查询

```java
"mathc":{
	"FIELD": "TEXT"     //字段:值
}

@Test
void word() {
    MatchQueryBuilder matchQueryBuilder = QueryBuilders.matchQuery("title", "浣溪沙");
    NativeSearchQuery searchQuery = new NativeSearchQueryBuilder()
            .withQuery(matchQueryBuilder)
            .build();
    SearchHits<Post> searchHits = elasticsearchRestTemplate.search(searchQuery, Post.class);
    searchHits.getSearchHits().forEach(item -> {
        log.info("{}", item);
    });
}
```

![image-20230428153721251](D:\图\typora\Elasticsearch分布式引擎\image-20230428153721251.png)

#### 排序

```java
{
    "sort": [
    {
      "FIELD": {
        "order": "desc"
      }
    }
  ]
}
@Test
void weight() {
    MatchQueryBuilder matchQueryBuilder = QueryBuilders.matchQuery("title", "浣溪沙");
    PageRequest pageRequest = PageRequest.of(0, 20);
    //weight排序
    FieldSortBuilder order = SortBuilders.fieldSort("weight").order(SortOrder.DESC);
    NativeSearchQuery searchQuery = new NativeSearchQueryBuilder()
            .withQuery(matchQueryBuilder)
            .withPageable(pageRequest)
            .withSorts(order)
            .build();
    SearchHits<Post> searchHits = elasticsearchRestTemplate.search(searchQuery, Post.class);
    searchHits.getSearchHits().forEach(item -> {
        log.info("{}", item);
    });
}
```

![image-20230428155257685](D:\图\typora\Elasticsearch分布式引擎\image-20230428155257685.png)

#### 高亮

```java
highlight 与 query同级
{
    "query" : {
        "match" : {
            "producer" : "producer"
        }
    },
    "highlight": {     //高亮显示
        "fields" : {    //字段
            "producer" : {}
        }
    }
}
@Test
void heigh() {
    MatchQueryBuilder matchQueryBuilder = QueryBuilders.matchQuery("title", "浣溪沙");
    PageRequest pageRequest = PageRequest.of(0, 20);
    FieldSortBuilder order = SortBuilders.fieldSort("weight").order(SortOrder.DESC);
    NativeSearchQuery searchQuery = new NativeSearchQueryBuilder()
            .withQuery(matchQueryBuilder)
            .withPageable(pageRequest)
            .withSorts(order)
            .withHighlightFields(new HighlightBuilder.Field("title"))
            .build();
    SearchHits<Post> searchHits = elasticsearchRestTemplate.search(searchQuery, Post.class);
    searchHits.getSearchHits().forEach(item -> {
        log.info("{}", item);
    });
}
```

## 聚合