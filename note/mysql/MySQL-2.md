# B+树索引

```mysql
mysql> CREATE TABLE index_demo(
 -> c1 INT,
 -> c2 INT,
 -> c3 CHAR(1),
 -> PRIMARY KEY(c1)
 -> ) ROW_FORMAT = Compact;
Query OK, 0 rows affected (0.03 sec)
```



数据页结构：

各个数据页可以组成一个`双向链表` ，而每个数据页中的记录会按照主键值从小到大的顺序组成一个`单向链表` ，每个数据页都会为存储在它里边儿的记录生成一个页目录 ，在通过主键查找某条记录的时候可以在 页目录 中使用`二分法`快速定位到对应的槽，然后再遍历该槽对应分组中的记录即可快速找到指定的记录。

![image-20230111095218693](D:/notes/3150/image-20230111095218693.png)

## 同页查找

- 主键搜索

  可以在 页目录 中使用二分法快速定位到对应的槽，然后再遍历该槽对应分组中的记录即可快速找到指定的记录。

- 非主键搜索

  从最小记录 开始依次遍历单链表中的每条记录，然后对比每条记录是不是符合搜索条件。很显然，这种查找的效率是非常低的。

## 多页查找

1. 定位所在页
2. 每页查找

<div style='color:red'>由于我们并不能快速的定位到记录所在的页，所以只能从第一个页沿着双向链表一直往下找，在每一个页中根据我们刚刚唠叨过的查找方式去查找指定的记录。</div>

## 索引构建

![image-20230111100107919](D:/notes/3150/image-20230111100107919.png)

- 数据页编号不一定连续
- 数据项编号
  - 0：用户数据
  - 2：最小记录（系统生成）
  - 3：最大记录（系统生成）
- 数据按照主键大小从小到大排序

由于数据页在物理存储上可能并不连续，所以为了加快定位某些主键，我们需要给他们做个目录，**每页对应一个目录。**

![image-20230111100614822](D:/notes/3150/image-20230111100614822.png)

目录项结构：

- key：页内用户记录的最小主键
- page_no：页号

## 索引方案

为了管理目录项，复用了之前存储用户记录的数据页来存储目录项，为了和用户记录做一下区分，我们把这些用来表示目录项的记录称为 目录项记录 。

区分目录项与用户记录（根据记录头的record_type）：

- 0：普通用户记录
- 1：目录项记录
- 2：最小记录
- 3：最大记录

![image-20230111101608992](D:/notes/3150/image-20230111101608992.png)

当目录项的记录过大，一页存放不下时，又会新增一页存放。逐层累加。最后形成一棵B+树。

![image-20230111101946052](D:/notes/3150/image-20230111101946052.png)

**实际存放用户记录的都是B+树的最底层节点。**其余用来存放`目录项`的节点称为`非叶子节点`。最上面的节点称为`根节点`。

### 聚簇索引

B+树特点：

1. 使用记录的主键值进行用户记录和页的排序
   - 页内记录按照主键从小到达排序成一个单项链表
   - 存放用户记录的页根据主键大小排成一个双向链表
   - （记录目录项的页）同一层次的页根据目录项记录的主键排成双向链表
2. 叶子节点记录完成的用户记录

把具有这两种特性的 B+ 树称为 聚簇索引 ，所有完整的用户记录都存放在这个 聚簇索引 的叶子节点处。

在 InnoDB 存储引擎中， 聚簇索引 就是数据的存储方式（所有的用户记录都存储在了 叶子节点 ），也就是所谓的索引即数据，数据即索引。

### 二级索引

聚簇索引只能在搜索条件是主键值时才能发挥作用，因为 B+ 树中的数据都是按照主键进行排序的。

我们可以多建几棵 B+ 树，不同的 B+ 树中的数据采用不同的排序规则。比方说我们用 c2 列的大小作为数据页、页中记录的排序规则，再建一棵 B+ 树。

![image-20230111101858175](D:/notes/3150/image-20230111101858175.png)

不同：

