# CRUD接口

## mapper

> 自定义mapper继承**BaseMapper<>**并指定泛型，例：

```java
public interface AdminMapper extends BaseMapper<Admin> {
}
```

相关方法：

- insertXXX
- updateXXX
- deleteXXX
- selectXXX

> mapper层方法一般以上述名称开头。

## service

> 自定义service继承**IService<>**并指定泛型，例：

```java
public interface AdminService extends IService<Admin> {
}
```

> 通用 Service CRUD 封装IService 接口，进一步封装 CRUD 采用 `get 查询单行` `remove 删除` `list 查询集合` `page 分页` 前缀命名方式区分 `Mapper` 层避免混淆

- saveXXX
- saveOrUpdate
- getXXX
- listXXX
- pageXXX
- countXXX

## service实现

> 继承ServiceImpl<T,E>并实现自定义的接口，例：

```java
public class AdminServiceImpl extends ServiceImpl<AdminMapper, Admin> implements AdminService{
}
```

- ServiceImpl<AdminMapper, Admin>框架提供
  - AdminMapper自定义mapper
  - Admin自定义实体
- AdminService自定义service

#  SimpleQuery 工具类

> 说明:
>
> - 对`selectList`查询后的结果用`Stream`流进行了一些封装，使其可以返回一些指定结果，简洁了api的调用
> - 需要项目中已注入对应实体的BaseMapper
> - 对于下方参数`peeks`，其类型为`Consumer...`，可一直往后叠加操作例如：`List<Long> ids = SimpleQuery.list(Wrappers.lambdaQuery(), Entity::getId, System.out::println, user -> userNames.add(user.getName()));`

## list

```java
// 查询表内记录，封装返回为List<属性>
List<A> list(LambdaQueryWrapper<E> wrapper, SFunction<E, A> sFunction, Consumer<E>... peeks);
// 查询表内记录，封装返回为List<属性>，考虑了并行流的情况
List<A> list(LambdaQueryWrapper<E> wrapper, SFunction<E, A> sFunction, boolean isParallel, Consumer<E>... peeks);
```
查询id在1-3的用户（包括）的姓名
```java
    @Test
    void test2() {
        LambdaQueryWrapper<Admin> queryWrapper = new LambdaQueryWrapper<>();
        queryWrapper.between(Admin::getId, 1, 3);
        SimpleQuery.list(queryWrapper, Admin::getName);
    }
```

```
Jone
Jack
Tom
```

> SimpleQuery.list(queryWrapper, Admin::getName, System.out::println);
>
> 1. **queryWrapper：编写选择条件**
>
> 2. **Admin::getName：组成集合的内容，此处为用户的姓名**

## keyMap

> 封装成**<属性，实体>**

```java
// 查询表内记录，封装返回为Map<属性,实体>
Map<A, E> keyMap(LambdaQueryWrapper<E> wrapper, SFunction<E, A> sFunction, Consumer<E>... peeks);
// 查询表内记录，封装返回为Map<属性,实体>，考虑了并行流的情况
Map<A, E> keyMap(LambdaQueryWrapper<E> wrapper, SFunction<E, A> sFunction, boolean isParallel, Consumer<E>... peeks);
```

```java
@Test
void test3(){
    //我要这个表里对应条件的用户，用id作为key给我一个map
    Map<Long, Admin> map = SimpleQuery.keyMap(Wrappers.<Admin>lambdaQuery().eq(Admin::getAge, 18), Admin::getId);
}
```

```
{1=Admin(id=1, name=Jone, age=18, email=test1@baomidou.com)}
```

## Map

> 不同于keyMap，Map封装成**<属性，属性>**

