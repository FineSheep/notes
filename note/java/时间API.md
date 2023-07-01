# 时间API

> 原文🔗：[Java8日期时间API_ThinkWon的博客-CSDN博客](https://blog.csdn.net/ThinkWon/article/details/111087199)

## LocalDate、LocalTime、LocalDateTime

`java.time.LocalDate` 用于表示 “本地日期”，无 “时间”。LocalDate 不承载时区信息。

`java.time.LocalTime `用于表示 “本地时间”，无 “日期”。LocalTime 不承载时区信息。

`java.time.LocalDateTime `用于表示 “本地日期与时间”。LocalDateTime 不承载时区信息。

LocalDate 实例与 LocalTime 实例能够共同构建 LocalDateTime 实例，由 LocalDateTime 实例能够获取 LocalDate 实例与 LocalTime 实例。

由于 LocalDateTime 不承载时区信息，因此，其不能与 Instant 相互转换，必须提供时区信息。

### 获取对象的方法

**获取对象**的方法：

1. 通过**静态方法** ：now()（获取的时间是**系统当前的时间**）
2. 通过**静态方法**：of()（方法参数可以**指定时间**）

```java
public static void main(String[] args) {
    /* 通过静态方法 now() 返回该类的实例 */
    //获取当前的日期时分秒
    LocalDateTime now = LocalDateTime.now();
    System.out.println(now);

    //获取当前的日期
    LocalDate now1 = LocalDate.now();
    System.out.println(now1);

    //获取当前的时分秒
    LocalTime now2 = LocalTime.now();
    System.out.println(now2);

    System.out.println("=========================================");
    /* 静态方法 of() 返回该类的实例 */
    //指定日期时分秒
    LocalDateTime localDateTime = LocalDateTime.of(2048, 11, 25, 12, 00, 30);
    System.out.println(localDateTime);

    //指定日期
    LocalDate date = LocalDate.of(2020, 12, 12);
    System.out.println(date);

    //指定时分秒
    LocalTime time = LocalTime.of(14, 20, 30);
    System.out.println(time);

}
```

```
2022-12-22T14:18:55.056
2022-12-22
14:18:55.056
=========================================
2048-11-25T12:00:30
2020-12-12
14:20:30
```

### 常用方法

与获取相关的方法(get系类的方法)

- getYear()：获取年
- getHour()：获取小时
- getMinute()：获取分钟
- getSecond()：获取秒值
- getDayOfMonth()：获得月份天数(1-31)
- getDayOfYear()：获得年份天数(1-366)
- getDayOfWeek()：获得星期几(返回一个 DayOfWeek枚举值)
- getMonth()：获得月份(返回一个 Month 枚举值)
- getMonthValue()：获得月份(1-12)
- getYear()：获得年份
  

转换方法

- toLocalDate()：将LocalDateTime转换为相应的LocalDate对象
- toLocalTime()：将LocalDateTime转换为相应的LocalTime对象

判断的方法

- isAfter()：判断一个日期**是否在指定日期之后**
- isBefore()：判断一个日期**是否在指定日期之前**
- isEqual()：判断两个日期**是否相同**
- isLeapYear()：判断**是否是闰年**（注意是**LocalDate类 和 LocalDateTime类**特有的方法）

增减年月日时分秒的方法（plus/minus系列的方法）

增加相关的方法

- plusYears(int offset)：增加指定年份
- plusMonths(int offset)：增加指定月份
- plusWeeks(int offset)：增加指定周
- plusDates(int offset)：增加指定日
- plusHours(int offset)：增加指定时
- plusMinuets(int offset)：增加指定分
- plusSeconds(int offset)：增加指定秒
- plusNanos(int offset)：增加指定纳秒

减少相关的方法

- minusYears(int offset)：减少指定年
- minusMonths(int offset)：减少指定月
- minusWeeks(int offset)：减少指定周
- minusDates(int offset)：减少指定日
- minusHours(int offset)：减少指定时
- minusMinuets(int offset)：减少指定分
- minusSeconds(int offset)：减少指定秒
- minusNanos(int offset)：减少指定纳秒
  

指定年月日时分秒的方法

- with(TemporalAdjuster adjuster):指定特殊时间**(指定转换器)**
- withYear(int year):指定年
- withDayOfYear(int dayOfYear):指定日
- withMonth(int month):指定月
- withDayOfMonth(int dayOfMonth):指定日
  

```java
//指定某个日期的方法 with()方法
LocalDate now2 = LocalDate.now();
System.out.println(now2);
LocalDate localDate = now2.withYear(2014);
System.out.println(localDate);
```
```
2022-12-22
2014-12-22
```

将日期格式化为字符串的方法

- format()：**格式化字符串**

```java
    public void test03() {
        //获取当前日期时分秒
        LocalDateTime now = LocalDateTime.now();

        //默认格式  年-月-日T时:分:秒
        System.out.println(now);
        //指定格式
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH时mm分ss秒");
        //传入格式
        String format = now.format(formatter);
        System.out.println(format);
    }
```

```
2022-12-22T14:38:08.399
2022年12月22日 14时38分08秒
```

解析字符串为日期时间的方法

- pase(String str)：将一个日期字符串解析成日期对象,注意字符串日期的写法的格式要正确,否则解析失败
- pase(String str, DateTimeFormatter formatter)：将字符串按照参数传入的格式进行解析

```java
public void test06() {
    //给出一个符合默认格式要求的日期字符串
    String dateStr = "2020-01-01";

    //把日期字符串解析成日期对象 如果日期字符串时年月日 解析时用  LocalDate
    LocalDate parse = LocalDate.parse(dateStr);
    System.out.println(parse);

    System.out.println("===========================================");
    //给出一个符合默认格式要求的 时分秒 字符串
    String dateTimeStr = "14:20:30";

    //把 时分秒 字符串解析成时分秒对象
    LocalTime parse1 = LocalTime.parse(dateTimeStr);
    System.out.println(parse1);

    System.out.println("=========================================");
    //给出一个符合默认格式要求的 日期时分秒 字符串
    String str = "2018-12-12T14:20:30";

    //把 日期时分秒 字符串解析成时分秒对象
    LocalDateTime parse2 = LocalDateTime.parse(str);
    System.out.println(parse2);

    System.out.println("========================================");
    //给出一个自定义日期时分秒格式字符串
    String dateStr2 = "2020年12月12日 12:13:14";

    //给出一个自定义解析格式
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy年MM月dd日 HH:mm:ss");

    //按照指定的格式去解析
    LocalDateTime parse3 = LocalDateTime.parse(dateStr2, formatter);
    System.out.println(parse3);
}
```

```
2020-01-01
===========================================
14:20:30
=========================================
2018-12-12T14:20:30
========================================
2020-12-12T12:13:14
```

## TemporalAdjuster接口 - 时间调节器

​		前面看到的所有日期操作都是相对比较直接的。有的时候，你需要进行一些更加灵活复杂的操作，比如，将日期调整到下个周日、下个工作日，或者是本月的最后一天。这时，就需要时间调节器 TemporalAdjuster，可以更加灵活地处理日期。TemporalAdjusters 工具提供了一些通用的功能，并且你还可以新增你自己的功能。

```java
public void testTemporalAdjust() {
    //当前时间
    LocalDate now = LocalDate.now();
    //指定日期
    //对于一些特殊的日期，可以通过一个工具类TemporalAdjusters 来指定
    //见名知意，本月第一天
    TemporalAdjuster adjust = TemporalAdjusters.firstDayOfMonth();
    LocalDate with = now.with(adjust);
    System.out.println(with);
    //下周周末
    TemporalAdjuster next = TemporalAdjusters.next(DayOfWeek.SUNDAY);
    LocalDate with1 = now.with(next);
    System.out.println(with1);
    //自定义  下一个工作日
    LocalDate with2 = now.with(new TemporalAdjuster() {
        @Override
        public Temporal adjustInto(Temporal nowDate) {
            //向下转型
            LocalDate date = (LocalDate) nowDate;
            DayOfWeek dayOfWeek = date.getDayOfWeek();
            if (dayOfWeek.equals(DayOfWeek.FRIDAY)) {
                return date.plusDays(3);
            } else if (dayOfWeek.equals(DayOfWeek.SATURDAY)) {
                return date.plusDays(2);
            } else {
                return date.plusDays(1);
            }
        }
    });
    System.out.println("下一个工作日是：" + with2 + "   " + with2.getDayOfWeek());
}
```

```
2022-12-01
2022-12-25
下一个工作日是：2022-12-23   FRIDAY
```

## Duration类 - 用于计算两个“时间”间隔的类

​		Duration 表示一个时间段，Duration 包含两部分：seconds 表示秒，nanos 表示纳秒，它们的组合表达了时间长度。因为 Duration 表示时间段，所以 Duration 类中不包含 now() 静态方法。**注意，Duration 不包含毫秒这个属性**。Duration只能处理两个LocalTime, LocalDateTime, ZonedDateTime; 如果传入的是LocalDate，将会抛出异常.

常用API：

- 静态方法 between()：计算两个时间的间隔，默认是秒
- toDays()：将时间转换为以天为单位的
- toHours()：将时间转换为以时为单位的
- toMinutes()：将时间转换为以分钟为单位的
- toMillis()：将时间转换为以毫秒为单位的
- toNanos()：将时间转换为以纳秒为单位的
  

```java
public void test10() {
    //计算时间的间隔
    Instant start = Instant.now();
    for (int i = 0; i < 100000; i++) {
        System.out.print(i + "\t");
    }
    Instant end = Instant.now();
    long l = Duration.between(start, end).toMillis();
    System.out.println();
    //间隔的时间
    System.out.println("循环耗时：" + l + "毫秒");
}
```

## Period类 - 用于计算两个“日期”间隔的类

​		Period 在概念上和 Duration 类似，区别在于 Period 是以年月日来衡量一个时间段。Duration 用于计算两个时间间隔，Period 用于计算两个日期间隔，所以 between() 方法只能接收 LocalDate 类型的参数。

- **静态方法 between()**：计算两个**日期之间的间隔**
- getYears()：获取年份
- getMonths()：获取月份
- getDays()：获取天数

```java
public void test11() {
    //计算两个日期的间隔
    LocalDate birthday = LocalDate.of(2012, 12, 12);
    LocalDate now = LocalDate.now();

    //我从出生到现在，有多少岁，零几个月，几天
    //计算两个日期的间隔
    Period between = Period.between(birthday, now);
    int years = between.getYears();
    int months = between.getMonths();
    int days = between.getDays();
    System.out.println("玛雅人的地球都消灭了" + years + "年" + months + "月" + days + "天了...");
}
```

```
玛雅人的地球都消灭了10年0月10天了...
```

## Instant 时间戳类

​		`java.time.Instant` 时间线上的一个瞬时点，承载纳秒级精度的 Unix 时间戳，其 `String toString()` 方法基于 ISO-8601 进行格式化。`Instant` 不承载时区信息。

**获取对象的方法**：now()：注意默认获取出来的是默认时区，和我们**相差八个小时**（因为我们在**东八时区**）

**设置偏移量的方法**：atOffset()

**获取系统默认时区时间的方法**：atZone()：方法的参数是要一个**时区的编号**(可以通过时区编号类获取ZonedDateTime类的对象)

**get系列的方法**

- getEpochSecond()：获取从**1970-01-01 00:00:00**到**当前时间**的**秒值**
- toEpochMilli()：获取从**1970-01-01 00:00:00**到**当前时间**的**毫秒值**
- getNano()：把获取到的**当前时间**的秒数 **换算成纳秒**

**ofEpoch系列方法**

- ofEpochSecond()：给计算机元年**增加秒数**
- ofEpochMilli()：给计算机元年**增加毫秒数**

```java
 	Instant now1 = Instant.now();
    //toEpochMilli():从1970 -01 - 01 00:00:00 截止到当前时间间隔的毫秒值
    long l1 = now1.toEpochMilli();
```

```java
public void test09() {
    //  Instant 时间戳类从1970 -01 - 01 00:00:00 截止到当前时间的毫秒值
    Instant now = Instant.now();
    System.out.println(now); //获取的是默认时区，获取的不是中国 的时区
    //获取当前时区的，我们可以添加偏移量,返回偏移过后的日期
    OffsetDateTime offsetDateTime = now.atOffset(ZoneOffset.ofHours(8));
    System.out.println(offsetDateTime);
    System.out.println("===========================");

    //从1970 - 01 - 01 00:00:00 截止到当前时间的毫秒值
    long l = System.currentTimeMillis();
    System.out.println(l);
    long time = new Date().getTime();
    System.out.println(time);

    //JDK1.8 Instant 时间戳类从1970 -01 - 01 00:00:00 截止到当前时间的毫秒值
    Instant now1 = Instant.now();

    //toEpochMilli():从1970 -01 - 01 00:00:00 截止到当前时间间隔的毫秒值
    long l1 = now1.toEpochMilli();
    System.out.println(l1);

    //获取从1970 -01 - 01 00:00:00 截止到当前时间间隔的秒值
    long epochSecond = now1.getEpochSecond();
    System.out.println(epochSecond);

    System.out.println("==========================");
    //给计算机元年增加相应的时间量
    Date date = new Date(1000 * 60 * 60 * 24);
    System.out.println(date);

    //现在 给计算机元年增加相应的时间量
    //5. ofEpochSecond() 方法 给计算机元年增加秒数
    //ofEpochMilli() 给计算机元年增加毫秒数
    Instant instant = Instant.ofEpochMilli(1000 * 60 * 60 * 24);
    System.out.println(instant);

    //ofEpochSecond() 方法 给计算机元年增加秒数
    Instant instant1 = Instant.ofEpochSecond(60 * 60 * 24);
    System.out.println(instant1);
}
```

## ZonedDateTime - 带时区的日期时间

​		ZonedDateTime 这**带有特定时区**

## ZoneId - 世界时区类

​		Java 使用 ZoneId 来标识不同的时区。时区从基准 UTC 开始的一个固定偏移。ZoneId 的子类 ZoneOffset，代表了这种从伦敦格林威治零度子午线开始的时间偏移，也就是时差。

**常用API**：

- getAvailableZoneIds()：获取世界各个地方的时区的集合
- systemDefault()：获取系统默认时区的ID
- of(String zoneName)：根据各个地区的时区ID名创建对象

## DateTimeFormatter类 - 用于解析日期字符串和格式化日期输出

​		DateTimeFormatter用于解析日期字符串和格式化日期输出，创建格式化器最简单的方法是通过 DateTimeFormatter 的静态工厂方法以及常量。

​		在java8之前，我们进行时间格式化主要是使用`SimpleDateFormat`，而在java8中，主要是使用`DateTimeFormatter`，java8中，预定义了一些标准的时间格式，我们可以直接将时间转换为标准的时间格式

**常用API** ：

- ofPattern(“yyyy-MM-dd”)：静态方法，通过给定格式获取对象
- format()：把一个日期对象的**默认格式** 格式化成**指定的格式**的**字符串**

```java
public void test12() {
    // 之前格式化日期的类  new SimpleDateFormat()
    //JDK1.8 DateTimeFormatter
    //指定格式 静态方法 ofPattern()
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    // DateTimeFormatter 自带的格式方法
    LocalDateTime now = LocalDateTime.now();

    //把日期对象，格式化成字符串
    String format = formatter.format(now);
    //刚才的方式是使用的日期自带的格式化方法
    String format1 = now.format(formatter);
    System.out.println(format);
    System.out.println(format1);
}
```

## Java8 日期时间类与Date类的相互转化

​		在转换中，我们需要注意，因为java8之前Date是包含日期和时间的，而LocalDate只包含日期，LocalTime只包含时间，所以与Date在互转中，势必会丢失日期或者时间，或者会使用起始时间。如果转LocalDateTime，那么就不存在信息误差。

### Date与Instant互相转换

```java
public void test18() {
    //Date与Instant互相转换
    Instant instant  = Instant.now();
    Date date = Date.from(instant);
    System.out.println(date);

    Instant instant2 = date.toInstant();
    System.out.println(instant2);
}
```

### Date与LocalDateTime互相转换

data-instant-(设置时区)-LocalDateTime

```java
public void test19() {
    //Date - LocalDateTime
    Date date = new Date();
    System.out.println("current date: " + date);

    LocalDateTime localDateTime1 = date.toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime();
    System.out.println("localDateTime1: " + localDateTime1);

    LocalDateTime localDateTime2 = LocalDateTime.ofInstant(date.toInstant(), ZoneId.systemDefault());
    System.out.println("localDateTime2: " + localDateTime2);

    //LocalDateTime - Date
    LocalDateTime localDateTime = LocalDateTime.now();
    System.out.println("localDateTime: " + localDateTime);

    Date date1 = Date.from(localDateTime.atZone(ZoneId.systemDefault()).toInstant());
    System.out.println("date1: " + date1);
}
```