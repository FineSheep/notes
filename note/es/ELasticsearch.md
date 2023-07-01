# ELasticsearch

案例地址：https://gitee.com/xn2001/cloudcode/tree/master/09-elasticsearch-hotel-demo

elasticsearch 是一款非常强大的开源搜索引擎，具备非常多强大功能，可以帮助我们从海量数据中快速找到需要的内容，可以用来实现搜索、日志统计、分析、系统监控等功能。

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561825477-fe30750e-e8d8-4e84-a763-bdb2b2a621fa.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918202359.png)

## 倒排索引

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

## 文档和字段

elasticsearch 是面向**文档（Document）**存储的，可以是数据库中的一条商品数据，一个订单信息。文档数据会被序列化为 json 格式后存储在 elasticsearch

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561829068-97296066-454c-4d91-b7d1-207085229b9a.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918212707.png)

而 JSON 文档中往往包含很多的**字段（Field）**，类似于数据库中的列。

## 索引和映射

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

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561830404-00d733e5-c773-4d58-8bb2-3b80b205838b.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918213631.png)

## 安装Elasticsearch

因为我们还需要部署 kibana 容器，需要让 es 和 kibana 容器互联。这里先创建一个网络：

docker network create es-net 



上传



加载

```shell
docker load -i es.tar
```

安装

```plain
docker run -d \
	--name es \
    -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
    -e "discovery.type=single-node" \
    -v es-data:/usr/share/elasticsearch/data \
    -v es-plugins:/usr/share/elasticsearch/plugins \
    --privileged \
    --network es-net \
    -p 9200:9200 \
    -p 9300:9300 \
elasticsearch:7.12.1
```

命令解释：

- -e "cluster.name=es-docker-cluster"：设置集群名称
- -e "http.host=0.0.0.0"：监听的地址，可以外网访问
- -e "ES_JAVA_OPTS=-Xms512m -Xmx512m"：内存大小
- -e "discovery.type=single-node"：非集群模式
- -v es-data:/usr/share/elasticsearch/data：挂载逻辑卷，绑定es的数据目录
- -v es-logs:/usr/share/elasticsearch/logs：挂载逻辑卷，绑定es的日志目录
- -v es-plugins:/usr/share/elasticsearch/plugins：挂载逻辑卷，绑定es的插件目录
- --privileged：授予逻辑卷访问权
- --network es-net ：加入一个名为 es-net 的网络中
- -p 9200:9200：端口映射配置

访问地址：[http://192.168.211.128:9200](http://192.168.211.128:9200/) 即可看到 elasticsearch 的响应结果

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561830898-229eb745-7aaa-4ce3-af98-6d330421feed.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918214620.png)

## 安装kibana

kibana 可以给我们提供一个 elasticsearch 的可视化界面，便于我们学习命令。

docker run -d  --name kibana -e ELASTICSEARCH_HOSTS=http://es:9200  --network=es-net  -p 5601:5601   kibana:7.12.1

- --network es-net ：加入一个名为 es-net 的网络中，与 elasticsearch 在同一个网络中
- -e ELASTICSEARCH_HOSTS=http://es:9200"：设置 elasticsearch 的地址，因为 kibana 已经与 elasticsearch 在一个网络，因此可以用容器名直接访问 elasticsearch
- -p 5601:5601：端口映射配置

访问地址：[http://192.168.211.128:5601](http://192.168.211.128:5601/)，即可看到结果

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561833362-df4f4b56-7bc0-456b-a051-05c741e9bfa7.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918214722.png)

控制面板：http://192.168.211.128:5601/app/dev_tools#/console

## 安装IK分词器

由于国内访问 GitHub 较慢，我们选择离线模式安装。

安装插件需要知道 elasticsearch 的 plugins 目录位置，而我们用了数据卷挂载，因此需要查看 elasticsearch 的数据卷目录，通过下面命令查看

docker volume inspect es-plugins

显示结果：

[     {         "CreatedAt": "2022-05-06T10:06:34+08:00",         "Driver": "local",         "Labels": null,         "Mountpoint": "/var/lib/docker/volumes/es-plugins/_data",         "Name": "es-plugins",         "Options": null,         "Scope": "local"     } ]

说明 plugins 目录被挂载到了 /var/lib/docker/volumes/es-plugins/_data 这个目录中

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561832856-e1073ca6-08d2-46d3-bc8b-7e3b1424753a.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918215615.png)

重启容器

**#** 4、重启容器 docker restart es  **#** 查看es日志 docker logs -f es

IK分词器包含两种模式：

- ik_smart：智能切分，粗粒度
- ik_max_word：最细切分，细粒度

我们在上面的 Kibana 控制台测试

GET /_analyze {   "analyzer": "ik_max_word",   "text": "钟老师你好菜啊" }

## 扩展词词典

在上面的IK分词器我们可以随着热点词来扩展，可以自己添加，比如 ”钟老师应该是一个热点词“，另外你也可以配置一些停用掉的敏感词，让其不进行分词。

打开IK分词器 config 目录是 IKAnalyzer.cfg.xml，添加一个文件名，我们以 ext.dic 文件名为例。

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561833575-ea15b265-c952-4e97-9e89-ddb23548a8e5.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918221159.png)

我们去创建 ext.dic ，在其中添加热点词就好了，一个词一行。

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561834976-0cb1d8c9-55e6-47cd-bc1f-43f0b11d6373.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918220910.png)

重启 elasticsearch

docker restart es

重新测试

GET /_analyze {   "analyzer": "ik_max_word",   "text": "钟老师你好菜啊" }

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561835816-3a507737-0c46-4e66-a09c-8c2a803bb990.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918221424.png)

## 索引库操作

### Mapping属性映射

索引库就类似数据库表，**mapping 映射就类似表的结构**

我们要向 es 中存储数据，必须先创建“库”和“表”

mapping 是对索引库中文档的约束，常见的 mapping 属性包括：

- type：字段数据类型，常见的简单类型有：

- - 字符串：text（可分词的文本）、keyword（精确值，例如：品牌、国家、ip地址）
  - 数值：long、integer、short、byte、double、float、
  - 布尔：boolean
  - 日期：date
  - 对象：object

- **index：是否创建索引，默认为 true**
- analyzer：使用哪种分词器
- properties：该字段的子字段

我们以需要存储下面的 JSON 为例来讲解

```shell
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

上面我们了解了 Mapping 属性映射，接下来我们就去看看如何创建索引库及映射。

```shell
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

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561836342-fb060f0f-a467-476f-b401-41f97b6169f7.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210918224647.png)

我们用真实的数据库表来创建一个索引库

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561837506-7b2816ed-b305-4aa7-a6d8-c423f09bd0ba.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210919164626.png)

- 字段名、字段数据类型，可以参考数据表结构的名称和类型
- 是否参与搜索要分析业务来判断，例如图片地址，就无需参与搜索
- 是否分词呢要看内容，内容如果是一个整体就无需分词
- 分词器，我们可以统一使用 ik_max_word