```java
// 查询表内记录，封装返回为Map<属性,属性>
Map<A, P> map(LambdaQueryWrapper<E> wrapper, SFunction<E, A> keyFunc, SFunction<E, P> valueFunc, Consumer<E>... peeks);
// 查询表内记录，封装返回为Map<属性,属性>，考虑了并行流的情况
Map<A, P> map(LambdaQueryWrapper<E> wrapper, SFunction<E, A> keyFunc, SFunction<E, P> valueFunc, boolean isParallel, Consumer<E>... peeks);
```

查询id1-3的用户，以id为key，age为value。封装成map

```java
Map<String, Integer> map = SimpleQuery.map(Wrappers.<Admin>lambdaQuery()
                .between(Admin::getId, 1, 3),
        Admin::getName, Admin::getAge);
```

```
{Tom=28, Jack=38, Jone=18}
```

## group

```java
// 查询表内记录，封装返回为Map<属性,List<实体>>
Map<K, List<T>> group(LambdaQueryWrapper<T> wrapper, SFunction<T, A> sFunction, Consumer<T>... peeks);

// 查询表内记录，封装返回为Map<属性,List<实体>>，考虑了并行流的情况
Map<K, List<T>> group(LambdaQueryWrapper<T> wrapper, SFunction<T, K> sFunction, boolean isParallel, Consumer<T>... peeks);

// 查询表内记录，封装返回为Map<属性,分组后对集合进行的下游收集器>
M group(LambdaQueryWrapper<T> wrapper, SFunction<T, K> sFunction, Collector<? super T, A, D> downstream, Consumer<T>... peeks);

// 查询表内记录，封装返回为Map<属性,分组后对集合进行的下游收集器>，考虑了并行流的情况
M group(LambdaQueryWrapper<T> wrapper, SFunction<T, K> sFunction, Collector<? super T, A, D> downstream, boolean isParallel, Consumer<T>... peeks);
```

```java
Map<Integer, List<Admin>> group = SimpleQuery.group(Wrappers.<Admin>lambdaQuery(), Admin::getAge);
```

# 条件构造器

> **AbstractWrapper：**QueryWrapper(LambdaQueryWrapper) 和 UpdateWrapper(LambdaUpdateWrapper) 的父类。用于生成 sql 的 where 条件

- lt：less than 小于
- le：less than or equal to 小于等于
- eq：equal to 等于
- ne：not equal to 不等于
- ge：greater than or equal to 大于等于
- gt：greater than 大于

## 非lambda方式

### 查询

```java
@Override
public Admin query(int id){
    //1.new一个QueryWrapper
    QueryWrapper<Admin> queryWrapper = new QueryWrapper<>();
    //2.构造条件。。。。eq啥的，具体看官方文档
    queryWrapper.eq("id", id);
    //3.调用相关方法
    Admin admin = this.getOne(queryWrapper);
    return admin;
    //条件很复杂时可以用如此方式，很简单时直接用MP本身提供的即可
}
```

> :warning:
>
> **eq("id", id)**
>
> **此处带引号的id必须和数据库字段对应！**
>
> **此处带引号的id必须和数据库字段对应！**
>
> **此处带引号的id必须和数据库字段对应！**

### 修改

```java
@Override
public boolean update(int id) {
    UpdateWrapper<Admin> updateWrapper = new UpdateWrapper<>();
    updateWrapper.eq("id", 1);
    Admin admin = new Admin();
    admin.setAge(10000);
    return this.update(admin, updateWrapper);
}
```

> 尝试了下，使用QueryWrapper貌似也可以

##  lambda方式

### 查询

```java
    @Override
    public Admin query(int id) {
/*        QueryWrapper<Admin> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("id", id);
        Admin admin = this.getOne(queryWrapper);*/
        LambdaQueryWrapper<Admin> lambdaQueryWrapper = new LambdaQueryWrapper<>();
        lambdaQueryWrapper.eq(Admin::getId,id);
        Admin admin = this.getOne(lambdaQueryWrapper);
        return admin;
    }
```