- 使用c2列的大小进行记录和页的排序
- 叶子节点不是完整的记录，而是c2+主键
- 目录项记录c2+页号

<div style='color:red'> 这个 B+ 树的叶子节点中的记录只存储了 c2 和 c1 （也就是 主键 ）两个列，所以我们必须再根据主键值去聚簇索引中再查找一遍完整的用户记录。</div>

所以如果我们想根据 c2 列的值查找到完整的用户记录的话，仍然需要到 聚簇索引 中再查一遍，这个过程也被称为 回表 。也就是根据 c2 列的值查询一条完整的用户记录需要使用到 2 棵 B+ 树！！！

### 联合索引

我们也可以同时以多个列的大小作为排序规则，也就是同时为多个列建立索引，比方说我们想让 B+ 树按照 c2和 c3 列的大小进行排序，这个包含两层含义：

- 先把各个记录和页按照 c2 列进行排序。
- 在记录的 c2 列相同的情况下，采用 c3 列进行排序

![image-20230111105742785](D:/notes/3150/image-20230111105742785.png)

- 每条 目录项记录 都由 c2 、 c3 、 页号 这三个部分组成，各条记录先按照 c2 列的值进行排序，如果记录的 c2 列相同，则按照 c3 列的值进行排序。
- B+ 树叶子节点处的用户记录由 c2 、 c3 和主键 c1 列组成。

> 以c2和c3列的大小为排序规则建立的B+树称为联合索引，本质上也是一个二级索引。它的意思与分别为c2和c3列分别建立索引的表述是不同的。

- 建立联合索引 只会建立1棵 B+ 树。
- 为c2和c3列分别建立索引会分别以 c2 和 c3 列的大小为排序规则建立2棵 B+ 树。

## 注意事项

### 根页面不动

一个B+树索引的根节点自诞生之日起，便不会再移动。

当 根节点 中的可用空间用完时继续插入记录，此时会将 根节点 中的所有记录复制到一个新分配的页，比如 页a 中，然后对这个新页进行 页分裂 的操作，得到另一个新页，比如 页b 。这时新插入的记录根据键值（也就是聚簇索引中的主键值，二级索引中对应的索引列的值）的大小就会被分配到 页a 或者 页b 中，而根节点 便升级为存储目录项记录的页。

### 内节点中目录项记录的唯一性

假设某张表：

![image-20230111110950301](D:/notes/3150/image-20230111110950301.png)

为c2建立索引：

![image-20230111111012495](D:/notes/3150/image-20230111111012495.png)

新插入一条记录： 9、1、c

由于 页3 中存储的目录项记录是由 c2列 + 页号 的值构成的，页3 中的两条目录项记录对应的 c2 列的值都是 1 ，而我们新插入的这条记录的 c2 列的值也是 1 ，那我们这条新插入的记录到底应该放到 页4 中，还是应该放到 页5 中啊？

**我们需要保证在B+树的同一层内节点的目录项记录除页号这个字段以外是唯一的。**

实际构成：

- 索引列
- 主键列
- 页号

可以先把新记录的 c2 列的值和 页3 中各目录项记录的 c2 列的值作比较，如果 c2 列的值相同的话，可以接着比较主键值，因为 B+ 树同一层中不同目录项记录的 c2列 + 主键 的值肯定是不一样的，所以最后肯定能定位唯一的一条目录项记录。

### 一个页面最少存储2条记录

InnoDB 的一个数据页至少可以存放两条记录

### MyISAM中的索引

InnoDB 中索引即数据，**也就是聚簇索引的那棵 B+ 树的叶子节点中已经把所有完整的用户记录都包含了**，而 MyISAM 的索引方案虽然也使用树形结构，但是却将索引和数据分开存储。

- 将表中的记录**按照记录的插入顺序**单独存储在一个文件中，称之为 数据文件 。这个文件并不划分为若干个数据页，有多少记录就往这个文件中塞多少记录就成了。我们可以通过行号而快速访问到一条记录。
- 使用 MyISAM 存储引擎的表会把索引信息另外存储到一个称为 索引文件 的另一个文件中。 MyISAM 会单独为表的主键创建一个索引，只不过在索引的叶子节点中存储的不是完整的用户记录，而是 主键值 + 行号 的组合。也就是先通过索引找到对应的行号，再通过行号去找对应的记录！