```plain
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

虽然无法修改 mapping 中已有的字段，但是却允许添加新的字段到 mapping 中，不会对倒排索引产生影响。

PUT /索引库名/_mapping {   "properties": {     "新字段名":{       "type": "integer"     }   } }

### 删除索引库

DELETE /索引库名

### 查询索引库

GET /数据库名

## DSL文档操作

### 新增文档

**POST** /索引库名/_doc/文档id {     "字段1": "值1",     "字段2": "值2",     "字段3": {         "子属性1": "值3",         "子属性2": "值4"     }     *// ...* }

POST /xn2001/_doc/1 {     "info": "我不会Java",     "email": "jialna@qq.com",     "name": {         "firstName": "钟",         "lastName": "弟弟"     } }

### 修改文档

修改文档有两种方式：

- 全量修改：直接覆盖原来的文档
- 增量修改：修改文档中的部分字段

**全量修改**是覆盖原来的文档，其本质是：

- 根据指定的 id 删除文档
- 新增一个相同 id 的文档

**注意**：如果根据 id 删除时，id 不存在，第二步的新增也会执行，也就是变成了新增操作

**PUT** /{索引库名}/_doc/id {     "字段1": "值1",     "字段2": "值2",     *// ... 略* }

**PUT** /xn2001/_doc/1 {     "info": "我也不会敲代码",     "email": "3300123589@qq.com",     "name": {         "firstName": "弟弟",         "lastName": "钟"     } }

**增量修改**是只修改指定 id 匹配的文档中的部分字段

**POST** /{索引库名}/_update/文档id {     "doc": {          "字段名": "新的值",     } }

**POST** /heima/_update/1 {   "doc": {     "email": "update@qq.com"   } }

### 查询文档

**GET** /{索引库名称}/_doc/{id}

### 删除文档

**DELETE** /{索引库名}/_doc/{id}

## RestClient文档操作

ES 官方提供了各种不同语言的客户端，用来操作 ES。这些客户端的本质就是组装 DSL 语句，通过 http 请求发送给 ES。官方文档地址：https://www.elastic.co/guide/en/elasticsearch/client/index.html

其中的Java Rest Client又包括两种：

- Java Low Level Rest Client
- Java High Level Rest Client

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561838204-b6a07531-59c8-485c-8cab-f2b1ff7f17cb.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210919234405.png)

我们下面学习的是 **Java HighLevel Rest Client 客户端 API**

### 初始化RestClient

在 elasticsearch 提供的 API 中，elasticsearch 一切交互都封装在一个名为 RestHighLevelClient 的类中，必须先完成这个对象的初始化，建立与 elasticsearch 的连接。

```xml
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
    <version>7.12.1</version>
</dependency>
```

SpringBoot 默认的 ES 版本是 7.6.2，我们需要覆盖默认的ES版本

```xml
<properties>    
  <java.version>1.8</java.version>    
  <elasticsearch.version>7.12.1</elasticsearch.version>
</properties>
```

初始化 RestHighLevelClient，初始化的代码如下：

RestHighLevelClient client = **new** RestHighLevelClient(RestClient.builder(         HttpHost.create("http://192.168.211.128:9200") ));

我们创建一个测试类 HotelIndexTest，然后将初始化的代码编写在 @BeforeEach 方法

```java
 private RestHighLevelClient restHighLevelClient;

    private RestHighLevelClient restHighLevelClient;

    @Test
    void testInit() {
        System.out.println(this.restHighLevelClient);
    }

    @BeforeEach
    void init() {
        this.restHighLevelClient = new RestHighLevelClient(
                RestClient.builder(
                        HttpHost.create("http://192.168.1.104:9200"
                        )));
    }

    @AfterEach
    void down() throws IOException {
        this.restHighLevelClient.close();
    }