> LambdaQueryWrapper和QueryWrapper相比较
>
> LambdaQueryWrapper不用写列名，而是使用纯java的方式，避免了拼写错误(LambdaQueryWrapper的写法如果有错，则在编译期就会报错，而QueryWrapper需要运行的时候调用该方法才会报错)

### 修改

```java
    @Override
    public boolean update(int id) {
/*        UpdateWrapper<Admin> updateWrapper = new UpdateWrapper<>();
        updateWrapper.eq("id", 1);
        Admin admin = new Admin();
        admin.setAge(10000);
        return this.update(admin, updateWrapper);*/
        LambdaUpdateWrapper<Admin> wrapper = new LambdaUpdateWrapper<>();
        wrapper.eq(Admin::getId,id);
        Admin admin = new Admin();
        admin.setAge(234242342);
        return this.update(admin,wrapper);
    }
```

# 链式编程

## 查询

```java
@Override
public Admin chainQuery(int id){
   return this.query().eq("id", id).one();
   //this.lambdaQuery().eq(Admin::getId,id).one();   lambda方式
}
```

> :warning:
>
> 注意与数据库列名相一致
>
> 当然，后面可继续接很多条件

## 修改

```java
@Override
public void chainUpdate(int id){
    this.update().eq("id", id).set("age", 1).update();
    //this.lambdaUpdate().eq(Admin::getId,id).set(Admin::getAge, 1).update();		lambda方式
}
```

# 主键生成策略

1. AUTO （自动增长策略）
2. Input（自定义输入策略）
3. ASSIGN_ID（雪花算法）
4. ASSIGN_UUID（不含中划线的UUID）
5. NONE（无状态）

## AUTO （自动增长策略）

aout自动增长策略，这个配合数据库使用，mysql可以，但是oracle不行。

```java
@Data
public class Admin {
    @TableId(type = IdType.AUTO)
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

## Input（自定义输入策略）

自定义输入策略
Mybatis-Plus 内置了如下数据库主键序列（如果内置支持不满足你的需求，可实现 IKeyGenerator 接口来进行扩展）：
DB2KeyGenerator
H2KeyGenerator
KingbaseKeyGenerator
OracleKeyGenerator
PostgreKeyGenerator

```java
 @Bean
    public OracleKeyGenerator oracleKeyGenerator(){
        return new OracleKeyGenerator();
    }
```

```java
@Data
@KeySequence(value = "SEQ_ACL_ROLE" , clazz = Integer.class)
//value表示oracle序列名称   clazz表示主键类型
public class AclUser implements Serializable {
    private static final long serialVersionUID = 780903014942735924L;
    @TableId(value = "ID",type = IdType.INPUT)
    private Integer id;


```

## ASSIGN_ID（雪花算法）

如果不设置 type 值，默认则使用 IdType.ASSIGN_ID 策略（自 3.3.0 起）。该策略会使用雪花算法自动生成主键 ID，主键类型为 Long 或 String。

> 雪花算法（SnowFlake）是 Twitter 开源的分布式 id 生成算法。其核心思想就是：使用一个 64 bit 的 long 型的数字作为全局唯一 id。在分布式系统中的应用十分广泛，且 ID 引入了时间戳，基本上保持自增的

```java
@Data
public class Admin {
    @TableId(type = IdType.ASSIGN_ID)
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

## ASSIGN_UUID（不含中划线的UUID）

如果使用 IdType.ASSIGN_UUID 策略，则会自动生成不含中划线的 UUID 作为主键，主键类型为 String。

```java
@Data
public class Admin {
    @TableId(type = IdType.ASSIGN_UUID)
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

## NONE（无状态）

如果使用 IdType.NONE 策略，表示未设置主键类型（注解里等于跟随全局,全局里约等于 INPUT）

> 假设我们希望默认全部都使用 AUTO 策略（数据库 ID 自增），那么可以在 application.properties 中添加如下配置进行修改：
> mybatis-plus.global-config.db-config.id-type=auto