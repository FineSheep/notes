

# java8新特性

## lambda表达式

> Lambda 表达式（lambda expression）是一个匿名函数，简化我们调用匿名函数的过程。

### 使用标准

* 形式：(函数接口的参数列表 不需要写类型 需要定义参数名称)->{方法体}
* 如果只有一个参数可省略（），只有一条语句可省略{}

### 函数式接口

* Supplier <供应型接口> (个人理解:有一个get()方法)
* Comsumer<消费型接口>
* Predictate<断言型接口>
* Function<转换型接口>

>利用@FunctionalInterface修饰为函数接口

### 无参方法调用

```java
  public interface AcanthopanaxInterface {
        void get();
    }
    public static void main(String[] args) {
        AcanthopanaxInterface acanthopanaxInterface = () -> {
            System.out.println("使用lamdba表达式调用方法");
        };
        acanthopanaxInterface.get();
    }
```

###  带参调用

```java
    public interface AcanthopanaxInterface {
        int get(int a, int b);
    }
    public static void main(String[] args) {
        AcanthopanaxInterface acanthopanaxInterface = (a,b) -> {
        return a+b;   
        };
        System.out.println(acanthopanaxInterface.get(1, 2));
    }
```

### 方法引用

> 需要结合lambda表达式能够让代码变得更加精简。

#### 方法引入

1. 静态方法引入 	 类名::（静态）方法名称
2. 对象方法引入      类名:: 实例方法名称
3. 实例方法引入       new对象 对象实例::方法引入
4. 构造函数引入       类名::new

|     类型     |             语法              | 表达式                             |
| :----------: | :---------------------------: | ---------------------------------- |
| 静态方法引用 |     Class::static_method      | (args) -> 类名.static_method(args) |
| 对象方法引用 |         Class::method         | (inst,args) -> 类名.method(args)   |
| 实例方法引用 | instance::methodClass::method | (args) -> instance.method(args)    |
|  构造器引用  |          Class::new           | (args) -> new 类名(args)           |

> 方法引用提供了非常有用的语法，可以直接引用已有的java类或对象的方法或构造器。方法引用其实也离不开Lambda表达式，
>
> 与lambda联合使用 ，方法引用可以使语言的构造更加紧凑简洁，减少冗余代码。
> 方法引用提供非常有用的语法，可以直接引用已有的java类或者对象中方法或者构造函数，
>
> 方法引用需要配合Lambda表达式语法一起使用减少代码的冗余性问题。

## stream流

### stream介绍

Stream 是JDK1.8 中处理集合的关键抽象概念，Lambda 和 Stream 是JDK1.8新增的函数式编程最有亮点的特性了，它可以指定你希望对集合进行的操作，可以执行非常复杂的查找、过滤和映射数据等操作。使用Stream API 对集合数据进行操作，就类似于使用SQL执行的数据库查询。Stream 使用一种类似用 SQL 语句从数据库查询数据的直观方式来提供一种对 Java 集合运算和表达的高阶抽象。Stream API可以极大提高Java程序员的生产力，让程序员写出高效率、干净、简洁的代码。

这种风格将要处理的元素集合看作一种流， 流在管道中传输， 并且可以在管道的节点上进行处理， 比如筛选， 排序，聚合等。

元素流在管道中经过中间操作（intermediate operation）的处理，最后由最终操作(terminal operation)得到前面处理的结果。

Stream ：非常方便精简的形式遍历集合实现 过滤、排序等。

### stream使用三步:memo:

1. 生成流

   通过数据源（集合，数组等）生成流，如list.stream()

   > parallelStream为并行流采用多线程执行
   >
   > Stream采用单线程执行 
   >
   > parallelStream效率比Stream要高。

2. 中间操作

   一个流后面，可以跟零个或多个中间操作；做了某个动作之后，又返回一个新的流，给下一个操作调用

3. 终结操作

   一个流，只能有一个终结操作，当这个操作执行之后，流就被用光了，无法再操作了。如forEach()

#### 生成流

##### 数组到流

```java
 public static void main(String[] args) {
        int[] arr = new int[]{1, 2, 3, 3, 4};
        int asInt = Arrays.stream(arr).parallel().max().getAsInt();
    }
```

##### 集合生成流

```java
 ArrayList<String> list = new ArrayList<>();
        list.stream();
        list.parallelStream();
```

#### 中间操作

##### list转set

```java
Stream<UserEntity> stream = userEntities.stream();
//将我们的集合转为Set
Set<UserEntity> collectSet = stream.collect(Collectors.toSet());
System.out.println(collectSet);
```

##### list转map

```java
public static void main(String[] args) {
        //数据添加
        ArrayList<User> list = new ArrayList<>();
        list.add(new User("zhangsan", 11));
        list.add(new User("lisi", 12));
        list.add(new User("wangwu", 13));
        list.add(new User("shunliu", 14));

        //转换 
        Map<String, Integer> collect = list.stream().collect(Collectors.toMap(new Function<User, String>() {
            @Override
            public String apply(User user) {
                return user.getName();
            }
        }, new Function<User, Integer>() {
            @Override
            public Integer apply(User user) {
                return user.getAge();
            }
        }));
        
        //输出
        collect.forEach((a, b) -> {
            System.out.println(a + "--->" + b);
        });
        
        //简写
        collect = list.stream().collect(Collectors.toMap(User::getName, User::getAge));
    }
```

##### Reduce 求和

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);
        Optional<Integer> reduce = stream.reduce((a, b) -> a + b);
        System.out.println(reduce.get());
```

##### max和min

```java
        Stream<Integer> stream = Stream.of(1, 2, 3, 4, 5);

        Optional<Integer> max = stream.max((a, b) -> a - b);
        System.out.println(max.get());
```

##### sort

```java
//默认从小到大
Stream<Integer> stream = Stream.of(9, 2, 3, 4, 5);
        Stream<Integer> sorted = stream.sorted();
        sorted.forEach(System.out::println);
//从大到小   stream.sorted((a,b)->b-a);

```

##### limit和skip

Limit 从头开始获取

Skip 就是跳过

## Optional类

Optional 类是一个可以为null的容器对象。如果值存在则isPresent()方法会返回true，调用get()方法会返回该对象。

Optional 是个容器：它可以保存类型T的值，或者仅仅保存null。Optional提供很多有用的方法，这样我们就不用显式进行空值检测。

Optional 类的引入很好的解决空指针异常。

```java
		Integer a1 = 1;
        Optional<Integer> a = Optional.ofNullable(a1);
        System.out.println(a.isPresent());
```

> isPresent true 不为空 
> isPresent返回 false 为空。

### 参数为空设置默认值

```java
		  Integer a1 = null;
        Integer a = Optional.ofNullable(a1).orElse(10);
        System.out.println(a);//a=10
```

### 参数实现过滤

```java
 Integer a1 = 1;
        Optional<Integer> a = Optional.ofNullable(a1);
        boolean isPresent = a.filter(a2 -> a2 > 1).isPresent();
        System.out.println(isPresent);//false 为空
```

### 为空优化

```java
User user =null;
        User user1 = Optional.ofNullable(user).orElseGet(() -> new User("1", 1));
        System.out.println(user1);
```