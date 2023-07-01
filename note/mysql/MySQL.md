# :label:数据来源

> [MySQL 速成课程 - Ben Forta（本·福塔）](https://forta.com/books/0672327120/)

![image-20221227113128225](D:/notes/3150/image-20221227113128225.png)

# 数据库概述

**MySQL图解应用** ：

<img src="https://xingqiu-tuchuang-1256524210.cos.ap-shanghai.myqcloud.com/2767/image-20220207184620623.png" alt="image-20220207184620623" style="zoom:80%;" />

## 数据库相关概念

**数据库：DataBase (DB)** 

- 按照一定格式存储数据的一些文件的组合
- 存储数据的仓库，数据文件，日志文件。具有一定特定格式的数据。

**数据库管理系统：DataBase Management （DBMS）**

- 专门用来管理数据库中的数据的，数据库管理系统可以对数据当中的数据进行增删改查

**SQL ：结构化查询语言**

- 使用DBMS负责执行SQL语句，来完成数据库中的增删改查。
- SQL是一套标准语言，主要学习SQL语句。SQL语句可以在MySQL、Oracle、DB2中使用。

三者之间的关系：

> DBMS ----执行----->      SQL    -------操作----> DB

# 使用MySQL

> MySQL必知必会~~~~

> - 命令输入在mysql>之后；
> - 命令用;或\g结束，换句话说，仅按Enter不执行命令；
> - 输入help或\h获得帮助，也可以输入更多的文本获得特定命令的
> - 帮助（如，输入help select获得使用SELECT语句的帮助）； 
> - 输入quit或exit退出命令行实用程序。

## 连接

- 主机：如果连接到本地MySQL服务器，为localhost
- 端口：（如果使用默认端口3306之外的端口
- 用户名
- 密码

本机连接：

> mysql -u root -p

```mysql
C:\Users\yang>mysql -u root -p
Enter password: ****
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 382
Server version: 5.7.37-log MySQL Community Server (GPL)

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

远程数据库连接：

> mysql -h mysql服务器的IP地址 -P 端口号（通常为3306） -u 用户名 -p密码  

```
mysql -h mysql服务器的IP地址 -P 端口号（通常为3306） -u 用户名 -p密码       

-h: mysql服务器的IP地址
-P: 大写的P选项表示端口号，端口号默认为3306，可省略
-u: 用户名
-p: 小写的p表示密码，当-p后输入密码时，会直接登陆。当-p后不输入密码时，会要求输入密码，但密码不显示
```

```mysql
C:\Users\yang>mysql -h ************* -P *** -u ************* -p
Enter password: *************

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## 查看数据库

> show databases;

```mysql
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| cloud              |
| db_admin3          |
| db_membership      |
| hmdp               |
| jpa                |
| learningplat       |
| lovefinder         |
| mysql              |
| performance_schema |
| recruit            |
| report             |
| shrio              |
| sys                |
| yupao              |
| yupi               |
+--------------------+
16 rows in set (0.00 sec)
```

## 选择数据库

> use learningplat;

```mysql
mysql> use learningplat;
Database changed
```

## 查看表

> show tables;

```mysql
mysql> show tables;
+------------------------+
| Tables_in_learningplat |
+------------------------+
| mistakerecord          |
| post                   |
| questions              |
| user                   |
+------------------------+
4 rows in set (0.00 sec)
```

## 查看列

> show columns from TABLE;
>
>  DESCRIBE TABLE;

![image-20221226104623710](D:/notes/3150/image-20221226104623710.png)

> MySQL支持用DESCRIBE作为SHOW COLUMNS FROM的一种快捷方式。换句话说，DESCRIBE user;是SHOW COLUMNS FROM user;的一种快捷方式。

其他show:	

- SHOW STATUS，用于显示广泛的服务器状态信息；
-  SHOW CREATE DATABASE和SHOW CREATE TABLE，分别用来显示创建特定数据库或表的MySQL语句；
- SHOW GRANTS，用来显示授予用户（所有用户或特定用户）的安全权限；
- SHOW ERRORS和SHOW WARNINGS，用来显示服务器错误或警告消息。

# 检索数据

## 检索单列

```mysql
mysql> select username from user;
+------------+
| username   |
+------------+
| tom        |
| pom6mqpkin |
+------------+
2 rows in set (0.00 sec)
```

> :jack_o_lantern:如果没有明确排序查询结果，则返回的数据的顺序没有特殊意义。返回数据的顺序可能是数据被添加到表中的顺序，也可能不是。只要返回相同数目的行，就是正常的。

- 多条SQL语句必须以分号（；）分隔。单条不需要，命令行必须分号分隔。
- SQL语句不区分大小写。

## 检索多列

```mysql
mysql> select id,username  from user;
+----+------------+
| id | username   |
+----+------------+
|  3 | tom        |
|  7 | pom6mqpkin |
+----+------------+
2 rows in set (0.00 sec)
```

## 检索所有的列

```mysql
mysql> select * from user;
```

> :warning:一般，除非你确实需要表中的每个列，否则最好别使用*通配符。虽然使用通配符可能会使你自己省事，不用明确列出所需列，但检索不需要的列通常会降低检索和应用程序的性能。

## 去重

> :sunny:上面都是用的我自己的数据库，然后我把官方的数据下载了。数据放最上面啦。

```mysql
mysql> select vend_id from products;
+---------+
| vend_id |
+---------+
|    1001 |
|    1001 |
|    1001 |
|    1002 |
|    1002 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1003 |
|    1005 |
|    1005 |
+---------+
14 rows in set (0.00 sec)
```

使用distinct去重:

```mysql
mysql> select distinct vend_id from products;
+---------+
| vend_id |
+---------+
|    1001 |
|    1002 |
|    1003 |
|    1005 |
+---------+
4 rows in set (0.00 sec)
```

> :bell:**DISTINCT关键字应用于所有列而不仅是前置它的列。**
>
> 如果给出SELECT DISTINCT vend_id, prod_name，除非指定的两个列都相同，否则所有行都将被检索出来。

```mysql
mysql> select distinct vend_id,prod_name from products;
+---------+----------------+
| vend_id | prod_name      |
+---------+----------------+
|    1001 | .5 ton anvil   |
|    1001 | 1 ton anvil    |
|    1001 | 2 ton anvil    |
|    1003 | Detonator      |
|    1003 | Bird seed      |
|    1003 | Carrots        |
|    1002 | Fuses          |
|    1005 | JetPack 1000   |
|    1005 | JetPack 2000   |
|    1002 | Oil can        |
|    1003 | Safe           |
|    1003 | Sling          |
|    1003 | TNT (1 stick)  |
|    1003 | TNT (5 sticks) |
+---------+----------------+
14 rows in set (0.00 sec)
```

## 限制结果

> 显示前三条数据

```mysql

mysql> select * from products limit 3;
+---------+---------+--------------+------------+----------------------------------------------------------------+
| prod_id | vend_id | prod_name    | prod_price | prod_desc                                                      |
+---------+---------+--------------+------------+----------------------------------------------------------------+
| ANV01   |    1001 | .5 ton anvil |       5.99 | .5 ton anvil, black, complete with handy hook                  |
| ANV02   |    1001 | 1 ton anvil  |       9.99 | 1 ton anvil, black, complete with handy hook and carrying case |
| ANV03   |    1001 | 2 ton anvil  |      14.99 | 2 ton anvil, black, complete with handy hook and carrying case |
+---------+---------+--------------+------------+----------------------------------------------------------------+
```

> 忽略前3条,展示5条数据

```mysql
mysql> select * from products limit 3,5;
+---------+---------+--------------+------------+-------------------------------------------------+
| prod_id | vend_id | prod_name    | prod_price | prod_desc                                       |
+---------+---------+--------------+------------+-------------------------------------------------+
| DTNTR   |    1003 | Detonator    |      13.00 | Detonator (plunger powered), fuses not included |
| FB      |    1003 | Bird seed    |      10.00 | Large bag (suitable for road runners)           |
| FC      |    1003 | Carrots      |       2.50 | Carrots (rabbit hunting season only)            |
| FU1     |    1002 | Fuses        |       3.42 | 1 dozen, extra long                             |
| JP1000  |    1005 | JetPack 1000 |      35.00 | JetPack 1000, intended for single use           |
+---------+---------+--------------+------------+-------------------------------------------------+
5 rows in set (0.00 sec)
```

## 使用限定名

> select 	表.列  	from 	库.表;

```mysql
mysql> select products.prod_name
    -> from mysql_test.products;
+----------------+
| prod_name      |
+----------------+
| .5 ton anvil   |
| 1 ton anvil    |
| 2 ton anvil    |
| Detonator      |
| Bird seed      |
| Carrots        |
| Fuses          |
| JetPack 1000   |
| JetPack 2000   |
| Oil can        |
| Safe           |
| Sling          |
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
14 rows in set (0.00 sec)
```

# 排序

## 单个排列

> 使用order by排序,默认升序
>
> 升序 ASC  降序 DESC

```mysql
mysql> select prod_name from products order by prod_name;
+----------------+
| prod_name      |
+----------------+
| .5 ton anvil   |
| 1 ton anvil    |
| 2 ton anvil    |
| Bird seed      |
| Carrots        |
| Detonator      |
| Fuses          |
| JetPack 1000   |
| JetPack 2000   |
| Oil can        |
| Safe           |
| Sling          |
| TNT (1 stick)  |
| TNT (5 sticks) |
+----------------+
14 rows in set (0.00 sec)
```

> 降序排列

```mysql
mysql> select prod_name from products order by prod_name DESC;
+----------------+
| prod_name      |
+----------------+
| TNT (5 sticks) |
| TNT (1 stick)  |
| Sling          |
| Safe           |
| Oil can        |
| JetPack 2000   |
| JetPack 1000   |
| Fuses          |
| Detonator      |
| Carrots        |
| Bird seed      |
| 2 ton anvil    |
| 1 ton anvil    |
| .5 ton anvil   |
+----------------+
14 rows in set (0.00 sec)
```

## 多个排列

首先按价格，然后再按名称排序。仅在多个行具有相同的prod_price值时才对产品按prod_name进行排序。

```mysql
mysql> select prod_id,prod_price,prod_name from products order by prod_price,prod_name;
+---------+------------+----------------+
| prod_id | prod_price | prod_name      |
+---------+------------+----------------+
| FC      |       2.50 | Carrots        |
| TNT1    |       2.50 | TNT (1 stick)  |
| FU1     |       3.42 | Fuses          |
| SLING   |       4.49 | Sling          |
| ANV01   |       5.99 | .5 ton anvil   |
| OL1     |       8.99 | Oil can        |
| ANV02   |       9.99 | 1 ton anvil    |
| FB      |      10.00 | Bird seed      |
| TNT2    |      10.00 | TNT (5 sticks) |
| DTNTR   |      13.00 | Detonator      |
| ANV03   |      14.99 | 2 ton anvil    |
| JP1000  |      35.00 | JetPack 1000   |
| SAFE    |      50.00 | Safe           |
| JP2000  |      55.00 | JetPack 2000   |
+---------+------------+----------------+
14 rows in set (0.00 sec)
```

# where数据过滤

## 匹配检查

| 操作符  | 说明         |
| ------- | ------------ |
| =       | 等于         |
| <>      | 不等于       |
| !=      | 不能与       |
| <       | 小于         |
| <=      | 小于等于     |
| >       | 大于         |
| >=      | 大于等于     |
| BETWEEN | 在指定值之间 |

```mysql
mysql> select prod_id,prod_price,prod_name from products where prod_price between 5 and 10 ;
+---------+------------+----------------+
| prod_id | prod_price | prod_name      |
+---------+------------+----------------+
| ANV01   |       5.99 | .5 ton anvil   |
| ANV02   |       9.99 | 1 ton anvil    |
| FB      |      10.00 | Bird seed      |
| OL1     |       8.99 | Oil can        |
| TNT2    |      10.00 | TNT (5 sticks) |
+---------+------------+----------------+
5 rows in set (0.00 sec)
```

## NULL检测

> NULL 无值（no value），它与字段包含0、空字符串或仅仅包含空格不同。

```mysql
mysql> select cust_id,cust_name,cust_email from customers where cust_email is null;
+---------+-------------+------------+
| cust_id | cust_name   | cust_email |
+---------+-------------+------------+
|   10002 | Mouse House | NULL       |
|   10005 | E Fudd      | NULL       |
+---------+-------------+------------+
2 rows in set (0.00 sec)

mysql>
```

## and操作符

AND 用在WHERE子句中的关键字，用来指示检索满足所有给定条件的行。

如果有多个条件,每加一个条件就需要加一个**and。**

```mysql
mysql> select prod_id,prod_name,prod_price from products where vend_id = 1003 and prod_price <=10 ;
+---------+----------------+------------+
| prod_id | prod_name      | prod_price |
+---------+----------------+------------+
| FB      | Bird seed      |      10.00 |
| FC      | Carrots        |       2.50 |
| SLING   | Sling          |       4.49 |
| TNT1    | TNT (1 stick)  |       2.50 |
| TNT2    | TNT (5 sticks) |      10.00 |
+---------+----------------+------------+
5 rows in set (0.00 sec)
```

## or操作符

OR操作符与AND操作符不同，它指示MySQL检索匹配任一条件的行。

```mysql
mysql> select prod_id,prod_name,prod_price,vend_id from products where vend_id = 1003 or vend_id=1002 ;
+---------+----------------+------------+---------+
| prod_id | prod_name      | prod_price | vend_id |
+---------+----------------+------------+---------+
| DTNTR   | Detonator      |      13.00 |    1003 |
| FB      | Bird seed      |      10.00 |    1003 |
| FC      | Carrots        |       2.50 |    1003 |
| FU1     | Fuses          |       3.42 |    1002 |
| OL1     | Oil can        |       8.99 |    1002 |
| SAFE    | Safe           |      50.00 |    1003 |
| SLING   | Sling          |       4.49 |    1003 |
| TNT1    | TNT (1 stick)  |       2.50 |    1003 |
| TNT2    | TNT (5 sticks) |      10.00 |    1003 |
+---------+----------------+------------+---------+
9 rows in set (0.00 sec)
```

## 组合次序

> 组合优先级:
>
> and	>	or
>
> SQL（像多数语言一样）在处理OR操作符前，优先处理AND操作符。

假如需要列出价格为10美元（含）以上且由1002或1003制造的所有产品。

```mysql
mysql> select vend_id,prod_name ,prod_price from products where vend_id =1002 or vend_id =1003 and prod_price >=10;
+---------+----------------+------------+
| vend_id | prod_name      | prod_price |
+---------+----------------+------------+
|    1003 | Detonator      |      13.00 |
|    1003 | Bird seed      |      10.00 |
|    1002 | Fuses          |       3.42 |
|    1002 | Oil can        |       8.99 |
|    1003 | Safe           |      50.00 |
|    1003 | TNT (5 sticks) |      10.00 |
+---------+----------------+------------+
6 rows in set (0.00 sec)
```

> 只有1003的价格大于10

使用括号组合条件:

```MySQL
mysql> select vend_id,prod_name ,prod_price from products where (vend_id =1002 or vend_id =1003) and prod_price >=10;
+---------+----------------+------------+
| vend_id | prod_name      | prod_price |
+---------+----------------+------------+
|    1003 | Detonator      |      13.00 |
|    1003 | Bird seed      |      10.00 |
|    1003 | Safe           |      50.00 |
|    1003 | TNT (5 sticks) |      10.00 |
+---------+----------------+------------+
4 rows in set (0.00 sec)
```

## in操作符

IN操作符用来指定条件范围，范围中的每个条件都可以进行匹配。IN取合法值的由逗号分隔的清单，全都括在圆括号中。

```mysql
mysql> select vend_id,prod_name ,prod_price from products where vend_id in (1002,1003) order by prod_name;
+---------+----------------+------------+
| vend_id | prod_name      | prod_price |
+---------+----------------+------------+
|    1003 | Bird seed      |      10.00 |
|    1003 | Carrots        |       2.50 |
|    1003 | Detonator      |      13.00 |
|    1002 | Fuses          |       3.42 |
|    1002 | Oil can        |       8.99 |
|    1003 | Safe           |      50.00 |
|    1003 | Sling          |       4.49 |
|    1003 | TNT (1 stick)  |       2.50 |
|    1003 | TNT (5 sticks) |      10.00 |
+---------+----------------+------------+
9 rows in set (0.00 sec)
```

in的功能与where相当,其优点在于:

- 在使用长的合法选项清单时，IN操作符的语法更清楚且更直观。
- 在使用IN时，计算的次序更容易管理（因为使用的操作符更少）。 
-  IN操作符一般比OR操作符清单执行更快。
- IN的最大优点是可以包含其他SELECT语句，使得能够更动态地建立WHERE子句。

## not操作符

NOT  WHERE子句中用来否定后跟条件的关键字。

列出除1002和1003之外的所有供应商制造的产品

```mysql
mysql> select vend_id,prod_name ,prod_price from products where vend_id not in (1002,1003) order by prod_name;
+---------+--------------+------------+
| vend_id | prod_name    | prod_price |
+---------+--------------+------------+
|    1001 | .5 ton anvil |       5.99 |
|    1001 | 1 ton anvil  |       9.99 |
|    1001 | 2 ton anvil  |      14.99 |
|    1005 | JetPack 1000 |      35.00 |
|    1005 | JetPack 2000 |      55.00 |
+---------+--------------+------------+
5 rows in set (0.00 sec)
```

> MySQL支持使用NOT对IN、BETWEEN和EXISTS子句取反，这与多数其他DBMS允许使用NOT对各种条件取反有很大的差别。

## like操作符

通配符:

- '%'表示任何字符出现任意次数。(0,1,2,3......)
- '_'下划线只匹配单个字符而不是多个字符。

```mysql
mysql> select vend_id,prod_name ,prod_price from products where prod_name like '%anvil%';
+---------+--------------+------------+
| vend_id | prod_name    | prod_price |
+---------+--------------+------------+
|    1001 | .5 ton anvil |       5.99 |
|    1001 | 1 ton anvil  |       9.99 |
|    1001 | 2 ton anvil  |      14.99 |
+---------+--------------+------------+
3 rows in set (0.00 sec)
```

```mysql
mysql> select vend_id,prod_name ,prod_price from products where prod_name like '_ ton anvil';
+---------+-------------+------------+
| vend_id | prod_name   | prod_price |
+---------+-------------+------------+
|    1001 | 1 ton anvil |       9.99 |
|    1001 | 2 ton anvil |      14.99 |
+---------+-------------+------------+
2 rows in set (0.00 sec)
```

# 正则表达式

## like与正则表达式

​		LIKE 匹配整个列，如果被匹配的文本在列值中出现，LIKE 将不会找到它，相应的行也不会被返回（除非使用通配符）。而 REGEXP 在列值内进行匹配，如果被匹配的文本在列值中出现，REGEXP 将会找到它，相应的行将被返回，并且 REGEXP 能匹配整个列值（与 LIKE 相同的作用）。

>  MySQL中的正则表达式匹配（自版本3.23.4后）不区分大小写（即，大写和小写都匹配）。为区分大小写，可使用**BINARY**关键字，如WHERE prod_name REGEXP BINARY 'JetPack .000'。

```mysql
mysql> select vend_id,prod_name ,prod_price from products where prod_name like '1000';
Empty set (0.00 sec)

mysql> select vend_id,prod_name ,prod_price from products where prod_name regexp '1000';
+---------+--------------+------------+
| vend_id | prod_name    | prod_price |
+---------+--------------+------------+
|    1005 | JetPack 1000 |      35.00 |
+---------+--------------+------------+
1 row in set (0.00 sec)

mysql> select vend_id,prod_name ,prod_price from products where prod_name like 'jetpack 1000';
+---------+--------------+------------+
| vend_id | prod_name    | prod_price |
+---------+--------------+------------+
|    1005 | JetPack 1000 |      35.00 |
+---------+--------------+------------+
1 row in set (0.00 sec)
```

## or匹配

为搜索两个串之一（或者为这个串，或者为另一个串），使用 	**'|'**

```mysql
mysql> select prod_name from products where prod_name regexp '1000|2000';
+--------------+
| prod_name    |
+--------------+
| JetPack 1000 |
| JetPack 2000 |
+--------------+
2 rows in set (0.00 sec)
```

:bug::bug:之前乱加空格，导致出了点问题

```mysql
mysql> select prod_name from products where prod_name regexp '1000 | 2000';
+--------------+
| prod_name    |
+--------------+
| JetPack 2000 |
+--------------+
1 row in set (0.00 sec)
```

> :warning::warning:
>
> 仔细看			**regexp '1000 | 2000'**
>
> 1000后有一个空格，2000前有一个空格
>
> 对于最开始的结果`JetPack 1000`和`JetPack 2000`
>
> 由于`JetPack 1000`在1000后没有空格，所以并没有匹配上
> `JetPack 2000`在2000前存在空格，所以匹配上了



## 匹配几个字符之一

匹配任何单一字符,通过指定一组用**[和]**括起来的字符来完成

```mysql
mysql> select prod_name from products where prod_name regexp '[123] Ton';
+-------------+
| prod_name   |
+-------------+
| 1 ton anvil |
| 2 ton anvil |
+-------------+
2 rows in set (0.00 sec)
```

> 区别于 			**'1|2|3 Ton'**

```mysql
mysql> select prod_name from products where prod_name regexp '1|2|3 Ton';
+---------------+
| prod_name     |
+---------------+
| 1 ton anvil   |
| 2 ton anvil   |
| JetPack 1000  |
| JetPack 2000  |
| TNT (1 stick) |
+---------------+
5 rows in set (0.00 sec)
```

之所以这样是由于MySQL假定你的意思是'1'或'2'或'3 ton'。除非把字符|括在一个集合中，否则它将应用于整个串。

## 匹配范围

\[1-9]:	匹配数字0到9

\[a-z]:	匹配任意字母字符

```mysql
mysql> select prod_name from products where prod_name regexp '[1-5] Ton';
+--------------+
| prod_name    |
+--------------+
| .5 ton anvil |
| 1 ton anvil  |
| 2 ton anvil  |
+--------------+
3 rows in set (0.00 sec)
```

## 匹配特殊字符

为了匹配特殊字符，必须用	\\\	为前导。\\\\-	表示查找-，	\\\\.	表示查找.。

```mysql
mysql> select vend_name from vendors where vend_name regexp '\\.' order by vend_name;
+--------------+
| vend_name    |
+--------------+
| Furball Inc. |
+--------------+
1 row in set (0.00 sec)
```

## 匹配字符类

存在找出你自己经常使用的数字、所有字母字符或所有数字字母字符等的匹配。为更方便工作，可以使用预定义的字符集，称为字符类（character class）。

| 类         | 说明                                              |
| ---------- | ------------------------------------------------- |
| [:alnum:]  | 任意字母和数字（同[a-zA-Z0-9]）                   |
| [:alpha:]  | 任意字符（同[a-zA-Z]）                            |
| [:blank:]  | 空格和制表（同[\\t]）                             |
| [:cntrl:]  | ASCII控制字符（ASCII 0到31和127）                 |
| [:digit:]  | 任意数字（同[0-9]）                               |
| [:graph:]  | 与[:print:]相同，但不包括空格                     |
| [:lower:]  | 任意小写字母（同[a-z]）                           |
| [:print:]  | 任意可打印字符                                    |
| [:punct:]  | 既不在[:alnum:]又不在[:cntrl:]中的任意字符        |
| [:space:]  | 包括空格在内的任意空白字符（同[\\f\\n\\r\\t\\v]） |
| [:upper:]  | 任意大写字母（同[A-Z]）                           |
| [:xdigit:] | 任意十六进制数字（同[a-fA-F0-9]）                 |

## 匹配多个实例

| 元字符 | 说明                         |
| ------ | ---------------------------- |
| \*     | 0个或多个匹配                |
| \+     | 1个或多个匹配（等于{1,}）    |
| ?      | 0个或1个匹配（等于{0,1}）    |
| {n}    | 指定数目的匹配               |
| {n,}   | 不少于指定数目的匹配         |
| {n,m}  | 匹配数目的范围（m不超过255） |

## 定位符

| 元字符  | 说明       |
| ------- | ---------- |
| ^       | 文本的开始 |
| $       | 文本的结尾 |
| [[:<:]] | 词的开始   |
| [[:>:]] | 词的结尾   |

## 表格

> [(2条消息) MySQL 正则表达式（REGEXP）_Hern（宋兆恒）的博客-CSDN博客_mysql regexp](https://blog.csdn.net/qq_36761831/article/details/82862135)

<div class="table-box"><table><tbody><tr><th style="width:84px;">字符</th><th>其它信息</th></tr></tbody><tbody><tr><td style="width:84px;"><strong>[</strong>&nbsp;和&nbsp;<strong>]</strong></td><td> <p>左右方括号用于指定<strong>字符类</strong>。字符类是进行匹配时所要依据的一组字符。</p> <p>除连字符 (-) 和脱字符 (^) 外，在字符类中指定的元字符和量词（如 * 和 {m}，分别为元字符和量词）没有特殊意义，可当作实际字符进行运算。</p> </td></tr><tr><td style="width:84px;"><strong>*</strong></td><td>星号可用于与字符匹配 0 次或多次。例如，<code>REGEXP '.*abc'</code>&nbsp;匹配的字符串以 abc 结尾并以任何前缀开头。因此，aabc、xyzabc 和 abc 匹配，但 bc 和 abcc 则不匹配。</td></tr><tr><td style="width:84px;"><strong>?</strong></td><td>问号可用于与字符匹配 0 次或 1 次。例如，<code>'colou?r'</code>&nbsp;匹配 color 和 colour。</td></tr><tr><td style="width:84px;"><strong>+</strong></td><td>加号可用于与字符匹配 1 次或多次。例如，<code>'bre+'</code>&nbsp;匹配 bre 和 bree，但不匹配 br。</td></tr><tr><td style="width:84px;"><strong>-</strong></td><td> <p>可以在字符类中使用一个连字符来表示一个范围。例如，<code>REGEXP '[a-e]'</code>&nbsp;匹配 a、b、c、d 和 e。</p> </td></tr><tr><td style="width:84px;"><strong>%</strong></td><td> <p>百分号可与 SIMILAR TO 配合使用来匹配任意数目的字符。</p> <p>不将百分号视为 REGEXP 和 REGEXP_SUBSTR 所使用的元字符。当指定时，它匹配百分号 (%)。</p> </td></tr><tr><td style="width:84px;"><strong>_</strong>（下划线字符）</td><td> <p>可将下划线与 SIMILAR TO 配合使用来匹配单个字符。</p> <p>不将下划线视为 REGEXP 和 REGEXP_SUBSTR 所使用的元字符。当指定时，它匹配下划线 (_)。</p> </td></tr><tr><td style="width:84px;"><strong>|</strong></td><td> <p>管道符号用于指定匹配字符串时要使用的替代模式。在由竖线分隔的一行模式中，竖线被解释为 OR，匹配过程从最左侧的模式开始，在找到第一个匹配项时停止。因此，您应按优先级的降序顺序列出模式。您可以指定任意数量的替代模式。</p> </td></tr><tr><td style="width:84px;"><strong>(</strong>&nbsp;和&nbsp;<strong>)</strong></td><td>当左括号和右括号用于正则表达式的各个组合部分时，它们为元字符。例如，<code>(ab)*</code>&nbsp;匹配零个或多个 ab 的重复项。与使用数学表达式一样，您使用组合来控制正则表达式各部分的计算顺序。</td></tr><tr><td style="width:84px;"><strong>{<!-- --></strong>&nbsp;和&nbsp;<strong>}</strong></td><td> <p>当左大括号和右大括号用于指定<strong>量词</strong>时，它们为元字符。量词指定一个模式要构成某个匹配所必须重复的次数。例如：</p> 
    <ul><li> <p><strong>{<!-- --><em>m</em>}&nbsp;&nbsp;&nbsp;</strong>匹配某个字符正好&nbsp;<em>m</em>&nbsp;次。例如，<code>'519-[0-9]{3}-[0-9]{4}'</code>&nbsp;匹配 519 地区号中的一个电话号码（假定数据按语法中定义的方式进行格式设置）。</p> </li><li> <p><strong>{<!-- --><em>m</em>,}&nbsp;&nbsp;&nbsp;</strong>匹配某个字符至少&nbsp;<em>m</em>&nbsp;次。例如，<code>'[0-9]{5,}'</code>&nbsp;匹配任何含有五个或更多数字的字符串。</p> </li><li> <p><strong>{<!-- --><em>m</em>,<em>n</em>}&nbsp;&nbsp;&nbsp;</strong>匹配某个字符至少&nbsp;<em>m</em>&nbsp;次，但不超过&nbsp;<em>n</em>&nbsp;次。例如，<code>SIMILAR TO '_{5,10}'</code>&nbsp;匹配任何含有 5 到 10（含 5 和 10）个字符的字符串。</p> </li></ul></td></tr><tr><td style="width:84px;"><strong>\</strong></td><td>反斜线被用作元字符的转义字符。它也可被用于转义非元字符。</td></tr><tr><td style="width:84px;"><strong>^</strong></td><td>匹配输入字符串的开始位置。如果设置了 RegExp 对象的 Multiline 属性，^ 也匹配 '\n' 或 '\r' 之后的位置。</td></tr><tr><td style="width:84px;"><strong>$</strong></td><td>匹配输入字符串的结束位置。如果设置了RegExp 对象的 Multiline 属性，$ 也匹配 '\n' 或 '\r' 之前的位置。</td></tr><tr><td style="width:84px;"><strong>.</strong></td><td>匹配除 "\n" 之外的任何单个字符。要匹配包括 '\n' 在内的任何字符，请使用象 '[.\n]' 的模式。</td></tr><tr><td style="width:84px;"><strong>:</strong></td><td> <p>在字符集中使用冒号来指定子字符类。例如，<code>'[[:alnum:]]'</code>。</p> </td></tr></tbody></table></div>

# 计算字段

存储在数据库表中的数据一般不是应用程序所需要的格式。我们需要直接从数据库中检索出转换、计算或格式化过的数据；而不是检索出数据，然后再在客户机应用程序或报告程序中重新格式化。

> 可在SQL语句内完成的许多转换和格式化工作都可以直接在客户机应用程序内完成。但一般来说，在数据库服务器上完成这些操作比在客户机中完成要快得多，因为DBMS是设计来快速有效地完成这种处理的。

## 拼接字段--concat

```mysql
mysql> select concat(vend_name,'(',vend_country,')')
    -> from vendors
    -> order by vend_name;
+----------------------------------------+
| concat(vend_name,'(',vend_country,')') |
+----------------------------------------+
| ACME(USA)                              |
| Anvils R Us(USA)                       |
| Furball Inc.(USA)                      |
| Jet Set(England)                       |
| Jouets Et Ours(France)                 |
| LT Supplies(USA)                       |
+----------------------------------------+
6 rows in set (0.00 sec)
```

## 使用别名

SQL支持列别名。别名（alias）是一个字段或值的替换名。别名用AS关键字赋予。

```mysql
mysql> select concat(vend_name,'(',vend_country,')') as vend_title from vendors order by vend_name;
+------------------------+
| vend_title             |
+------------------------+
| ACME(USA)              |
| Anvils R Us(USA)       |
| Furball Inc.(USA)      |
| Jet Set(England)       |
| Jouets Et Ours(France) |
| LT Supplies(USA)       |
+------------------------+
6 rows in set (0.00 sec)
```

## 计算

​	汇总物品的价格（单价乘以订购数量）：

```mysql
mysql> select prod_id ,quantity,item_price, quantity*item_price as expanded_price from orderitems where order_num = 20005;
+---------+----------+------------+----------------+
| prod_id | quantity | item_price | expanded_price |
+---------+----------+------------+----------------+
| ANV01   |       10 |       5.99 |          59.90 |
| ANV02   |        3 |       9.99 |          29.97 |
| TNT2    |        5 |      10.00 |          50.00 |
| FB      |        1 |      10.00 |          10.00 |
+---------+----------+------------+----------------+
4 rows in set (0.00 sec)
```

# 函数

## 文本处理函数

| 函数        | 说明              |
| ----------- | ----------------- |
| left()      | 返回串左边的字符  |
| length()    | 返回串的长度      |
| locate()    | 找出串的一个子串  |
| lower()     | 将串转换为小写    |
| ltrim()     | 去掉串左边的空格  |
| right()     | 返回串右边的字符  |
| rtrim()     | 去掉串右边的空格  |
| soundex()   | 返回串的SOUNDEX值 |
| subString() | 返回子串的字符    |
| upper()     | 将串转换为大写    |

​		SOUNDEX是一个将任何文本串转换为描述其语音表示的字母数字模式的算法。SOUNDEX考虑了类似的发音字符和音节，使得能对串进行发音比较而不是字母比较。

​		customers表中有一个顾客Coyote Inc.，其联系名为Y.Lee。但如果这是输入错误，此联系名实际应该是Y.Lie

```mysql
mysql> select cust_name,cust_contact from customers where cust_contact = 'Y.Lie';
Empty set (0.00 sec)

mysql> select cust_name,cust_contact from customers where Soundex(cust_contact) = Soundex('Y.Lie');
+-------------+--------------+
| cust_name   | cust_contact |
+-------------+--------------+
| Coyote Inc. | Y Lee        |
+-------------+--------------+
1 row in set (0.00 sec)
```

​		WHERE子句使用Soundex()函数来转换cust_ contact列值和搜索串为它们的SOUNDEX值。因为Y.Lee和Y.Lie发音相似，所以它们的SOUNDEX值匹配，因此WHERE子句正确地过滤出了所需的数据。

## 日期时间处理函数

​		首先需要注意的是MySQL使用的日期格式。无论你什么时候指定一个日期，不管是插入或更新表值还是用WHERE子句进行过滤，日期必须为格式yyyy-mm-dd。

![image-20221226164325820](D:/notes/3150/image-20221226164325820.png)

> :warning:对于具有时间值00:00:00的日期，比较的时候应使用Date（）函数提取日期部分比较。
>
>  比 如 ， 存 储 的 order_date 值 为2005-09-01 11:30:05，则WHERE order_date = '2005-09-01'失败。即使给出具有该日期的一行，也不会把它检索出来，因为WHERE匹配失败。

```mysql
mysql> select cust_id,order_num,order_date from orders where order_date = '2022-12-26';
Empty set (0.00 sec)

mysql> select cust_id,order_num,order_date from orders where Date(order_date) = '2022-12-26';
+---------+-----------+---------------------+
| cust_id | order_num | order_date          |
+---------+-----------+---------------------+
|   10001 |     20010 | 2022-12-26 16:50:54 |
+---------+-----------+---------------------+
1 row in set (0.00 sec)
```

> :mouse::mouse:看日期也知道，这条数据是我自己插入的。嗯。。。

## 数值处理函数

![image-20221226165350957](D:/notes/3150/image-20221226165350957.png)

## 聚集函数

![image-20221226170031942](D:/notes/3150/image-20221226170031942.png)

- avg(),max(),min(),sum()函数忽略列值为NULL的行。
- 如果指定列名，则指定列的值为空的行被COUNT()函数忽略，但如果COUNT()函数中用的是星号（*），则不忽略。

```mysql
mysql> select count(*) from customers;
+----------+
| count(*) |
+----------+
|        5 |
+----------+
1 row in set (0.00 sec)

mysql> select count(cust_email) from customers;
+-------------------+
| count(cust_email) |
+-------------------+
|                 3 |
+-------------------+
1 row in set (0.00 sec)
```

## 聚集函数+distinct

- 对于聚集函数来说,all是默认参数(全部数据都会参与计算,包含重复值)
- 可使用distint去重

```mysql
mysql> select avg(prod_price) from products where vend_id =1003;
+-----------------+
| avg(prod_price) |
+-----------------+
|       13.212857 |
+-----------------+
1 row in set (0.00 sec)
#使用distinct后平均值变高了,去除了重复的较低价格
mysql> select avg(distinct prod_price) from products where vend_id =1003;
+--------------------------+
| avg(distinct prod_price) |
+--------------------------+
|                15.998000 |
+--------------------------+
1 row in set (0.00 sec)
```

- DISTINCT只能用于COUNT()。DISTINCT不能用于COUNT(*)
- DISTINCT必须使用列名，不能用于计算或表达式

# 分组

## 创建分组

```mysql
mysql> select vend_id,count(*) from products group by vend_id;
+---------+----------+
| vend_id | count(*) |
+---------+----------+
|    1001 |        3 |
|    1002 |        2 |
|    1003 |        7 |
|    1005 |        2 |
+---------+----------+
4 rows in set (0.01 sec)
```

GROUP BY子句指示MySQL分组数据，然后对每个组而不是整个结果集进行聚集。

:warning::warning::warning:

- 如果在GROUP BY子句中嵌套了分组，数据将在最后规定的分组上进行汇总。换句话说，在建立分组时，指定的所有列都一起计算（所以不能从个别的列取回数据）。
- GROUP BY子句中列出的每个列都必须是检索列或有效的表达式（但**不能是聚集函数**）。如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式。不能使用别名。
- 除聚集计算语句外，SELECT语句中的每个列都必须在GROUP BY子句中给出。
- 如果分组列中具有NULL值，则NULL将作为一个分组返回。如果列中有多行NULL值，它们将分为一组。
- GROUP BY子句必须出现在WHERE子句之后，ORDER BY子句之前。

**WITH ROLLUP**：在group分组字段的基础上再进行统计数据。

```mysql
mysql> select vend_id,count(*) from products group by vend_id with rollup;
+---------+----------+
| vend_id | count(*) |
+---------+----------+
|    1001 |        3 |
|    1002 |        2 |
|    1003 |        7 |
|    1005 |        2 |
|    NULL |       14 |
+---------+----------+
5 rows in set (0.01 sec)
```

> 统计count（*），最后一行的值  14=3+2+7+2
>
> [Mysql中的WITH ROLLUP用法 - 周伯通之草堂 - 博客园 (cnblogs.com)](https://www.cnblogs.com/phpper/p/9384614.html#:~:text=Mysql中的WITH ROLLUP用法 1.WITH,ROLLUP ：在group分组字段的基础上再进行统计数据。 例子：首先在name字段上进行分组，然后在分组的基础上进行某些字段统计，表结构如下：)

## 过滤分组

MySQL提供了另外的子句，那就是HAVING子句。HAVING非常类似于WHERE。

```mysql
mysql> select cust_id ,count(*) orders from orders group by cust_id having count(*)>2;
+---------+--------+
| cust_id | orders |
+---------+--------+
|   10001 |      3 |
+---------+--------+
1 row in set (0.00 sec)
```

> **having和where区别**
>
> WHERE在数据分组前进行过滤，HAVING在数据分组后进行过滤。这是一个重要的区别，WHERE排除的行不包括在分组中。这可能会改变计算值，从而影响HAVING子句中基于这些值过滤掉的分组。

列出具有2个（含）以上、价格为10（含）以上的产品的供应商：

```mysql
mysql> select vend_id,count(*) from products where prod_price>=10 group by vend_id having count(*) >=2;
+---------+----------+
| vend_id | count(*) |
+---------+----------+
|    1003 |        4 |
|    1005 |        2 |
+---------+----------+
2 rows in set (0.00 sec)
```

## 分组和排序

一般在使用GROUP BY子句时，应该也给出ORDER BY子句。这是保证数据正确排序的唯一方法。千万不要仅依赖GROUP BY排序数据。

```mysql
mysql> select order_num,sum(quantity*item_price) as ordertotal from orderite
ms group by order_num having ordertotal >=50;
+-----------+------------+
| order_num | ordertotal |
+-----------+------------+
|     20005 |     149.87 |
|     20006 |      55.00 |
|     20007 |    1000.00 |
|     20008 |     125.00 |
+-----------+------------+
4 rows in set (0.00 sec)

mysql> select order_num,sum(quantity*item_price) as ordertotal from orderitems group by order_num having ordertotal >=50 order by ordertotal;
+-----------+------------+
| order_num | ordertotal |
+-----------+------------+
|     20006 |      55.00 |
|     20008 |     125.00 |
|     20005 |     149.87 |
|     20007 |    1000.00 |
+-----------+------------+
4 rows in set (0.00 sec)
```

## select语句顺序

| 子句     | 说明               | 是否必须使用           |
| -------- | ------------------ | ---------------------- |
| SELECT   | 要返回的列或表达式 | 是                     |
| FROM     | 从中检索数据的表   | 仅在从表选择数据时使用 |
| WHERE    | 行级过滤           | 否                     |
| GROUP BY | 分组说明           | 仅在按组计算聚集时使用 |
| HAVING   | 组级过滤           | 否                     |
| ORDER BY | 输出排序顺序       | 否                     |
| LIMIT    | 要检索的行数       | 否                     |

# 子查询

## 子查询过滤

假如需要列出订购物品TNT2的所有客户：

- 检索包含物品TNT2的所有订单的编号。

  `select order_num from orderitems where prod_id = 'tnt2'`

- 检索具有前一步骤列出的订单编号的所有客户的ID。

  `select cust_id from orders where order_num in 。。。`

- 检索前一步骤返回的所有客户ID的客户信息。

  `select cust_name ,cust_contact from customers where cust_id in。。。。。`

```mysql
mysql> select cust_name ,cust_contact from customers where cust_id in 
	(select cust_id from orders where order_num in 
     	(select order_num from orderitems where prod_id = 'tnt2'));
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.01 sec)
```

> **列必须匹配**
>
> 在WHERE子句中使用子查询（如这里所示），应该保证SELECT语句具有与WHERE子句中相同数目的列。通常，子查询将返回单个列并且与单个列匹配，但如果需要也可以使用多个列。

## 作为计算字段

假如需要显示customers表中每个客户的订单总数。订单与相应的客户ID存储在orders表中。

- 从customers表中检索客户列表。

  ` select....from customers order by cust_name` 

- 对于检索出的每个客户，统计其在orders表中的订单数目。

  `select count(*) from orders where orders.cust_id =customers.cust_id`

```mysql
mysql> select cust_name,cust_state,
	(select count(*) 
     	from orders 
     	where orders.cust_id =customers.cust_id) as orders 
	from customers 
	order by cust_name;
+----------------+------------+--------+
| cust_name      | cust_state | orders |
+----------------+------------+--------+
| Coyote Inc.    | MI         |      3 |
| E Fudd         | IL         |      1 |
| Mouse House    | OH         |      0 |
| Wascals        | IN         |      1 |
| Yosemite Place | AZ         |      1 |
+----------------+------------+--------+
5 rows in set (0.00 sec)
```

这 条 SELECT 语句对 customers 表中每个客户返回 3 列 ：cust_name、cust_state和orders。orders是一个计算字段，它是由圆括号中的子查询建立的。该子查询对检索出的每个客户执行一次。在此例子中，该子查询执行了5次，因为检索出了5个客户。

`where orders.cust_id =customers.cust_id`

`orders.cust_id`是内部字段，`customers.cust_id`是外部字段，由外部传入。**不加限定名会无法区分**

相关子查询：

> [(2条消息) 【MySql】相关子查询_Mercy92的博客-CSDN博客_相关子查询](https://blog.csdn.net/weixin_40844116/article/details/92759433)

- 从外层查询中取出一个[元组](https://so.csdn.net/so/search?q=元组&spm=1001.2101.3001.7020)，将元组相关列的值传给内层查询。
- 执行内层查询，得到子查询操作的值。
- 外查询根据子查询返回的结果或结果集得到满足条件的行。
- 然后外层查询取出下一个元组重复做步骤1-3，直到外层的元组全部处理完毕。

# 联结表

联结表的目的是为了用一条select语句检索多张表的数据。

联结的关键在于**主键**和**外键**。主键唯一标识一行数据，外键则是另一张表的主键。（包含外键的表称为从表）

products表只存储产品信息，它除了存储供应商ID（vendors表的主键）外不存储其他供应商信息。vendors表的主键又叫作products的外键，它将vendors表与products表关联。（products是从表，包含外键；vendors是主表）

```mysql
mysql> select vend_name,prod_name,prod_price
    -> from vendors,products
    ->  where vendors.vend_id = products.vend_id
    -> order by vend_name,prod_name;
+-------------+----------------+------------+
| vend_name   | prod_name      | prod_price |
+-------------+----------------+------------+
| ACME        | Bird seed      |      10.00 |
| ACME        | Carrots        |       2.50 |
| ACME        | Detonator      |      13.00 |
| ACME        | Safe           |      50.00 |
| ACME        | Sling          |       4.49 |
| ACME        | TNT (1 stick)  |       2.50 |
| ACME        | TNT (5 sticks) |      10.00 |
| Anvils R Us | .5 ton anvil   |       5.99 |
| Anvils R Us | 1 ton anvil    |       9.99 |
| Anvils R Us | 2 ton anvil    |      14.99 |
| Jet Set     | JetPack 1000   |      35.00 |
| Jet Set     | JetPack 2000   |      55.00 |
| LT Supplies | Fuses          |       3.42 |
| LT Supplies | Oil can        |       8.99 |
+-------------+----------------+------------+
14 rows in set (0.01 sec)
```

> 两张表：vendors,products
>
> 不同的列：**vend_name 	**和	 **prod_name,prod_price**
>
> 完全限定列名：vendors.vend_id = products.vend_id
> 区分两张表的相同字段vend_id

## where条件过滤

WHERE子句作为过滤条件，它只包含那些匹配给定条件（这里是联结条件）的行。没有WHERE子句，第一个表中的每个行将与第二个表中的每个行配对，而不管它们逻辑上是否可以配在一起。不使用where条件过滤最终结果会采用笛卡尔积，其数据并不是我们想要的结果。

## 笛卡尔积

集合的乘积

![image-20221227105429854](D:/notes/3150/image-20221227105429854.png)

对于最终的结果应该是3*3 =9 种组合

```mysql
mysql> select count(*) from vendors,products;
+----------+
| count(*) |
+----------+
|       84 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from vendors;
+----------+
| count(*) |
+----------+
|        6 |
+----------+
1 row in set (0.00 sec)

mysql> select count(*) from products;
+----------+
| count(*) |
+----------+
|       14 |
+----------+
1 row in set (0.00 sec)
```

> 结果计算：84=6*14

## 内连接

目前为止所用的联结称为等值联结（equijoin），它基于两个表之间的相等测试。这种联结也称为内部联结。

不同写法，一样的效果：

```mysql
mysql> select vend_name,prod_name,prod_price from vendors inner join products on vendors.vend_id = products.vend_id;
+-------------+----------------+------------+
| vend_name   | prod_name      | prod_price |
+-------------+----------------+------------+
| Anvils R Us | .5 ton anvil   |       5.99 |
| Anvils R Us | 1 ton anvil    |       9.99 |
| Anvils R Us | 2 ton anvil    |      14.99 |
| LT Supplies | Fuses          |       3.42 |
| LT Supplies | Oil can        |       8.99 |
| ACME        | Detonator      |      13.00 |
| ACME        | Bird seed      |      10.00 |
| ACME        | Carrots        |       2.50 |
| ACME        | Safe           |      50.00 |
| ACME        | Sling          |       4.49 |
| ACME        | TNT (1 stick)  |       2.50 |
| ACME        | TNT (5 sticks) |      10.00 |
| Jet Set     | JetPack 1000   |      35.00 |
| Jet Set     | JetPack 2000   |      55.00 |
+-------------+----------------+------------+
14 rows in set (0.00 sec)

mysql> select vend_name,prod_name,prod_price from vendors , products where vendors.vend_id = products.vend_id;
+-------------+----------------+------------+
| vend_name   | prod_name      | prod_price |
+-------------+----------------+------------+
| Anvils R Us | .5 ton anvil   |       5.99 |
| Anvils R Us | 1 ton anvil    |       9.99 |
| Anvils R Us | 2 ton anvil    |      14.99 |
| LT Supplies | Fuses          |       3.42 |
| LT Supplies | Oil can        |       8.99 |
| ACME        | Detonator      |      13.00 |
| ACME        | Bird seed      |      10.00 |
| ACME        | Carrots        |       2.50 |
| ACME        | Safe           |      50.00 |
| ACME        | Sling          |       4.49 |
| ACME        | TNT (1 stick)  |       2.50 |
| ACME        | TNT (5 sticks) |      10.00 |
| Jet Set     | JetPack 1000   |      35.00 |
| Jet Set     | JetPack 2000   |      55.00 |
+-------------+----------------+------------+
14 rows in set (0.00 sec)

mysql>
```

> 小道消息：:postbox:
> [(2条消息) 连接查询（等值连接和内连接）_weixin_30858241的博客-CSDN博客](https://blog.csdn.net/weixin_30858241/article/details/97830364)
>
> 等值连接：2个表会先进行笛卡尔乘积运算，生成一个新表格，占据在电脑内存里，当表的数据量很大时，很耗内存，这种方法效率比较低，尽量不用。
>
> 内连接：2个表根据共同ID进行逐条匹配，不会出现笛卡尔乘积的现象，效率比较高，优先使用这种方法。

## 多表联结

SQL对一条SELECT语句中可以联结的表的数目没有限制。所以，继续往后加就行。:maple_leaf:

```mysql
mysql> select prod_name,vend_name,prod_price,quantity
    -> from orderitems,products,vendors
    -> where products.vend_id = vendors.vend_id
    -> and orderitems.prod_id = products.prod_id
    -> and order_num=20005;
+----------------+-------------+------------+----------+
| prod_name      | vend_name   | prod_price | quantity |
+----------------+-------------+------------+----------+
| .5 ton anvil   | Anvils R Us |       5.99 |       10 |
| 1 ton anvil    | Anvils R Us |       9.99 |        3 |
| TNT (5 sticks) | ACME        |      10.00 |        5 |
| Bird seed      | ACME        |      10.00 |        1 |
+----------------+-------------+------------+----------+
4 rows in set (0.01 sec)
```

子查询例子：

假如需要列出订购物品TNT2的所有客户：

- 检索包含物品TNT2的所有订单的编号。

  `select order_num from orderitems where prod_id = 'tnt2'`

- 检索具有前一步骤列出的订单编号的所有客户的ID。

  `select cust_id from orders where order_num in 。。。`

- 检索前一步骤返回的所有客户ID的客户信息。

  `select cust_name ,cust_contact from customers where cust_id in。。。。。`

```mysql
mysql> select cust_name ,cust_contact from customers where cust_id in 
	(select cust_id from orders where order_num in 
     	(select order_num from orderitems where prod_id = 'tnt2'));
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.01 sec)
```

改写：

```mysql
mysql> select cust_name,cust_contact
    -> from customers,orders,orderitems
    -> where customers.cust_id = orders.cust_id
    -> and orderitems.order_num = orders.order_num
    -> and prod_id = 'tnt2';
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.00 sec)
```

> 似乎多表联结更加无脑
>
> 找出相关表，无脑跟在from后面，无脑联结
>
> where后面跟上联结条件（匹配相关外键与主键）以及过滤条件

# 高级联结

## 别名使用

```mysql
mysql> select cust_name,cust_contact
    -> from customers as cus ,orders as o,orderitems as oi
    -> where cus.cust_id = o.cust_id
    -> and oi.order_num = o.order_num
    -> and prod_id = 'tnt2';
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.00 sec)

# 当然，as可省略
mysql> select cust_name,cust_contact
    -> from customers  cus ,orders  o,orderitems  oi
    -> where cus.cust_id = o.cust_id
    -> and oi.order_num = o.order_num
    -> and prod_id = 'tnt2';
+----------------+--------------+
| cust_name      | cust_contact |
+----------------+--------------+
| Coyote Inc.    | Y Lee        |
| Yosemite Place | Y Sam        |
+----------------+--------------+
2 rows in set (0.00 sec)
```

> :mega:表别名只在查询执行中使用。与列别名不一样，表别名不返回到客户机。

## 自联结

也就是自己和自己做关联

假如你发现某物品（其ID为DTNTR）存在问题，因此想知道生产该物品的供应商生产的其他物品是否也存在这些问题。此查询要求首先找到生产ID为DTNTR的物品的供应商，然后找出这个供应商生产的其他物品。

等值连接：

- 联结两张products表，起名为p1  p2
- 组合过滤条件，`p2.vend_id = p1.vend_id`联结两张表，`p2.prod_id = 'dtntr'`过滤数据

```mysql
mysql> select p1.prod_id ,p1.prod_name
    -> from products p1,products p2
    -> where p2.prod_id = 'dtntr'
    -> and p2.vend_id = p1.vend_id;
+---------+----------------+
| prod_id | prod_name      |
+---------+----------------+
| DTNTR   | Detonator      |
| FB      | Bird seed      |
| FC      | Carrots        |
| SAFE    | Safe           |
| SLING   | Sling          |
| TNT1    | TNT (1 stick)  |
| TNT2    | TNT (5 sticks) |
+---------+----------------+
7 rows in set (0.00 sec)
```

子查询:

- 查询id为DTNTR物品的供应商的vend_id

  `select vend_id  from products  where prod_id ='dtntr'`

- 检索该供应商的所有物品

  `select prod_id,prod_name from products where vend_id =`

```mysql
mysql> select prod_id,prod_name
    -> from products
    -> where vend_id =(select vend_id
    -> from products
    -> where prod_id ='dtntr');
+---------+----------------+
| prod_id | prod_name      |
+---------+----------------+
| DTNTR   | Detonator      |
| FB      | Bird seed      |
| FC      | Carrots        |
| SAFE    | Safe           |
| SLING   | Sling          |
| TNT1    | TNT (1 stick)  |
| TNT2    | TNT (5 sticks) |
+---------+----------------+
7 rows in set (0.01 sec)
```

## 自然联结

自然连接在等值连接中去除重复的属性列。（无重复列）

迄今为止我们建立的每个内部联结都是自然联结，很可能我们永远都不会用到不是自然联结的内部联结。

## 外部联结

许多联结将一个表中的行与另一个表中的行相关联。但有时候会需要包含没有关联行的那些行。

内部联结展示已关联的元组，外部联结可展示没有关联的元组。

检索所有客户及其订单：

```mysql
mysql> select cus.cust_id ,o.order_num from customers cus inner join orders
o on cus.cust_id = o.cust_id;
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10001 |     20010 |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
6 rows in set (0.00 sec)
```

检索所有客户，包括那些没有订单的客户：

```mysql
mysql> select cus.cust_id ,o.order_num from customers cus left join orders o on cus.cust_id = o.cust_id;
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10001 |     20010 |
|   10002 |      NULL |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
7 rows in set (0.00 sec)

mysql>
```

外部联结分为左外联结`left (outer) join`和右外联结`right (outer) join`。

它们之间的唯一差别是所关联的表的顺序不同。换句话说，左外部联结可通过颠倒FROM或WHERE子句中表的顺序转换为右外部联结。

```mysql
mysql> select cus.cust_id ,o.order_num from orders o right outer join customers cus on cus.cust_id = o.cust_id;
+---------+-----------+
| cust_id | order_num |
+---------+-----------+
|   10001 |     20005 |
|   10001 |     20009 |
|   10001 |     20010 |
|   10002 |      NULL |
|   10003 |     20006 |
|   10004 |     20007 |
|   10005 |     20008 |
+---------+-----------+
7 rows in set (0.00 sec)
```

## 聚集函数

聚集函数也可以方便地与其他联结一起使用

```mysql
mysql> select cus.cust_name,cus.cust_id,count(o.order_num) num_ord
    -> from customers cus left join orders o
    -> on cus.cust_id = o.cust_id
    -> group by cus.cust_id;
+----------------+---------+---------+
| cust_name      | cust_id | num_ord |
+----------------+---------+---------+
| Coyote Inc.    |   10001 |       3 |
| Mouse House    |   10002 |       0 |
| Wascals        |   10003 |       1 |
| Yosemite Place |   10004 |       1 |
| E Fudd         |   10005 |       1 |
+----------------+---------+---------+
5 rows in set (0.00 sec)
```

# 组合查询

MySQL也允许执行多个查询（多条SELECT语句），并将结果作为单个查询结果集返回。

- 在单个查询中从不同的表返回类似结构的数据；
- 对单个表执行多个查询，按单个查询返回数据。

> **组合查询和多个WHERE条件**
>
> 任何具有多个WHERE子句的SELECT语句都可以作为一个组合查询给出。

## union

UNION的使用很简单。所需做的只是给出每条SELECT语句，在各条语句之间放上关键字UNION。

假如需要价格小于等于5的所有物品的一个列表，而且还想包括供应商1001和1002生产的所有物品（不考虑价格）。

不使用union：

```mysql
mysql> select vend_id,prod_id ,prod_price
    -> from products
    -> where prod_price <=5
    -> or vend_id in (1001,1002);
+---------+---------+------------+
| vend_id | prod_id | prod_price |
+---------+---------+------------+
|    1001 | ANV01   |       5.99 |
|    1001 | ANV02   |       9.99 |
|    1001 | ANV03   |      14.99 |
|    1003 | FC      |       2.50 |
|    1002 | FU1     |       3.42 |
|    1002 | OL1     |       8.99 |
|    1003 | SLING   |       4.49 |
|    1003 | TNT1    |       2.50 |
+---------+---------+------------+
8 rows in set (0.00 sec)
```

使用union：

```mysql
mysql> select vend_id ,prod_id,prod_price
    -> from products
    -> where prod_price <=5
    -> union
    -> select vend_id ,prod_id ,prod_price
    -> from products
    -> where vend_id in(1001,1002);
+---------+---------+------------+
| vend_id | prod_id | prod_price |
+---------+---------+------------+
|    1003 | FC      |       2.50 |
|    1002 | FU1     |       3.42 |
|    1003 | SLING   |       4.49 |
|    1003 | TNT1    |       2.50 |
|    1001 | ANV01   |       5.99 |
|    1001 | ANV02   |       9.99 |
|    1001 | ANV03   |      14.99 |
|    1002 | OL1     |       8.99 |
+---------+---------+------------+
8 rows in set (0.00 sec)
```

> 在这个简单的例子中，使用UNION可能比使用WHERE子句更为复杂。但对于更复杂的过滤条件，或者从多个表（而不是单个表）中检索数据的情形，使用UNION可能会使处理更简单。

## union规则

- UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔（因此，如果组合4条SELECT语句，将要使用3个UNION关键字）。
- UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过各个列不需要以相同的次序列出）。
- 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）。

## 取消重复行

UNION从查询结果集中自动去除了重复的行（换句话说，它的行为与单条SELECT语句中使用多个WHERE子句条件一样）。

```mysql
mysql> select vend_id,prod_id,prod_price from products where prod_price<=5;
+---------+---------+------------+
| vend_id | prod_id | prod_price |
+---------+---------+------------+
|    1003 | FC      |       2.50 |
|    1002 | FU1     |       3.42 |
|    1003 | SLING   |       4.49 |
|    1003 | TNT1    |       2.50 |
+---------+---------+------------+
4 rows in set (0.00 sec)

mysql> select vend_id,prod_id,prod_price from products where vend_id in (1001,1002);
+---------+---------+------------+
| vend_id | prod_id | prod_price |
+---------+---------+------------+
|    1001 | ANV01   |       5.99 |
|    1001 | ANV02   |       9.99 |
|    1001 | ANV03   |      14.99 |
|    1002 | FU1     |       3.42 |
|    1002 | OL1     |       8.99 |
+---------+---------+------------+
5 rows in set (0.00 sec)

mysql>
```

分条执行的时候，价格小于等于5的有4条；商品号为(1001,1002)的有4条，使用union后却只有8条？

UNION从查询结果集中自动去除了重复的行（换句话说，它的行为与单条SELECT语句中使用多个WHERE子句条件一样）。因为供应商1002生产的一种物品的价格也低于5，所以两条SELECT语句都返回该行。在使用UNION时，重复的行被自动取消。

使用`union all`可查看所有匹配行。

```mysql
mysql>  select vend_id ,prod_id,prod_price
    -> from products
    -> where prod_price <=5
    ->  union all
    ->  select vend_id ,prod_id ,prod_price
    -> from products
    -> where vend_id in(1001,1002);
+---------+---------+------------+
| vend_id | prod_id | prod_price |
+---------+---------+------------+
|    1003 | FC      |       2.50 |
|    1002 | FU1     |       3.42 |
|    1003 | SLING   |       4.49 |
|    1003 | TNT1    |       2.50 |
|    1001 | ANV01   |       5.99 |
|    1001 | ANV02   |       9.99 |
|    1001 | ANV03   |      14.99 |
|    1002 | FU1     |       3.42 |
|    1002 | OL1     |       8.99 |
+---------+---------+------------+
9 rows in set (0.00 sec)
```

> UNION ALL为UNION的一种形式，它完成WHERE子句完成不了的工作。如果确实需要每个条件的匹配行全部出现（包括重复行），则必须使用UNION ALL而不WHERE。

## 排序

在用UNION组合查询时，只能使用一条ORDER BY子句，它必须出现在最后一条SELECT语句之后。

```mysql
mysql>  select vend_id ,prod_id,prod_price
    -> from products
    -> where prod_price <=5
    ->  union
    ->  select vend_id ,prod_id ,prod_price
    -> from products
    -> where vend_id in(1001,1002)
    -> order by vend_id,prod_price;
+---------+---------+------------+
| vend_id | prod_id | prod_price |
+---------+---------+------------+
|    1001 | ANV01   |       5.99 |
|    1001 | ANV02   |       9.99 |
|    1001 | ANV03   |      14.99 |
|    1002 | FU1     |       3.42 |
|    1002 | OL1     |       8.99 |
|    1003 | FC      |       2.50 |
|    1003 | TNT1    |       2.50 |
|    1003 | SLING   |       4.49 |
+---------+---------+------------+
8 rows in set (0.00 sec)
```

# 全文本搜索

> ps::boom:这一块强烈建议看书第18章，我已经不知道该怎么写了:sob:

> 两个最常使用的引擎为MyISAM和InnoDB，前者支持全文本搜索，而后者不支持。
>
> 样例表（productnotes表）使用MyISAM。

![image-20221227145604245](D:/notes/3150/image-20221227145604245.png)

like和正则的限制

- 性能——通配符和正则表达式匹配通常要求MySQL尝试匹配表中所有行（而且这些搜索极少使用表索引）。这些搜索可能非常耗时。
-  明确控制——使用通配符和正则表达式匹配，很难（而且并不总是能）明确地控制匹配什么和不匹配什么。
- 智能化的结果——虽然基于通配符和正则表达式的搜索提供了非常灵活的搜索，但它们都不能提供一种智能化的选择结果的方法。

## 启用

一般在创建表时启用全文本搜索。CREATE TABLE语句接受FULLTEXT子句，它给出被索引列的一个逗号分隔的列表。

![image-20221227150024080](D:/notes/3150/image-20221227150024080.png)

## 使用

在索引之后，使用两个函数Match()和Against()执行全文本搜索，其中Match()指定被搜索的列，Against()指定要使用的搜索表达式。

```mysql
mysql> select note_text from productnotes where match(note_text) against('rabbit');
+-----------------------------------------------------------------------------------------------------------------------+
| note_text
                                            |
+-----------------------------------------------------------------------------------------------------------------------+
| Customer complaint: rabbit has been able to detect trap, food apparently less effective now.                          |
| Quantity varies, sold by the sack load.
All guaranteed to be bright and orange, and suitable for use as rabbit bait. |
+-----------------------------------------------------------------------------------------------------------------------+
2 rows in set (0.01 sec)
```

> 此SELECT语句检索单个列note_text。由于WHERE子句，一个全文本搜索被执行。Match(note_text)指示MySQL针对指定的列进行搜索，Against('rabbit')指定词rabbit作为搜索文本。由于有两行包含词rabbit，这两个行被返回。

与like的区别：

```mysql
mysql> select note_text from productnotes where match(note_text) against('rabbit');
```

> 贴代码不美观。。。。。。。。。。。。。。贴图

![image-20221227151801706](D:/notes/3150/image-20221227151801706.png)

> 这里，在SELECT而不是WHERE子句中使用Match()和Against()。这使所有行都被返回（因为没有WHERE子句）。Match()和Against()用来建立一个计算列（别名为rank），此列包含全文本搜索计算出的等级值。等级由MySQL根据行中词的数目、唯一词的数目、整个索引中词的总数以及包含该词的行的数目计算出来。正如所见，不包含词rabbit的行等级为0（因此不被前一例子中的WHERE子句选择）。确实包含词rabbit的两个行每行都有一个等级值，文本中词靠前的行的等级值比词靠后的行的等级值高。

全文本搜索提供了简单LIKE搜索不能提供的功能。而且，由于数据是索引的，全文本搜索还相当快。

## 查询扩展

查询扩展不仅会展示所搜索的字符，还会找出可能与搜索相关的所有其他行。利用查询扩展，能找出可能相关的结果，即使它们并不精确包含所查找的词。

- 首先，进行一个基本的全文本搜索，找出与搜索条件匹配的所有行；
- 其次，MySQL检查这些匹配行并选择所有有用的词
- 再其次，MySQL再次进行全文本搜索，这次不仅使用原来的条件，而且还使用所有有用的词。

```mysql
select note_text from productnotes where match(note_text) against('anvils' with query expansion);
```

![image-20221227152418077](D:/notes/3150/image-20221227152418077.png)

> 这次返回了7行。第一行包含词anvils，因此等级最高。第二行与anvils无关，但因为它包含第一行中的两个词（customer和recommend），所以也被检索出来。第3行也包含这两个相同的词，但它们在文本中的位置更靠后且分开得更远，因此也包含这一行，但等级为第三。第三行确实也没有涉及anvils（按它们的产品名）。

## 布尔文本搜索

以布尔方式，可以提供关于如下内容的细节：

- 要匹配的词；
- 要排斥的词
- 排列提示
- 表达式分组；
- 另外一些内容。

> 布尔方式可不加索引，但非常慢

```mysql
mysql> select note_text from productnotes where match(note_text) against('heavy' in boolean mode);
```

为了匹配包含heavy但不包含任意以rope开始的词的行，可使用以下查询：

```mysql
mysql> select note_text from productnotes where match(note_text) against('heavy -rope*' in boolean mode);
```

![image-20221227153120616](D:/notes/3150/image-20221227153120616.png)

使用案例：

![image-20221227153343596](D:/notes/3150/image-20221227153343596.png)

## 使用说明

1. 在索引全文本数据时，短词被忽略且从索引中排除。短词定义为那些具有3个或3个以下字符的词（如果需要，这个数目可以更改）。
2. MySQL带有一个内建的非用词（stopword）列表，这些词在索引全文本数据时总是被忽略。如果需要，可以覆盖这个列表（请参阅MySQL文档以了解如何完成此工作）。
3. 许多词出现的频率很高，搜索它们没有用处（返回太多的结果）。因此，MySQL规定了一条50%规则，如果一个词出现在50%以上的行中，则将它作为一个非用词忽略。50%规则不用于IN BOOLEAN MODE。
4. 如果表中的行数少于3行，则全文本搜索不返回结果（因为每个词或者不出现，或者至少出现在50%的行中）。
5. 忽略词中的单引号。例如，don't索引为dont。
6. 不具有词分隔符（包括日语和汉语）的语言不能恰当地返回全文本搜索结果。（很可惜~~）
7. 如前所述，仅在MyISAM数据库引擎中支持全文本搜索。 

# 插入数据

## 单行插入

> insert into 表名(列名)  values (对应的值);

对于完整写法来说，每一列必须一一对应每一个值。否则插入会出问题。（可根据需要选择列）

> insert into 表名  values (值);

省略写法，默认对应所有的列，即value值也必须对应所有的列。

## 多行插入

1. 多条insert语句
2. 多个value项

> insert into 表名(列名)  values (对应的值),(对应的值),(对应的值)......;

> MySQL用单条INSERT语句处理多个插入比使用多条INSERT语句快。

## 插入检索出的数据

假如你想从另一表中合并客户列表到你的customers表。不需要每次读取一行，然后再将它用INSERT插入，可以使用INSERT SELECT：(这道题面试我就遇上了:sob:)

1. 准备一张和customers相同结构的表custnew
2. 给custnew 填充数据，注意主键不能重复，否则会插入失败

```mysql
mysql> insert into customers 
		select * from custnew;
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0
```

> 超级简写形式，因为懒:joy:
>
> 正常应列出所有的列

> 不一定要求列名匹配，它使用的是列的位置，因此SELECT中的第一列（不管其列名）将用来填充表列中指定的第一个列，第二列将用来填充表列中指定的第二个列，如此等等。

# 更新和删除

## 更新

- 更新表中特有的行
- 更新表中所有的行

两者的区别在于语句中是否有where来限制范围。

更新客户1005的电子邮箱：

```mysql
mysql> update customers set cust_email = 'abc@qq.com'
    -> where cust_id =1005;
Query OK, 0 rows affected (0.00 sec)
Rows matched: 0  Changed: 0  Warnings: 0
```

> :exclamation::exclamation:where子句非常重要，没有就更新整张表了

更新多列：

```mysql
mysql> update customers 
set cust_email = 'abc@qq.com',
cust_name = 'test_update'
where cust_id =1005;
Query OK, 0 rows affected (0.00 sec)
Rows matched: 0  Changed: 0  Warnings: 0
```

> 如果用UPDATE语句更新多行，并且在更新这些行中的一行或多行时出一个现错误，则整个UPDATE操作被取消（错误发生前更新的所有行被恢复到它们原来的值）。为即使是发生错误，也继续进行更新，可使用IGNORE关键字，如下所示： 
>
> UPDATE IGNORE customers… 

## 删除

- 删除表中特定的行
- 删除表中所有的行

两者的区别在于语句中是否有where来限制范围。

删除某一行：

```mysql
mysql> delete from customers where cust_id = 1006;
Query OK, 0 rows affected (0.00 sec)
```

> 如果没有where子句，那么将删除所有行。

> 如果想从表中删除所有行，不要使用DELETE。可使用TRUNCATE TABLE语句，它完成相同的工作，但速度更快（TRUNCATE实际是删除原来的表并重新创建一个表，而不是逐行删除表中的数据）。

# 创建和操作表

## 创建表

```mysql
CREATE TABLE `customers` (
  `cust_id` int(11) NOT NULL AUTO_INCREMENT,
  `cust_name` char(50) NOT NULL,
  `cust_address` char(50) DEFAULT NULL,
  `cust_city` char(50) DEFAULT NULL,
  `cust_state` char(5) DEFAULT NULL,
  `cust_zip` char(10) DEFAULT NULL,
  `cust_country` char(50) DEFAULT NULL,
  `cust_contact` char(50) DEFAULT NULL,
  `cust_email` char(255) DEFAULT NULL,
  PRIMARY KEY (`cust_id`)
) ENGINE=InnoDB;
```

```mysql
CREATE TABLE 表名(
    字段值  字段类型  是否为空（NULL|NOT NULL） 默认值（default xx）AUTO_INCREMENT(int主键自动递增),
    ....
    PRIMARY KEY (`字段值`) #指定主键
)ENGINE = 选择引擎类型;
```

## 更新表

```mysql
#添加一列：
alter table 表名  add  列名  类型;
#删除一列：
alter table 表名 drop column 列名;
#定义外键：
alter table orderitems
add constant fk_orderitems_orders
foreign key (order_num) references orders(order_num);
```

## 删除表

```mysql
drop table 表名;
```

## 重命名

```mysql
rename table 表名 to 更新后名字;
```

# 视图

​		视图是虚拟的表。与包含数据的表不一样，视图只包含使用时动态检索数据的查询。(使用视图还是相当于查询语句，只是语句提前写好了)

- 重用SQL语句。
- 简化复杂的SQL操作。（把复杂的语句重用）
- 使用表的组成部分而不是整个表。
- 保护数据。
- 更改数据格式和表示。视

## 使用视图

- 使用CREATE VIEW语句来创建。

- 使用SHOW CREATE VIEW viewname；来查看创建视图的语句。

- 用DROP删除视图，其语法为DROP VIEW viewname;

- 更新视图时，可先删除再创建，也可使用CREATE OR 

  REPLACE VIEW。

## 利用视图

创建一个视图：

```mysql
mysql> create view productcustomers as
    -> select cust_name,cust_contact,prod_id
    -> from customers,orders,orderitems
    -> where customers.cust_id = orders.cust_id
    -> and orderitems.order_num = orders.order_num;
Query OK, 0 rows affected (0.01 sec)

mysql>
```

工具中查看：

![image-20221228090413477](D:/notes/3150/image-20221228090413477.png)

```mysql
mysql> select * from productcustomers;
+----------------+--------------+---------+
| cust_name      | cust_contact | prod_id |
+----------------+--------------+---------+
| Coyote Inc.    | Y Lee        | ANV01   |
| Coyote Inc.    | Y Lee        | ANV02   |
| Coyote Inc.    | Y Lee        | TNT2    |
| Coyote Inc.    | Y Lee        | FB      |
| Coyote Inc.    | Y Lee        | FB      |
| Coyote Inc.    | Y Lee        | OL1     |
| Coyote Inc.    | Y Lee        | SLING   |
| Coyote Inc.    | Y Lee        | ANV03   |
| Wascals        | Jim Jones    | JP2000  |
| Yosemite Place | Y Sam        | TNT2    |
| E Fudd         | E Fudd       | FC      |
+----------------+--------------+---------+
11 rows in set (0.00 sec)
```

> 可以看出，它是一张表。但它是一张虚拟的表，是通过查询语句动态查询出来的。

检索订购了产品TNT2的客户:

```mysql
mysql> select * from productcustomers
    -> where prod_id = 'tnt2';
+----------------+--------------+---------+
| cust_name      | cust_contact | prod_id |
+----------------+--------------+---------+
| Coyote Inc.    | Y Lee        | TNT2    |
| Yosemite Place | Y Sam        | TNT2    |
+----------------+--------------+---------+
2 rows in set (0.00 sec)

mysql>
```

## 利用视图格式化数据

> 视图是检索出来的数据。理论上来说，适用于select的操作都可以在视图中使用。

```mysql
mysql> create view vendorlocatons as
    -> select concat(rtrim(vend_name),'(',rtrim(vend_country),')')  vend_title
    -> from vendors
    -> order by vend_name;
Query OK, 0 rows affected (0.01 sec)

mysql> select * from vendorlocatons;
+------------------------+
| vend_title             |
+------------------------+
| ACME(USA)              |
| Anvils R Us(USA)       |
| Furball Inc.(USA)      |
| Jet Set(England)       |
| Jouets Et Ours(France) |
| LT Supplies(USA)       |
+------------------------+
6 rows in set (0.00 sec)

mysql>
```

## 利用视图过滤数据

过滤没有电子邮件地址的客户

```mysql
mysql> create view customeremial as
    -> select cust_id ,cust_name,cust_email
    -> from customers
    -> where cust_email is not null;
Query OK, 0 rows affected (0.01 sec)

mysql> select * from customeremail;
ERROR 1146 (42S02): Table 'mysql_test.customeremail' doesn't exist
mysql> select * from customeremial;
+---------+----------------+---------------------+
| cust_id | cust_name      | cust_email          |
+---------+----------------+---------------------+
|       1 | Coyote Inc.    | ylee@coyote.com     |
|   10001 | Coyote Inc.    | ylee@coyote.com     |
|   10003 | Wascals        | rabbit@wascally.com |
|   10004 | Yosemite Place | sam@yosemite.com    |
|   10006 | a              | h                   |
+---------+----------------+---------------------+
5 rows in set (0.00 sec)
```

## 计算字段

为检索订单20005的详细内容

```mysql
mysql> create view  orderitemexpand as
    -> select order_num,prod_id,quantity,item_price,
    -> quantity*item_price as expanded_price
    -> from orderitems;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from orderitemexpand where order_num = 20005;
+-----------+---------+----------+------------+----------------+
| order_num | prod_id | quantity | item_price | expanded_price |
+-----------+---------+----------+------------+----------------+
|     20005 | ANV01   |       10 |       5.99 |          59.90 |
|     20005 | ANV02   |        3 |       9.99 |          29.97 |
|     20005 | TNT2    |        5 |      10.00 |          50.00 |
|     20005 | FB      |        1 |      10.00 |          10.00 |
+-----------+---------+----------+------------+----------------+
4 rows in set (0.00 sec)
```

## 更新视图

通常，视图是可更新的（即，可以对它们使用INSERT、UPDATE和DELETE）。如果你对视图增加或删除行，实际上是对其基表增加或删除行。

但是，并非所有视图都是可更新的。基本上可以说，如果MySQL不能正确地确定被更新的基数据，则不允许更新（包括插入和删除）。如果视图定义中有以下操作，则不能进行视图的更新：

- 分组
- 联结
- 子查询
- 并
- 聚集函数
- distinct
- 导出（计算）列

>  一般，应该将视图用于检索（SELECT语句）而不用于更新（INSERT、UPDATE和DELETE）。

# 存储过程

​		迄今为止，使用的大多数SQL语句都是针对一个或多个表的单条语句。并非所有操作都这么简单，经常会有一个完整的操作需要多条语句才能完成。（:bamboo:也就是说，涉及到复杂的业务逻辑。但是，既然涉及到复杂的业务逻辑我在应用层解决就行了，为啥要留在数据库解决呢？当然，既然设计出来了就有它的意义，该学还是得学，该掉头发还是得掉头发:dog:）

## 创建存储过程

> 用命令行创建会有点麻烦，因为  ； 标志着结束，需要临时更改程序的语句分隔符。

创建：

```mysql
CREATE PROCEDURE productprincing()
BEGIN
	SELECT AVG(prod_price) as priceaverage
	from products;
end;
```

用CREATE PROCEDURE productpricing()语句定义。如果存储过程接受参数，它们将在()中列举出来。此存储过程没有参数，但后跟的()仍然需要。BEGIN和END语句用来限定存储过程体，过程体本身仅是一个简单的SELECT语句

## 调用

```mysql
mysql> CALL productprincing();
+--------------+
| priceaverage |
+--------------+
|    16.133571 |
+--------------+
1 row in set (0.01 sec)

Query OK, 0 rows affected (0.01 sec)
```

CALL接受存储过程的名字以及需要传递给它的任意参数。

> CALL	 productprincing(@pricelow,@pricehigh,@priceaverage);

## 删除存储过程

```mysql
mysql> drop procedure productprincing;
Query OK, 0 rows affected (0.01 sec)
```

这条语句删除刚创建的存储过程。请注意没有使用后面的()，只给出存储过程名。

> 使用DROP PROCEDURE IF EXISTS。存在时删除

## 使用参数

```mysql
CREATE PROCEDURE ordertotal ( 
    IN onnumber INT, 
    OUT ototal DECIMAL ( 8, 2 ) ) 
    BEGIN
	SELECT
		SUM( item_price * quantity ) INTO ototal
	FROM
		orderitems 
	WHERE
		order_num = onnumber ;
	
END;
```

onumber定义为IN，因为订单号被传入存储过程。ototal定义为OUT，因为要从存储过程返回合计。SELECT语句使用这两个参数，WHERE子句使用onumber选择正确的行，INTO使用ototal存储计算出来的合计。

```mysql
mysql> CALL ordertotal ( 20005, @total );
Query OK, 1 row affected (0.00 sec)

mysql> SELECT
    ->  @total;
+--------+
| @total |
+--------+
| 149.87 |
+--------+
1 row in set (0.00 sec)
```

> ps:当然，还有些更高级的用法。选择，循环等
>
> [MySQL 存储过程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/mysql-stored-procedure.html#:~:text=MySQL 5.0 版本开始支持存储过程。,存储过程（Stored Procedure）是一种在数据库中存储复杂程序，以便外部程序调用的一种数据库对象。 存储过程是为了完成特定功能的SQL语句集，经编译创建并保存在数据库中，用户可通过指定存储过程的名字并给定参数 (需要时)来调用执行。)

## 例子

```mysql
CREATE PROCEDURE ordertotal ( 
    IN onumber INT, 
    IN taxable BOOLEAN, 
    OUT ototal DECIMAL ( 8, 2 ) ) 
BEGIN
    # DECLARE声明两个局部变量，指定变量名和类型
	DECLARE total DECIMAL ( 8, 2 );
	DECLARE taxrate INT DEFAULT 6;
	# 查询总额
	SELECT
		sum( item_price * quantity ) 
	FROM
		orderitems 
	WHERE
	# 存储到total
		order_num = onumber INTO total;
	IF taxable THEN
	# 如果存在执行
		SELECT total + ( total / 100 * taxrate ) INTO total;
		# set total = total + ( total / 100 * taxrate );
	END IF;
	SELECT total INTO ototal;
	# set ototal = total;
END
```

```mysql
mysql> CALL ordertotal ( 20005,0, @total );
Query OK, 1 row affected (0.00 sec)

mysql> select @total;
+--------+
| @total |
+--------+
| 149.87 |
+--------+
1 row in set (0.00 sec)
```

## 检查存储过程

```mysql
mysql> show create procedure ordertotal;
```

# 游标

游标（cursor）是一个存储在MySQL服务器上的数据库查询，它不是一条SELECT语句，而是被该语句检索出来的**结果集**。在存储了游标之后，应用程序可以根据需要滚动或浏览其中的数据。

MySQL游标只能用于存储过程（和函数）。

```mysql
# 创建游标
declare ordernumbers CURSOR for select order_num from orders;
# 打开和关闭
open ordernumbers;
close ordernumbers;
#如果你不明确关闭游标，MySQL将会在到达END语句时自动关闭它。
```

## 简单案例

```mysql
CREATE PROCEDURE processorders ( ) BEGIN
# oracle可以定义为游标的行类型，MySQL难道不支持？暂时没查到资料
	DECLARE
		o INT;
	DECLARE
		ordernumbers CURSOR FOR SELECT
		order_num 
	FROM
		orders;
	OPEN ordernumbers;
	# 中FETCH用来检索当前行的order_num列（将自动从第一行开始）到一个名为o的局部声明的变量中。
	# 结合循环获取每一行，隐约记得以前上大型数据库这门课时用loop啥的坐过，现在忘光了。。。。只能下次补啦。。
	FETCH ordernumbers INTO o;
CLOSE ordernumbers;
END;
```

# 触发器

触发器是MySQL响应以下任意语句而自动执行的一条MySQL语句（或位于BEGIN和END语句之间的一组语句）

只支持：INSERT、UPDATE、DELETE

只有表才支持触发器！视图不支持。

触发器需要的信息：

- 唯一的触发器名
- 触发器关联的表
-  触发器应该响应的活动（DELETE、INSERT或UPDATE）
- 触发器何时执行

```mysql
create TRIGGER newproduct after insert on products for each row select 'product add';
```

> CREATE TRIGGER用来创建名为newproduct的新触发器。触发器可在一个操作发生之前或之后执行，这里给出了AFTER INSERT，所以此触发器将在INSERT语句成功执行后执行。这个触发器还指定FOREACH ROW，因此代码对每个插入行执行。(默认语句级触发器)在这个例子中，文本product add将对每个插入的行显示一次。

> 行级和语句级：
>
> 行级当影响一行数据执行；语句级一条语句执行一次。
>
> 例如：一个update改变6行数据。行级6次，语句级1次。

删除触发器：

```mysql
drop trigger newproduct;
```

NEW 和OLD

```mysql
create trigger neworder after insert on orders for each row select  select NEW.order_num;
```

> 此代码创建一个名为neworder的触发器，它按照AFTER INSERT ON orders执行。在插入一个新订单到orders表时，MySQL生成一个新订单号并保存到order_num中。触发器从NEW. order_num取得这个值并返回它。
>
> 此触发器必须按照AFTER INSERT执行，因为在BEFOREINSERT语句执行之前，新order_num还没有生成。
>
> 可通过NEW.和OLD.引用新值和旧值。
>
> ps:Oracle 可通过**:NEW** 和**:OLD**获取到新值和旧值

![image-20221228114708052](D:/notes/3150/image-20221228114708052.png)

# 事务管理

事务处理是一种机制，用来管理必须成批执行的MySQL操作，以保证数据库不包含不完整的操作结果。利用事务处理，可以保证一组操作不会中途停止，它们或者作为整体执行，或者完全不执行（除非明确指示）。如果没有错误发生，整组语句提交给（写到）数据库表。如果发生错误，则进行回退（撤销）以恢复数据库到某个已知且安全的状态。

- 事务：一组SQL语句
- 回退：撤销指定SQL语句的过程
- 提交：将未存储的SQL语句结果写入数据库表
- 保留点：事务处理中设置的临时占位符。你可以对它发布回退

事务开始：

```mysql
start transaction
```

> 开启事务后，不会默认提交

回退：

```mysql
rollback
```

> 事务处理用来管理INSERT、UPDATE和DELETE语句。你不能回退SELECT语句。（这样做也没有什么意义。）
>
> 你不能回退CREATE或DROP操作。事务处理块中可以使用这两条语句，但如果你执行回退，它们不会被撤销。

例：

```mysql
select * from ordertotals;
start transaction;
DELETE from ordertotals;
select * from ordertotals;
rollback;
select * from ordertotals;
```

提交：在事务处理块中，提交不会隐含地进行。为进行明确的提交，使用COMMIT语句

```mysql
start transaction;
delete from orderitems where order_num =20010;
delete from orders where order_num = 20010;
commit;
```

保留点：

```mysql
SAVEPOINT pointone;
#回退至保留点
ROLLBACK TO pointone;
```

默认的MySQL行为是自动提交所有更改。换句话说，任何时候你执行一条MySQL语句，该语句实际上都是针对表执行的，而且所做的更改立即生效。

```mysql
#设置为0不自动提交
SET autocommit =0;
```

# MySQL语法

ALTERT TABLE

````mysql
ALTER TABLE tablename
(
	add    	column 			datatype [NULL|NOT NULL] [CONSTRAINTS],
    CHANGE  column  columns datatype [NULL|NOT NULL] [CONSTRAINTS],
    drop    column,
    ...
)
````

COMMIT

```mysql
commit
```

CREATE INDEX

```mysql
CREATE index indexname on tablename (column);
```

CREATE USER

CREATE VIEW

```MYSQL
CREATE VIEW viewname as select....
```

DELETE

```MYSQL
delete from tablename where ..
```

DROP

```MYSQL
DROP DATABASE|INDEX|PROCEDURE|TABLE|TRIGGER|USER|VIEW name;
```

INSERT

```MYSQL
INSERT INTO tablename ... values(..);
```

INSERT SELECT

```MYSQL
INSERT INTO tablename ...
SELECT ... from ..
```

ROLLBACK

SAVEPOINT

SELECT

START  TRANSACTION

UPDATE

# 进阶

![](D:/notes/3150/MySQL.png)