```

### 创建索引库

**@Test** **void** **createHotelIndex**() **throws** IOException {     *//指定索引库名*     CreateIndexRequest hotel = **new** CreateIndexRequest("hotel");     *//写入JSON数据，这里是Mapping映射*     hotel.source(HotelConstants.MAPPING_TEMPLATE, XContentType.JSON);     *//创建索引库*     restHighLevelClient.indices().create(hotel, RequestOptions.DEFAULT); }

**public** **class** **HotelConstants** {     **public** **static** String MAPPING_TEMPLATE = "{\n" +             "  \"mappings\": {\n" +             "    \"properties\": {\n" +             "      \"id\": {\n" +             "        \"type\": \"keyword\"\n" +             "      },\n" +             "      \"name\":{\n" +             "        \"type\": \"text\",\n" +             "        \"analyzer\": \"ik_max_word\",\n" +             "        \"copy_to\": \"all\"\n" +             "      },\n" +             "      \"address\":{\n" +             "        \"type\": \"keyword\",\n" +             "        \"index\": false\n" +             "      },\n" +             "      \"price\":{\n" +             "        \"type\": \"integer\"\n" +             "      },\n" +             "      \"score\":{\n" +             "        \"type\": \"integer\"\n" +             "      },\n" +             "      \"brand\":{\n" +             "        \"type\": \"keyword\",\n" +             "        \"copy_to\": \"all\"\n" +             "      },\n" +             "      \"city\":{\n" +             "        \"type\": \"keyword\",\n" +             "        \"copy_to\": \"all\"\n" +             "      },\n" +             "      \"starName\":{\n" +             "        \"type\": \"keyword\"\n" +             "      },\n" +             "      \"business\":{\n" +             "        \"type\": \"keyword\"\n" +             "      },\n" +             "      \"location\":{\n" +             "        \"type\": \"geo_point\"\n" +             "      },\n" +             "      \"pic\":{\n" +             "        \"type\": \"keyword\",\n" +             "        \"index\": false\n" +             "      },\n" +             "      \"all\":{\n" +             "        \"type\": \"text\",\n" +             "        \"analyzer\": \"ik_max_word\"\n" +             "      }\n" +             "    }\n" +             "  }\n" +             "}"; }

### 删除索引库

**@Test** **void** **deleteHotelIndex**() **throws** IOException {     DeleteIndexRequest hotel = **new** DeleteIndexRequest("hotel");     restHighLevelClient.indices().delete(hotel,RequestOptions.DEFAULT); }

### 判断索引库

**@Test** **void** **existHotelIndex**() **throws** IOException {     GetIndexRequest hotel = **new** GetIndexRequest("hotel");     **boolean** exists = restHighLevelClient.indices().exists(hotel, RequestOptions.DEFAULT);     System.out.println(exists); }

### 新增文档

*/**  ** *@author* *乐心湖  ** *@version* *1.0  ** *@date* *2021/9/19 17:18  \*/* **@SpringBootTest** **public** **class** **HotelDocumentTest** {     **private** RestHighLevelClient restHighLevelClient;     **@Autowired**     **private** IHotelService hotelService;     **@Test**     **void** **testInit**(){         System.out.println(**this**.restHighLevelClient);     }     **@Test**     **void** **createHotelIndex**() **throws** IOException {         Hotel hotel = hotelService.getById(61083L);         HotelDoc hotelDoc = **new** HotelDoc(hotel);         *// 1.准备Request对象*         IndexRequest hotelIndex = **new** IndexRequest("hotel").id(hotelDoc.getId().toString());         *// 2.准备Json文档*         hotelIndex.source(JSON.toJSONString(hotelDoc), XContentType.JSON);         *// 3.发送请求*         restHighLevelClient.index(hotelIndex, RequestOptions.DEFAULT);     }     **@BeforeEach**     **void** **init**(){         **this**.restHighLevelClient = **new** RestHighLevelClient(RestClient.builder(                 HttpHost.create("http://192.168.211.128:9200")         ));     }     **@AfterEach**     **void** **down**() **throws** IOException {         **this**.restHighLevelClient.close();     } }

### 查询文档

**@Test** **void** **testGetDocumentById**() **throws** IOException {     *// 1.准备Request*     GetRequest hotel = **new** GetRequest("hotel", "61083");     *// 2.发送请求，得到响应*     GetResponse hotelResponse = restHighLevelClient.get(hotel, RequestOptions.DEFAULT);     *// 3.解析响应结果*     String hotelDocSourceAsString = hotelResponse.getSourceAsString();     *// 4.json转实体类*     HotelDoc hotelDoc = JSON.parseObject(hotelDocSourceAsString, HotelDoc.class);     System.out.println(hotelDoc); }

### 删除文档

**@Test** **void** **testDeleteDocumentById**() **throws** IOException {     DeleteRequest hotel = **new** DeleteRequest("hotel", "61083");     restHighLevelClient.delete(hotel,RequestOptions.DEFAULT); }

### 修改文档

前面我们说过，修改文档有两种方式：

- 全量修改：直接覆盖原来的文档
- 增量修改：修改文档中的部分字段

在 RestClient 的 API 中，全量修改与新增的 API 完全一致，判断依据是 ID

- 如果新增时，ID已经存在，则修改
- 如果新增时，ID不存在，则新增

所以全量修改写法与新增文档一样，下面我们主要是介绍增量修改。

**@Test** **void** **testUpdateDocument**() **throws** IOException {     *// 1.准备Request*     UpdateRequest request = **new** UpdateRequest("hotel", "61083");     *// 2.准备请求参数*     request.doc(         "price", "952",         "starName", "四钻"     );     *// 3.发送请求*     restHighLevelClient.update(request, RequestOptions.DEFAULT); }

### 批量导入文档

案例需求：利用 BulkRequest 批量将数据库数据导入到索引库中。

- 利用 mybatis-plus 查询酒店数据
- 将查询到的酒店数据（Hotel）转换为文档类型数据（HotelDoc）
- 利用 JavaRestClient 中的 BulkRequest 批处理，实现批量新增文档

批量处理 BulkRequest，其本质就是将多个普通的 CRUD 请求组合在一起发送。

因此Bulk中添加了多个IndexRequest，就是批量新增功能了。示例：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561838819-9528221b-6734-4128-8807-63e70bb1e431.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210919234350.png)

利用这一点，我们可以写出自己需要的代码，如下

**@Test** **void** **testBulk**() **throws** IOException {     BulkRequest bulkRequest = **new** BulkRequest();     List<Hotel> hotelList = hotelService.list();     hotelList.forEach(item -> {         HotelDoc hotelDoc = **new** HotelDoc(item);         bulkRequest.add(**new** IndexRequest("hotel")                 .id(hotelDoc.getId().toString())                 .source(JSON.toJSONString(hotelDoc), XContentType.JSON));     });     restHighLevelClient.bulk(bulkRequest, RequestOptions.DEFAULT); }

------

总之，在 Java 代码中，client 针对操作索引库还是文档，基本都是一样的代码

restHighLevelClient.indices().xxx，代表操作索引库

restHighLevelClient.xxx，代表操作文档

而其中所需要的参数，我们直接通过 **ctrl+p** 这样的快捷键去查看就可以，不需要单独记住。

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

------

*// 查询所有* GET /indexName/_search {   "query": {     "match_all": {     }   } }

### 全文检索

使用场景：全文检索查询的基本流程如下：

- 对用户搜索的内容做分词，得到词条
- 根据词条去倒排索引库中匹配，得到文档id
- 根据文档id找到文档，返回给用户

比较常用的场景包括：

- 商城的输入框搜索
- 百度输入框搜索

例如京东：



因为是拿着词条去匹配，因此参与搜索的字段也必须是可分词的text类型的字段。

常见的全文检索查询包括：

- match 查询：单字段查询
- multi_match 查询：多字段查询，任意一个字段符合条件就算符合查询条件

match 查询语法如下：

GET /indexName/_search {   "query": {     "match": {       "FIELD": "TEXT"     }   } }

mulit_match 查询语法如下：

GET /indexName/_search {   "query": {     "multi_match": {       "query": "TEXT",       "fields": ["FIELD1", " FIELD12"]     }   } }

因为我们将 brand、name、business 值都利用 **copy_to** 复制到了 **all** 字段中，你根据三个字段搜索，和根据 all字段搜索效果是一样的。

GET /hotel/_search {   "query": {     "match": {       "all": "7天酒店"     }   } }

GET /hotel/_search {   "query": {     "multi_match": {       "query": "7天酒店",       "fields": ["brand","name"]     }   } }

**搜索字段越多，对查询性能影响越大，因此建议采用 copy_to 将多个字段合并为一个，然后使用单字段查询的方式。**

### 精准查询

精确查询一般是查找 keyword、数值、日期、boolean 等类型字段。所以**不会**对搜索条件分词。

- term：根据词条精确值查询
- range：根据值的范围查询

#### term查询

因为精确查询的字段搜是不分词的字段，因此查询的条件也必须是**不分词**的词条。查询时，用户输入的内容跟自动值完全匹配时才认为符合条件。如果用户输入的内容过多，反而搜索不到数据。

语法说明：

*// term查询* GET /indexName/_search {   "query": {     "term": {       "FIELD": {         "value": "VALUE"       }     }   } }

示例：

GET /hotel/_search {   "query": {     "term": {       "brand": {         "value": "7天酒店"       }     }   } }

#### range查询

范围查询，一般应用在对数值类型做范围过滤的时候。比如做价格范围过滤。

基本语法：

*// range查询* GET /indexName/_search {   "query": {     "range": {       "FIELD": {         "gte": 10, *// 这里的gte代表大于等于，gt则代表大于*         "lte": 20 *// lte代表小于等于，lt则代表小于*       }     }   } }

示例：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561842696-1ca01eef-c1e2-4f40-9618-0d2f0b5be952.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921182858.png)

精确查询常见的有哪些？

- term 查询：根据词条精确匹配，一般搜索 keyword 类型、数值类型、布尔类型、日期类型字段
- range 查询：根据数值范围查询，可以是数值、日期的范围

### 地理坐标查询

地理坐标查询，其实就是根据经纬度查询，官方文档：https://www.elastic.co/guide/en/elasticsearch/reference/current/geo-queries.html

常见的使用场景包括：

- 携程：搜索我附近的酒店
- 滴滴：搜索我附近的出租车
- 微信：搜索我附近的人

附近的酒店：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561841679-0322c0ab-a4e5-4f53-b989-b059596838f5.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921183030.png)

附近的车：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561841988-9a7663ff-b4fb-42cf-a496-5fa22756b420.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921183033.png)

矩形范围查询

矩形范围查询，也就是 geo_bounding_box 查询，查询坐标落在某个矩形范围的所有文档



查询时，需要指定矩形的**左上**、**右下**两个点的坐标，然后画出一个矩形，落在该矩形内的都是符合条件的点。

*// geo_bounding_box查询* GET /indexName/_search {   "query": {     "geo_bounding_box": {       "FIELD": {         "top_left": { *// 左上点*           "lat": 31.1,           "lon": 121.5         },         "bottom_right": { *// 右下点*           "lat": 30.9,           "lon": 121.7         }       }     }   } }

附近查询

附近查询，也叫做距离查询（geo_distance）：查询到指定中心点小于某个距离值的所有文档

在地图上找一个点作为圆心，以指定距离为半径，画一个圆，落在圆内的坐标都算符合条件：



*// geo_distance 查询* GET /indexName/_search {   "query": {     "geo_distance": {       "distance": "15km", *// 半径*       "FIELD": "31.21,121.5" *// 圆心*     }   } }

我们先搜索陆家嘴附近15km的酒店

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561846869-71b73dab-b598-42ff-8aad-d78cf1a067f8.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921183228.png)

发现共有47家酒店，然后把半径缩短到3公里

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561850225-f5dee0cd-e60a-4b7a-b1f8-042b75d8aa4b.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921183240.png)

可以发现，搜索到的酒店数量减少到了5家。

GET /hotel/_search {   "query": {     "match_all": {}   },   "sort": [     {       "_geo_distance" : {           "location": "31.034661,121.612282", *//圆心*           "order" : "asc", *//排序*           "unit" : "km" *//单位*       }     }   ] }

结果为：

"hits" : [     {         "_index" : "hotel",         "_type" : "_doc",         "_id" : "2056298828",         "_score" : null,         "_source" : {             ...         },         "sort" : [             4.8541199685347785 *//这里的结果为离圆心的距离*         ]     },

注意：输出结果中的 **sort** 为距离，比较常用。

排序完成后，页面还要获取我附近每个酒店的具体**距离**值，这个值在响应结果中是独立的：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561856333-567937e6-257a-4076-9978-e4f1a7bd1635.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211019011334.png)

### 复合查询

复合（compound）查询：复合查询可以将其它简单查询组合起来，实现更复杂的搜索逻辑。

- fuction score：算分函数查询，可以控制文档相关性算分，控制文档排名
- bool query：布尔查询，利用逻辑关系组合多个其它的查询，实现复杂搜索

### 相关性算分

这部分内容作为了解即可。

当我们利用 match 查询时，文档结果会根据与搜索词条的关联度打分（_score），返回结果时按照分值降序排列。例如，我们搜索 "虹桥如家"，结果如下：

[   {     "_score" : 17.850193,     "_source" : {       "name" : "虹桥如家酒店真不错",     }   },   {     "_score" : 12.259849,     "_source" : {       "name" : "外滩如家酒店真不错",     }   },   {     "_score" : 11.91091,     "_source" : {       "name" : "迪士尼如家酒店真不错",     }   } ]

elasticsearch 早期使用的打分算法是 **TF-IDF 算法**，公式如下：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561855051-8503fe71-8003-498e-af54-f96cc4596f96.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921205752.png)

在后来的5.1版本升级中，elasticsearch 将算法改进为 **BM25 算法**，公式如下：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561854144-aa22c0eb-c7bb-4926-be12-ce158d6cc837.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921205757.png)

TF-IDF 算法有一各缺陷，就是词条频率越高，文档得分也会越高，单个词条对文档影响较大。而 BM25 则会让单个词条的算分有一个上限，曲线更加平滑：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561859301-279781dc-f9db-450a-a581-b45400e15dfc.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921205831.png)

### 算分函数查询

根据相关度打分是比较合理的需求，但有时候也不能够满足我们的需求。

以百度为例，你搜索的结果中，并不是相关度越高排名越靠前，而是谁给的钱多排名就越靠前。

**要想认为控制相关性算分，就需要利用 elasticsearch 中的 function score 查询了。**

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561858778-6b616baf-6d68-41ec-af4b-09bc27c3c2c4.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921210256.png)

function score 查询中包含四部分内容：

- **原始查询**条件：query 部分，基于这个条件搜索文档，并且基于BM25算法给文档打分，**原始算分**（query score)
- **过滤条件**：filter 部分，符合该条件的文档才会**重新算分**
- **算分函数**：符合 filter 条件的文档要根据这个函数做运算，得到的**函数算分**（function score），有四种函数

- - weight：函数结果是常量
  - field_value_factor：以文档中的某个字段值作为函数结果
  - random_score：以随机数作为函数结果
  - script_score：自定义算分函数算法

- **运算模式**：算分函数的结果、原始查询的相关性算分，两者之间的运算方式，包括：

- - multiply：相乘
  - replace：用 function score 替换 query score
  - sum、avg、max、min

function score 的运行流程如下：

1. 根据**原始条件**查询搜索文档，并且计算相关性算分，称为**原始算分**（query score）
2. 根据**过滤条件**，过滤文档
3. 符合**过滤条件**的文档，基于**算分函数**运算，得到**函数算分**（function score）
4. 将**原始算分**（query score）和**函数算分**（function score）基于**运算模式**做运算，得到最终结果，作为相关性算分。

因此，其中的关键点是

- 过滤条件：决定哪些文档的算分被修改
- 算分函数：决定函数算分的算法
- 运算模式：决定最终算分结果

例如：我们给“如家”这个品牌的酒店排名靠前一些

GET /hotel/_search {   "query": {     "function_score": {       "query": {  .... }, *// 原始查询，可以是任意条件*       "functions": [ *// 算分函数*         {           "filter": { *// 满足的条件，品牌必须是如家*             "term": {               "brand": "如家"             }           },           "weight": 10 *// 算分权重为10*         }       ],       "boost_mode": "sum" *// 加权模式，求和*     }   } }

测试，在未添加算分函数时，如家得分如下

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561877261-4293a2d8-bc87-4f4e-b182-9389864a7f28.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921231508.png)

添加了算分函数后，如家得分就提升了

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561882393-76d82943-2ad3-46d5-87b2-e3a45a4dc605.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921231513.png)

### 布尔查询

布尔查询是一个或多个查询子句的组合，每一个子句就是一个**子查询**。子查询的组合方式有

- must：必须匹配每个子查询，类似“与”
- should：选择性匹配子查询，类似“或”
- must_not：必须不匹配，**不参与算分**，类似“非”
- filter：必须匹配，**不参与算分**

比如在搜索酒店时，除了关键字搜索外，我们还可能根据品牌、价格、城市等字段做过滤

**每一个不同的字段，其查询的条件、方式都不一样，必须是多个不同的查询，而要组合这些查询，就必须用 bool查询了。**

需要注意的是，搜索时，参与**打分的字段越多，查询的性能也越差**。因此这种多条件查询时，建议这样做：

- 搜索框的关键字搜索，是全文检索查询，使用 must 查询，参与算分
- 其它过滤条件，采用 filter 查询，不参与算分

GET /hotel/_search {   "query": {     "bool": {       "must": [         {"term": {"city": "上海" }}       ],       "should": [         {"term": {"brand": "皇冠假日" }},         {"term": {"brand": "华美达" }}       ],       "must_not": [         { "range": { "price": { "lte": 500 } }}       ],       "filter": [         { "range": {"score": { "gte": 45 } }}       ]     }   } }

需求：搜索名字包含“如家”，价格不高于 400，在坐标 31.21,121.5 周围 10km 范围内的酒店。

- 名称搜索，属于全文检索查询，应该参与算分，放到 must 中
- 价格不高于 400，用 range 查询，属于过滤条件，不参与算分，放到 must_not 中
- 周围 10km 范围内，用 geo_distance 查询，属于过滤条件，不参与算分，放到 filter 中

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561884787-9e6f685f-454d-44c9-aab9-7ae908a4d76a.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921233252.png)

bool 查询的几种逻辑关系

- must：必须匹配的条件，可以理解为“与”
- should：选择性匹配的条件，可以理解为“或”
- must_not：必须不匹配的条件，不参与打分
- filter：必须匹配的条件，不参与打分

## 搜索结果处理

### 排序

elasticsearch 默认是根据相关度算分（_score）来排序，但是也支持自定义方式对搜索[结果排序](https://www.elastic.co/guide/en/elasticsearch/reference/current/sort-search-results.html)。可以排序字段类型有：keyword 类型、数值类型、地理坐标类型、日期类型等

keyword、数值、日期类型排序的语法基本一致。

GET /indexName/_search {   "query": {     "match_all": {}   },   "sort": [     {       "FIELD": "desc"  *// 排序字段、排序方式ASC、DESC*     }   ] }

排序条件是一个数组，也就是可以写多个排序条件。按照声明的顺序，当第一个条件相等时，再按照第二个条件排序。

需求描述：酒店数据按照用户评价（score)降序排序，评价相同的按照价格(price)升序排序

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561878979-5c6cb884-d11d-4781-9201-ac56e3fc5809.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921233829.png)

地理坐标排序略有不同

GET /indexName/_search {   "query": {     "match_all": {}   },   "sort": [     {       "_geo_distance" : {           "FIELD" : "纬度，经度", *// 文档中geo_point类型的字段名、目标坐标点*           "order" : "asc", *// 排序方式*           "unit" : "km" *// 排序的距离单位*       }     }   ] }

GET /hotel/_search {   "query": {     "match_all": {}   },   "sort": [     {       "_geo_distance" : {           "location": "31.034661,121.612282",            "order" : "asc",            "unit" : "km"        }     }   ] }

获取你的位置的经纬度的方式：https://lbs.amap.com/demo/jsapi-v2/example/map/click-to-get-lnglat

假设我的位置是：31.034661，121.612282，寻找我周围距离最近的酒店。

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561885789-bef74ce3-2539-4d7a-8722-533a605746f4.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921233931.png)

### 分页

elasticsearch 默认情况下只返回 top10 的数据。而如果要查询更多数据就需要修改分页参数了。

elasticsearch 通过修改 from、size 参数来控制要返回的分页结果：

- from：从第几个文档开始
- size：总共查询几个文档

类似于mysql中的limit ?, ?

GET /hotel/_search {   "query": {     "match_all": {}   },   "from": 0, *// 分页开始的位置，默认为0*   "size": 10, *// 期望获取的文档总数*   "sort": [     {"price": "asc"}   ] }

深度分页问题

现在，我要查询990~1000的数据，查询逻辑要这么写

GET /hotel/_search {   "query": {     "match_all": {}   },   "from": 990, *// 分页开始的位置，默认为0*   "size": 10, *// 期望获取的文档总数*   "sort": [     {"price": "asc"}   ] }

这里是查询990开始的数据，也就是 第990~第1000条 数据。

注意：elasticsearch 内部分页时，必须先查询 0~1000条，然后截取其中的 990 ~ 1000 的这10条

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561885180-b71d1c13-b6bd-4532-b580-2bba65f7a986.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921234503.png)

查询TOP1000，如果 es 是单点模式，这并无太大影响。

但是 elasticsearch 将来一定是集群，例如我集群有5个节点，我要查询 TOP1000 的数据，并不是每个节点查询200条就可以了。节点A的 TOP200，在另一个节点可能排到10000名以外了。

**因此要想获取整个集群的 TOP1000，必须先查询出每个节点的 TOP1000，汇总结果后，重新排名，重新截取 TOP1000。**

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561887893-a0e51df2-b51e-4b9b-a75b-b0e05286d5b8.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921234555.png)

**当查询分页深度较大时，汇总数据过多，对内存和CPU会产生非常大的压力，因此 elasticsearch 会禁止from+ size 超过10000的请求。**

针对深度分页，ES提供了两种解决方案，[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/paginate-search-results.html)：

- search after：分页时需要排序，原理是从上一次的排序值开始，查询下一页数据。官方推荐使用的方式。
- scroll：原理将排序后的文档id形成快照，保存在内存。官方已经不推荐使用。

------

分页查询的常见实现方案以及优缺点

- from + size

- - 优点：支持随机翻页
  - 缺点：深度分页问题，默认查询上限（from + size）是10000
  - 场景：百度、京东、谷歌、淘宝这样的随机翻页搜索

- after search

- - 优点：没有查询上限（单次查询的size不超过10000）
  - 缺点：只能向后逐页查询，不支持随机翻页
  - 场景：没有随机翻页需求的搜索，例如手机向下滚动翻页

- scroll

- - 优点：没有查询上限（单次查询的size不超过10000）
  - 缺点：会有额外内存消耗，并且搜索结果是非实时的
  - 场景：海量数据的获取和迁移。从ES7.1开始不推荐，建议用 after search方案。

### 高亮

我们在百度，京东搜索时，关键字会变成红色，比较醒目，这叫高亮显示：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561892865-3ac39322-af30-4e7a-9e23-8eae266d1578.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921234711.png)

高亮显示的实现分为两步：

- 1）给文档中的所有关键字都添加一个标签，例如<em>标签
- 2）页面给<em>标签编写CSS样式

GET /hotel/_search {   "query": {     "match": {       "FIELD": "TEXT" *// 查询条件，高亮一定要使用全文检索查询*     }   },   "highlight": {     "fields": { *// 指定要高亮的字段*       "FIELD": {         "pre_tags": "<em>",  *// 用来标记高亮字段的前置标签*         "post_tags": "</em>" *// 用来标记高亮字段的后置标签*       }     }   } }

**注意：**

- 高亮是对关键字高亮，因此**搜索条件必须带有关键字**，而不能是范围这样的查询。
- 默认情况下，**高亮的字段，必须与搜索指定的字段一致**，否则无法高亮
- 如果要对非搜索字段高亮，则需要添加一个属性：required_field_match=false

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561904308-3d35e850-43b0-48bb-bc0b-e2cd04176382.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921234739.png)

DSL 总体结构如下：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561898412-9222739a-6e97-44fe-a2ab-c65555302058.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20210921235330.png)

## RestClient文档查询

### 发起查询请求

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561898503-122b43d0-74b7-4dcf-beb6-3a0a76461cd2.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016170057.png)

*/**  ** *@author* *乐心湖  ** *@version* *1.0  ** *@date* *2021/10/16 17:05  \*/* **@SpringBootTest** **public** **class** **HotelSearchTest** {     **private** RestHighLevelClient restHighLevelClient;     **@Autowired**     **private** IHotelService hotelService;     **@Test**     **public** **void** **match_All**() **throws** IOException {         SearchRequest request = **new** SearchRequest("hotel");         request.source()                 .query(QueryBuilders.matchAllQuery());         SearchResponse response = restHighLevelClient.search(request, RequestOptions.DEFAULT);     }     **@BeforeEach**     **void** **init**() {         **this**.restHighLevelClient = **new** RestHighLevelClient(RestClient.builder(                 HttpHost.create("http://192.168.211.128:9200")         ));     }     **@AfterEach**     **void** **down**() **throws** IOException {         **this**.restHighLevelClient.close();     } }

- 第一步，创建SearchRequest对象，指定索引库名
- 第二步，利用request.source()构建 DSL，DSL 中可以包含查询、分页、排序、高亮等

- - query()：代表查询条件，利用 QueryBuilders.matchAllQuery() 构建一个 match_all 查询的 DSL

- 第三步，利用 client.search() 发送请求，得到响应

关键的 API 有两个，一个是 request.source()，其中包含了查询、排序、分页、高亮等所有功能

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561907644-9004817b-ee14-46f2-b519-bd112e44a1ce.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016170203.png)

另一个是 QueryBuilders，其中包含 match、term、function_score、bool 等各种查询

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561916316-46d09f4a-c1b4-4872-bd8f-4621d969a758.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016170234.png)

### 解析查询响应

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561915370-15051eee-e20e-4de5-87c7-5191a6c90bd3.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016174631.png)

Elasticsearch 返回的结果是一个 JSON 字符串，结构包含

- hits：命中的结果

- - total：总条数，其中的value是具体的总条数值
  - max_score：所有结果中得分最高的文档的相关性算分
  - hits：搜索结果的文档数组，其中的每个文档都是一个 json 对象

- - - _source：文档中的原始数据，也是 json 对象

因此，我们解析响应结果，就是逐层解析 JSON 字符串，流程如下

- SearchHits：通过 response.getHits() 获取，就是 json 中的最外层的 hits，代表命中的结果

- - SearchHits.getTotalHits().value：获取总条数信息
  - SearchHits.getHits()：获取 SearchHit 数组，也就是文档数组

- - - SearchHit.getSourceAsString()：获取文档结果中的 _source，也就是原始的 json 文档数据

*/**  ** *@author* *乐心湖  ** *@version* *1.0  ** *@date* *2021/10/16 17:05  \*/* **@SpringBootTest** **public** **class** **HotelSearchTest** {     **private** RestHighLevelClient restHighLevelClient;     **@Autowired**     **private** IHotelService hotelService;     **@Test**     **public** **void** **match_All**() **throws** IOException {         SearchRequest request = **new** SearchRequest("hotel");         request.source()                 .query(QueryBuilders.matchAllQuery());         SearchResponse response = restHighLevelClient.search(request, RequestOptions.DEFAULT);         SearchHits searchHits = response.getHits();         System.out.println("hits.getTotalHits().条数 = " + searchHits.getTotalHits().value);         SearchHit[] hits = searchHits.getHits();         **for** (SearchHit hit : hits) {             String sourceAsString = hit.getSourceAsString();             HotelDoc hotelDoc = JSON.parseObject(sourceAsString, HotelDoc.class);             System.out.println(hotelDoc);         }     }     **@BeforeEach**     **void** **init**() {         **this**.restHighLevelClient = **new** RestHighLevelClient(RestClient.builder(                 HttpHost.create("http://192.168.211.128:9200")         ));     }     **@AfterEach**     **void** **down**() **throws** IOException {         **this**.restHighLevelClient.close();     } }

### match查询

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561916613-e5e74aa7-6053-4075-8a0c-7bcb9689a4d2.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016182859.png)

**@Test** **public** **void** **matchQuery**() **throws** IOException {     SearchRequest request = **new** SearchRequest("hotel");     request.source()             .query(QueryBuilders.matchQuery("all","如家"));     SearchResponse response = restHighLevelClient.search(request, RequestOptions.DEFAULT);     SearchHits searchHits = response.getHits();     System.out.println("hits.getTotalHits().条数 = " + searchHits.getTotalHits().value);     SearchHit[] hits = searchHits.getHits();     **for** (SearchHit hit : hits) {         String sourceAsString = hit.getSourceAsString();         HotelDoc hotelDoc = JSON.parseObject(sourceAsString, HotelDoc.class);         System.out.println(hotelDoc);     } } **@Test** **public** **void** **multiMatchQuery**() **throws** IOException {     SearchRequest request = **new** SearchRequest("hotel");     request.source()             .query(QueryBuilders.multiMatchQuery("如家","name","brand"));     SearchResponse response = restHighLevelClient.search(request, RequestOptions.DEFAULT);     SearchHits searchHits = response.getHits();     System.out.println("hits.getTotalHits().条数 = " + searchHits.getTotalHits().value);     SearchHit[] hits = searchHits.getHits();     **for** (SearchHit hit : hits) {         String sourceAsString = hit.getSourceAsString();         HotelDoc hotelDoc = JSON.parseObject(sourceAsString, HotelDoc.class);         System.out.println(hotelDoc);     } }

### 精确查询

精确查询主要是两者

- term：词条精确匹配
- range：范围查询

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561917523-c5b46c6d-c119-4960-90e8-818949845a40.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016192658.png)

### 布尔查询

布尔查询是用 must、must_not、filter等方式组合其它查询，代码示例如下

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561919349-e4f4feca-159b-4721-a820-e7325f05edb7.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016192745.png)

**@Test** **void** **testBool**() **throws** IOException {     *// 1.准备Request*     SearchRequest request = **new** SearchRequest("hotel");     *// 2.准备DSL*     request.source()             .query(                     QueryBuilders.boolQuery()                             .must(QueryBuilders.termQuery("city", "上海"))                             .filter(QueryBuilders.rangeQuery("price").lte(300))             );     *// 3.发送请求*     SearchResponse response = restHighLevelClient.search(request, RequestOptions.DEFAULT);     *// 4.解析响应*     SearchHits searchHits = response.getHits();     System.out.println("hits.getTotalHits().条数 = " + searchHits.getTotalHits().value);     SearchHit[] hits = searchHits.getHits();     **for** (SearchHit hit : hits) {         String sourceAsString = hit.getSourceAsString();         HotelDoc hotelDoc = JSON.parseObject(sourceAsString, HotelDoc.class);         System.out.println(hotelDoc);     } }

### 排序、分页

搜索结果的排序和分页是与 query 同级的参数，因此同样是使用 request.source() 来设置。

对应的API如下

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561920203-90e21992-3f22-4640-9aaa-de310a427637.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016202447.png)

**@Test** **void** **testPageAndSort**() **throws** IOException {     *// 页码，每页大小*     **int** page = 1, size = 5;     *// 1.准备Request*     SearchRequest request = **new** SearchRequest("hotel");     *// 2.准备DSL*     *// 2.1.query*     request.source().query(QueryBuilders.matchAllQuery());     *// 2.2.排序 sort*     request.source().sort("price", SortOrder.ASC);     *// 2.3.分页 from、size*     request.source().from((page - 1) * size).size(5);     *// 3.发送请求*     SearchResponse response = restHighLevelClient.search(request, RequestOptions.DEFAULT);     *// 4.解析响应*     SearchHits searchHits = response.getHits();     System.out.println("hits.getTotalHits().条数 = " + searchHits.getTotalHits().value);     SearchHit[] hits = searchHits.getHits();     **for** (SearchHit hit : hits) {         String sourceAsString = hit.getSourceAsString();         HotelDoc hotelDoc = JSON.parseObject(sourceAsString, HotelDoc.class);         System.out.println(hotelDoc);     } }

### 高亮

- 查询的 DSL：其中除了查询条件，还需要添加高亮条件，同样是与 query 同级。
- 结果解析：结果除了要解析 _source 文档数据，还要解析高亮结果

**高亮请求的构建 API**

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561924766-73cfc9d6-6982-4b52-b127-533340923315.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016202707.png)

上述代码省略了查询条件部分，但是高亮查询必须使用全文检索查询，并且要有搜索关键字，将来才可以对关键字高亮.

**@Test** **void** **testHighlight**() **throws** IOException {     *// 1.准备Request*     SearchRequest request = **new** SearchRequest("hotel");     *// 2.准备DSL*     *// 2.1.query*     request.source().query(QueryBuilders.matchQuery("all", "如家"));     *// 2.2.高亮*     request.source().highlighter(**new** HighlightBuilder().field("name").requireFieldMatch(**false**));     *// 3.发送请求*     SearchResponse response = client.search(request, RequestOptions.DEFAULT);     *// 4.解析响应*     handleResponse(response); *//代码在下文* }

**高亮结果解析**

高亮的结果与查询的文档结果默认是分离的，并不在一起。

因此解析高亮的代码需要额外处理：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561931421-058540da-300c-47e2-8820-14366bc5f5a5.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211016202853.png)

- 第一步：从结果中获取 source。hit.getSourceAsString()，这部分是非高亮结果，json 字符串，需要反序列为 HotelDoc 对象
- 第二步：获取高亮结果。hit.getHighlightFields()，返回值是一个 Map，key 是高亮字段名称，值是HighlightField 对象，代表高亮值
- 第三步：从 map 中根据高亮字段名称，获取高亮字段值对象 HighlightField
- 第四步：从 HighlightField 中获取 Fragments，并且转为字符串。**这部分是真正的高亮字符串**
- 第五步：用高亮的结果替换 HotelDoc 中的非高亮结果

完整代码如下：

**private** **void** **handleResponse**(SearchResponse response) {     *// 4.解析响应*     SearchHits searchHits = response.getHits();     *// 4.1.获取总条数*     **long** total = searchHits.getTotalHits().value;     System.out.println("共搜索到" + total + "条数据");     *// 4.2.文档数组*     SearchHit[] hits = searchHits.getHits();     *// 4.3.遍历*     **for** (SearchHit hit : hits) {         *// 获取文档source*         String json = hit.getSourceAsString();         *// 反序列化*         HotelDoc hotelDoc = JSON.parseObject(json, HotelDoc.class);         *// 获取高亮结果*         Map<String, HighlightField> highlightFields = hit.getHighlightFields();         **if** (!CollectionUtils.isEmpty(highlightFields)) {             *// 根据字段名获取高亮结果*             HighlightField highlightField = highlightFields.get("name");             **if** (highlightField != **null**) {                 *// 获取高亮值*                 String name = highlightField.getFragments()[0].string();                 *// 覆盖非高亮结果*                 hotelDoc.setName(name);             }         }         System.out.println("hotelDoc = " + hotelDoc);     } }

### 地理坐标查询

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561942447-8ad4f68e-5d84-4e7f-873f-a82f3a9a0c29.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211019000540.png)

### 相关性得分

function_score 查询结构如下

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561936750-0f85f9c6-bfec-4a14-9828-986b2556fcaa.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211019011433.png)

对应的 JavaAPI 如下

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561942805-94d215e1-85e8-47f3-9598-ae0ba1932e67.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211019011439.png)

## DSL数据聚合

[聚合（](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)[aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)[）](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-aggregations.html)可以让我们极其方便的实现对数据的统计、分析、运算。

- 什么品牌的手机最受欢迎？
- 这些手机的平均价格、最高价格、最低价格？
- 这些手机每月的销售情况如何？

在 Elasticsearch 实现这些统计功能比数据库的 sql 要方便的多，而且查询速度非常快，可以实现近实时搜索效果。

聚合常见的有三类

- **桶（Bucket）**聚合：用来对文档做分组

- - TermAggregation：按照文档字段值分组，例如按照品牌值分组、按照国家分组
  - Date Histogram：按照日期阶梯分组，例如一周为一组，或者一月为一组

- **度量（Metric）**聚合：用以计算一些值，比如：最大值、最小值、平均值等

- - Avg：求平均值
  - Max：求最大值
  - Min：求最小值
  - Stats：同时求 max、min、avg、sum 等

- **管道（pipeline）**聚合：其它聚合的结果为基础做聚合

**注意：**参加聚合的字段必须是keyword、日期、数值、布尔类型

### Bucket聚合语法

例如：我们要统计所有数据中的酒店品牌有几种，其实就是按照品牌对数据分组。此时可以根据酒店品牌的名称做聚合，也就是 Bucket 聚合。

GET /hotel/_search {   "size": 0,  *// 设置size为0，结果中不包含文档，只包含聚合结果*   "aggs": { *// 定义聚合*     "brandAgg": { *//给聚合起个名字*       "terms": { *// 聚合的类型，按照品牌值聚合，所以选择term*         "field": "brand", *// 参与聚合的字段*         "size": 20 *// 希望获取的聚合结果数量*       }     }   } }

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561943455-cfaf667f-1665-4774-bf6c-b9041603bae9.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211022184007.png)

默认情况下，Bucket 聚合会统计 Bucket 内的文档数量，记为 _count，并且按照 _count 降序排序。

我们可以指定 order 属性，自定义聚合的排序方式

GET /hotel/_search {   "size": 0,    "aggs": {     "brandAgg": {       "terms": {         "field": "brand",         "order": {           "_count": "asc" *// 按照_count升序排列*         },         "size": 20       }     }   } }

默认情况下，Bucket 聚合是对索引库的所有文档做聚合，但真实场景下，用户会输入搜索条件，因此聚合必须是对搜索结果聚合。那么聚合必须添加限定条件。

我们可以限定要聚合的文档范围，只要添加 query 条件即可；

GET /hotel/_search {   "query": {     "range": {       "price": {         "lte": 200 *// 只对200元以下的文档聚合*       }     }   },    "size": 0,    "aggs": {     "brandAgg": {       "terms": {         "field": "brand",         "size": 20       }     }   } }

这次，聚合得到的品牌明显变少了

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561957114-875394ca-07fa-4b39-bcba-208093f76937.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211022184549.png)

### Metric聚合语法

上面，我们对酒店按照品牌分组，形成了一个个桶。现在我们需要对桶内的酒店做运算，获取每个品牌的用户评分的 min、max、avg 等值。

这就要用到 Metric 聚合了，例如 stats 聚合：就可以获取 min、max、avg 等结果

GET /hotel/_search {   "size": 0,    "aggs": {     "brandAgg": {        "terms": {          "field": "brand",          "size": 20       },       "aggs": { *// 是brands聚合的子聚合，也就是分组后对每组分别计算*         "score_stats": { *// 聚合名称*           "stats": { *// 聚合类型，这里stats可以计算min、max、avg等*             "field": "score" *// 聚合字段，这里是score*           }         }       }     }   } }

这次的 score_stats 聚合是在 brandAgg 的聚合内部嵌套的子聚合。因为我们需要在每个桶分别计算。

另外，我们还可以给聚合结果做个排序，例如按照每个桶的酒店平均分做排序

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561964090-fa8b802c-3b7e-4260-a197-660b1d3a3940.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211022184847.png)

## RestAPI数据聚合

聚合条件与 query 条件同级别，因此需要使用 request.source() 来指定聚合条件

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561956383-ef851461-fb4a-4071-a2e8-4ac773e32407.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211022190429.png)

聚合的结果也与查询结果不同，API 也比较特殊。不过同样是 JSON 逐层解析

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561964346-d9a85ce5-5353-45f0-83fc-0c234d23696f.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211022190457.png)

**@Test** **public** **void** **testAggregation**() **throws** IOException {     SearchRequest request = **new** SearchRequest("hotel");     request.source().aggregation(AggregationBuilders.terms("brandAgg").field("brand").size(20));     SearchResponse response = client.search(request, RequestOptions.DEFAULT);     Terms brandAgg = response.getAggregations().get("brandAgg");     List<? extends Terms.Bucket> buckets = brandAgg.getBuckets();     **for** (Terms.Bucket bucket : buckets) {         String key = bucket.getKeyAsString();         System.out.println("key = " + key);     } }

## 自动补全

当用户在搜索框输入字符时，我们应该提示出与该字符有关的搜索项，提示完整词条的功能，就是自动补全了。

### 拼音分词器

如果我们需要根据拼音字母来推断，因此要用到拼音分词功能。

要实现根据字母做补全，就必须对文档按照拼音分词。插件地址：https://github.com/medcl/elasticsearch-analysis-pinyin

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561963618-f7daa25a-7970-4f7a-9751-ebfb8399b347.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211023015636.png)

使用 docker volume inspect es-plugins 查看插件目录，将下载的文件解压上传，重启 Elasticsearch

测试用法如下：

POST /_analyze {   "text": "如家酒店还不错",   "analyzer": "pinyin" }

结果：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561971784-6e3a5451-dc04-4e69-8839-7c75931b4a45.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211023021713.png)

### 自定义分词器

默认的拼音分词器会将每个汉字单独分为拼音，而我们希望的是每个词条形成一组拼音，需要对拼音分词器做个性化定制，形成自定义分词器。

elasticsearch 中分词器（analyzer）的组成包含三部分：

- character filters：在 tokenizer 之前对文本进行处理。例如删除字符、替换字符
- tokenizer：将文本按照一定的规则切割成词条（term）。例如 keyword，就是不分词；还有 ik_smart
- tokenizer filter：将 tokenizer 输出的词条做进一步处理。例如大小写转换、同义词处理、拼音处理等

文档分词时会依次由这三部分来处理文档：

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561969511-2e02d930-e12d-4326-ab5b-6ca6281b39a1.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211023021451.png)

声明自定义分词器的语法如下：

PUT /test {   "settings": {     "analysis": {       "analyzer": { *// 自定义分词器*         "my_analyzer": {  *// 分词器名称*           "tokenizer": "ik_max_word",           "filter": "py"         }       },       "filter": { *// 自定义tokenizer filter*         "py": { *// 过滤器名称*           "type": "pinyin", *// 过滤器类型，这里是pinyin*           "keep_full_pinyin": false,           "keep_joined_full_pinyin": true,           "keep_original": true,           "limit_first_letter_length": 16,           "remove_duplicated_term": true,           "none_chinese_pinyin_tokenize": false         }       }     }   },   "mappings": {     "properties": {       "name": {         "type": "text",         "analyzer": "my_analyzer",         "search_analyzer": "ik_smart"       }     }   } }

测试

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561984327-cdf9e238-8f78-419c-87bd-c4ba01314f68.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211023021532.png)

注意：**为了避免搜索到同音字，搜索时不要使用拼音分词器**

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561986098-71186acb-6785-4976-aa46-3fece86f632a.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211023022355.png)

### 自动补全查询

elasticsearch 提供了 [Completion Suggester](https://www.elastic.co/guide/en/elasticsearch/reference/7.6/search-suggesters.html) 查询来实现自动补全功能。这个查询会匹配以用户输入内容开头的词条并返回；为了提高补全查询的效率，对于文档中字段的类型有一些约束

- 参与补全查询的字段必须是 completion 类型。
- 字段的内容一般是用来补全的多个词条形成的数组。

*// 创建索引库* PUT test {   "mappings": {     "properties": {       "title":{         "type": "completion"       }     }   } }

然后插入下面的数据

*// 示例数据* POST test/_doc {   "title": ["Sony", "WH-1000XM3"] } POST test/_doc {   "title": ["SK-II", "PITERA"] } POST test/_doc {   "title": ["Nintendo", "switch"] }

查询的 DSL 语句如下

*// 自动补全查询* GET /test/_search {   "suggest": {     "title_suggest": {       "text": "s", *// 关键字*       "completion": {         "field": "title", *// 补全查询的字段*         "skip_duplicates": true, *// 跳过重复的*         "size": 10 *// 获取前10条结果*       }     }   } }

例如一个酒店的索引库完整案例

*// 酒店数据索引库* PUT /hotel {   "settings": {     "analysis": {       "analyzer": {         "text_anlyzer": {           "tokenizer": "ik_max_word",           "filter": "py"         },         "completion_analyzer": {           "tokenizer": "keyword",           "filter": "py"         }       },       "filter": {         "py": {           "type": "pinyin",           "keep_full_pinyin": false,           "keep_joined_full_pinyin": true,           "keep_original": true,           "limit_first_letter_length": 16,           "remove_duplicated_term": true,           "none_chinese_pinyin_tokenize": false         }       }     }   },   "mappings": {     "properties": {       "id":{         "type": "keyword"       },       "name":{         "type": "text",         "analyzer": "text_anlyzer",         "search_analyzer": "ik_smart",         "copy_to": "all"       },       "address":{         "type": "keyword",         "index": false       },       "price":{         "type": "integer"       },       "score":{         "type": "integer"       },       "brand":{         "type": "keyword",         "copy_to": "all"       },       "city":{         "type": "keyword"       },       "starName":{         "type": "keyword"       },       "business":{         "type": "keyword",         "copy_to": "all"       },       "location":{         "type": "geo_point"       },       "pic":{         "type": "keyword",         "index": false       },       "all":{         "type": "text",         "analyzer": "text_anlyzer",         "search_analyzer": "ik_smart"       },       "suggestion":{           "type": "completion",           "analyzer": "completion_analyzer"       }     }   } }

### JavaAPI

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561986138-00b4f585-65d1-4ab4-bcc6-de38a04ddeba.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211025113007.png)

解析响应的代码如下

[![img](https://cdn.nlark.com/yuque/0/2022/png/26526323/1651561991283-049b109d-9aa0-416c-839a-9a380ef94b8f.png)](https://cdn.jsdelivr.net/gh/lexinhu/Image/img/2021/20211025113011.png)

## 数据同步