在 MyISAM 中却需要进行一次 回表 操作，意味着 MyISAM 中建立的索引相当于全部都是 二级索引 ！

# B+树索引使用

## 适用条件

```mysql
CREATE TABLE person_info(
 id INT NOT NULL auto_increment,
 name VARCHAR(100) NOT NULL,
 birthday DATE NOT NULL,
 phone_number CHAR(11) NOT NULL,
 country varchar(100) NOT NULL,
 PRIMARY KEY (id),
 KEY idx_name_birthday_phone_number (name, birthday, phone_number)
);
```

- 表中的主键是 id 列，它存储一个自动递增的整数。
- 个二级索引 idx_name_birthday_phone_number ，它是由3个列组成的联合索引。只保留 name 、 birthday 、 phone_number以及主键id。
  - 先按照 name 列的值进行排序。
  - 如果 name 列的值相同，则按照 birthday 列的值进行排序。
  - 如果 birthday 列的值也相同，则按照 phone_number 的值进行排序。

### 全值匹配

如果我们的搜索条件中的列和索引列一致的话，这种情况就称为全值匹配。

```mysql
SELECT * FROM person_info WHERE name = 'Ashburn' AND birthday = '1990-09-27' AND phone_number = '15123983239';
```

- 因为 B+ 树的数据页和记录先是按照 name 列的值进行排序的，所以先可以很快定位 name 列的值是 Ashburn的记录位置。
- 在 name 列相同的记录里又是按照 birthday 列的值进行排序的，所以在 name 列的值是 Ashburn 的记录里又可以快速定位 birthday 列的值是 '1990-09-27' 的记录。
-  name 和 birthday 列的值都是相同的，那记录是按照 phone_number 列的值排序的，所以联合索引中的三个列都可能被用到。

> 由于查询优化器的存在， WHERE 子句中的几个搜索条件的顺序对查询结果没有影响！！！
>
> 例如：
>
> ```mysql
> SELECT * FROM person_info WHERE birthday = '1990-09-27' AND phone_number = '15123983239' AND name = 'Ashburn';
> ```

### 匹配最左边的列

可以不用包含全部联合索引的列，值包含最左边的就行：

```mysql
 # 包含1列
 SELECT * FROM person_info WHERE name = 'Ashburn';
 # 包含多列
 SELECT * FROM person_info WHERE name = 'Ashburn' AND birthday = '1990-09-27';
```

:bomb::bomb:必须从最左边开始（列不全时）！！

**不行的：**

```mysql
SELECT * FROM person_info WHERE birthday = '1990-09-27';
```

因为 B+ 树的数据页和记录先是按照 name 列的值排序的，在 name 列的值相同的情况下才使用 birthday 列进行排序，也就是说 name 列的值不同的记录中 birthday 的值可能是无序的。

**:rotating_light:如果我们想使用联合索引中尽可能多的列，搜索条件中的各个列必须是联合索引中从最左边连续的列:rotating_light:**

```mysql
SELECT * FROM person_info WHERE name = 'Ashburn' AND phone_number = '15123983239';
```

这样只能用到 name 列的索引， birthday 和 phone_number 的索引就用不上了

### 匹配列前缀

对name建立索引、排序。此时的排列：

```
Aaron
Aaron
...
Aaron
Asa
Ashburn
...
Ashburn
Baird
Barlow
...
Barlow
```

- 先比较第一个，第一个小的小
- 第一个相同，比较第二个
- 。。。。

也就是说，对于字符串来说，前缀都是排好序的。我们只匹配前缀也可以快速定位。

```mysql
SELECT * FROM person_info WHERE name LIKE 'As%';
```

但是，只有后缀或者中间的某个字符串不行：

```mysql
SELECT * FROM person_info WHERE name LIKE '%As%';
# 这种不行
```

### 匹配范围

所有记录都是按照索引列的值从小到大的顺序排好序的，所以这极大的方便我们查找索引列的值在某个范围内的记录。

```mysql
SELECT * FROM person_info WHERE name > 'Asa' AND name < 'Barlow';
```

- 找到 name 值为 Asa 的记录。
- 找到 name 值为 Barlow 的记录。
- 通过链表取记录。
- 回表查询。

**如果对多个列同时进行范围查找的话，只有对索引最左边的那个列进行范围查找的时候才能用到 B+ 树索引**

> 使用后一列的索引的 条件是必须前一列确定。

```mysql
SELECT * FROM person_info WHERE name > 'Asa' AND name < 'Barlow' AND birthday > '1980-01-01';
```

- 对name范围查找，结果会有很多name不同的记录
- name不同的记录通过birthday过滤

### 精确匹配某一列并范围匹配另一列

虽然对多个列都进行范围查找时只能用到最左边那个索引列，但是如果左边的列是精确查找，则右边的列可以进行范围查找。

```mysql
SELECT * FROM person_info WHERE name = 'Ashburn' AND birthday > '1980-01-01' AND birthday< '2000-12-31' AND phone_number > '15100000000';
```

### 排序

```mysql
 ORDER BY name, birthday, phone_number
```

要先按照 name 值排序，如果记录的 name 值相同，则需要按照 birthday 来排序，如果birthday 的值相同，则需要按照 phone_number 排序。

**顺序不对不行！**

ORDER BY 的子句后边的列的顺序也必须按照索引列的顺序给出，如果给出ORDER BY phone_number, birthday, name 的顺序，那也是用不了 B+ 树索引。

#### 索引无效情况

##### ASC、DESC混用

```mysql
SELECT * FROM person_info ORDER BY name, birthday DESC LIMIT 10;
```

混用：

- 先从索引的最左边确定 name 列最小的值，然后找到 name 列等于该值的所有记录，然后从 name 列等于该值的最右边的那条记录开始往左找10条记录。
- 如果 name 列等于最小的值的记录不足10条，再继续往右找 name 值第二小的记录，重复上边那个过程，直到找到10条记录为止。

##### **WHERE**子句中出现非排序使用到的索引列

```mysql
SELECT * FROM person_info WHERE country = 'China' ORDER BY name LIMIT 10;
```

这个查询只能先把符合搜索条件 country = 'China' 的记录提取出来后再进行排序，是使用不到索引。

```mysql
SELECT * FROM person_info WHERE name = 'A' ORDER BY birthday, phone_number LIMIT 10;
```

这个可以！ 

name = 'A' 可以使用到索引 idx_name_birthday_phone_number ，而且过滤剩下的记录还是按照 birthday 、 phone_number 列排序的

##### 排序列包含非同一个索引的列

```mysql
SELECT * FROM person_info ORDER BY name, country LIMIT 10;
```

- name的索引里没有country的记录

##### 使用复杂表达式

要想使用索引进行排序操作，必须保证索引列是以单独列的形式出现，而不是修饰过的形式

```mysql
SELECT * FROM person_info ORDER BY UPPER(name) LIMIT 10;
```

### 分组

```mysql
SELECT name, birthday, phone_number, COUNT(*) FROM person_info GROUP BY name, birthday, phone_number
```

-  先把记录按照 name 值进行分组，所有 name 值相同的记录划分为一组。
- 将每个 name 值相同的分组里的记录再按照 birthday 的值进行分组，将 birthday 值相同的记录放到一个小分组里，所以看起来就像在一个大分组里又化分了好多小分组。
-  再将上一步中产生的小分组按照 phone_number 的值分成更小的分组，所以整体上看起来就像是先把记录分成一个大分组，然后把 大分组 分成若干个 小分组 ，然后把若干个 小分组 再细分成更多的 小小分组 。

分组列的顺序也需要和索引列的顺序一致，也可以只使用索引列中左边的列进行分组。