---
title: "MySQL的索引和SQL优化"
date: 2020-06-07T20:13:46+08:00
draft: true
---

## 索引

### 1.索引的分类

（1）从数据结构角度
B+树索引、hash索引、FULLTEXT索引、R-tree树索引

（2） 从物理角度
聚族索引、非聚族索引

（3）从逻辑角度
主键索引 唯一索引 多列索引 单列索引

### 2.创建索引的一些基本原则

（1）多列索引需要满足左前缀原则,即联合索引先匹配最左边的那个，然后一层层往下。

（2）= 和 in 可以乱序， 比如a = 1 and b = 2 and c = 3 建立(a,b,c)索引可以任意顺序，mysql的查询优化器会帮你优化成索引
可以识别的形式

（3）尽量选择区分度高的列作为索引（区分度越高，我们扫描的记录就越少）

（4）索引列不能参与计算（索引列一旦参与函数计算，会使得索引失效）

（5）尽量扩展索引，不要新建索引，比如表中已经有a索引，现在要加（a,b）索引只需要修改原来的索引即可。

## SQL优化

### 1.慢查询日志

查看是否开启： show variables like '%slow_query_log%';

开启慢查询日志：set global slow_query_log=1; (重启会失效)

开启了慢查询日志后，什么样的SQL才会记录到查询日志里面？

这个是由参数long_query_time控制，默认情况下long_query_time的值为10秒

查看命令： show variables like 'long_query_time%';

### 2.日志分析工具 mysqldumpslow

- 工作常用参考:

1. 得到返回记录集最多的10个SQL: mysqldumpslow -s r -t 10 /var/lib/mysql/mysql-slow.log

2. 得到访问次数最多的10个SQL: mysqldumpslow -s c -t 10 /var/lib/mysql/mysql-slow.log

3. 得到按照时间排序的前10条里面含有左连接的SQL: mysqldumpslow -s t -t 10 -g "left join" /var/lib/mysql/mysql-slow.log

### 3.SQL优化点

- 一、不要建立太多索引

我们虽然可以根据我们的喜好在不同的列上建立索引，但是建立索引是有代价的：

**a. 空间上的代价** 

每建立一个索引都要为它建立一棵B+树，每一棵B+树的每一个节点都是一个数据页，一个页默认会占用16KB的存储空间，一棵很大的B+树由许多数据页组成，可想而知会占多少存储空间了

**b. 时间上的代价**

每次对表中的数据进行增、删、改操作时，都需要去修改各个B+树索引。在B+ 树上每层节点都是按照索引列的值从小到大的顺序排序而组成了双向链表。

不论是叶子节点中的记录，还是内节点中的记录（也就是不论是用户记录还是目录项记录）都是按照索引列的值从小到大的顺序而形成了一个单向链表。

而增、删、改操作可能会对节点和记录的排序造成破坏，所以存储引擎需要额外的时间进行一些记录移位，页面分裂、页面回收啥的操作来维护好节点和记录的排序。

- 二、联合索引使用问题

B+树中每层节点都是按照索引列的值从小到大的顺序而形成了一个单链表。如果是联合索引的话，则页面和记录先按照联合索引前边的列排序，如果该列值相同，再按照联合索引后边的列排序。

#### 1.匹配左边的列

因为B+树的数据页和记录先是按照name列的值排序的，在name列的值相同的情况下才使用birthday列进行排序，也就是说name列的值不同的记录中birthday的值可能是无序的。

如果用的不是最左列的话就无法使用到索引，例如

`SELECT * FROM person_info WHERE birthday = '1990-09-27';`

如果是

`SELECT * FROM person_info WHERE name = 'Ashburn' AND phone_number = '15123983239';`

这样只能用到name列的索引，birthday和phone_number的索引就用不上了，因为name值相同的记录先按照birthday的值进行排序，birthday值相同的记录才按照phone_number值进行排序。

#### 2.匹配范围值

在使用联合索引进行范围查找时候，如果对多个列同时进行范围查找的话，只有对索引最左边的那个列进行范围查找的时候才能用到B+树索引。

`SELECT * FROM person_info WHERE name > 'Asa' AND name < 'Barlow' AND birthday > '1980-01-01';`

对于联合索引`idx_name_birthday_phone_number`来说，可以用name快速定位到通过条件`name > 'Asa' AND name < 'Barlow’`.

但是却无法通过`birthday > '1980-01-01`'条件继续过滤，因为通过name进行范围查找的记录中可能并不是按照birthday列进行排序的。

#### 3.精确匹配某一列并范围匹配另外一列

`SELECT * FROM person_info WHERE name = 'Ashburn' AND birthday > '1980-01-01' AND birthday < '2000-12-31' AND phone_number > '15100000000';`

在这条SQL中，由于对name是精确查找，所以在name相同的情况下birthday是排好序的，birthday列进行范围查找是可以用到B+树索引的。

但是对于phone_number来说，通过birthday的范围查找的记录的birthday的值可能不同，所以这个条件无法再利用B+树索引了


#### 4.排序

1.对于联合索引来说，ORDER BY的子句后边的列的顺序也必须按照索引列的顺序给出，如果给出ORDER BY phone_number, birthday, name的顺序，那也是用不了B+树索引。

2.ASC、DESC混用是不能使用到索引的
对于使用联合索引进行排序的场景，我们要求各个排序列的排序顺序是一致的，也就是要么各个列都是ASC规则排序，要么都是DESC规则排序。

3.WHERE子句中出现非排序使用到的索引列无法使用到索引
如：


`SELECT * FROM person_info WHERE country = 'China' ORDER BY name LIMIT 10;`

这个语句需要回表后查出整行记录进行过滤后才能进行排序，无法使用索引进行排序

4.排序列包含非同一个索引的列无法使用索引
比方说：


`SELECT * FROM person_info ORDER BY name, country LIMIT 10;`

Order by 中使用了函数也无法使用索引。

#### 5.匹配列前缀

和联合索引其实有点类似，如果一个字段比如是varchar类型的name字段，那么在索引中name字段的排列就会：

1.先比较字符串的第一个字符，第一个字符小的那个字符串就比较小。

2.如果两个字符串的第一个字符相同，那就再比较第二个字符，第二个字符比较小的那个字符串就比较小。

3.如果两个字符串的第二个字符也相同，那就接着比较第三个字符，依此类推
所以这样是可以用到索引：

`SELECT * FROM person_info WHERE name LIKE 'As%';`

但是这样就用不到：

`SELECT * FROM person_info WHERE name LIKE '%As%';`

#### 6.覆盖索引#

如果我们查询的所有列都可以在索引中找到，那么就可以就不需要回表去查找对应的列了。
例如：

`SELECT name, birthday, phone_number FROM person_info WHERE name > 'Asa' AND name < 'Barlow'`

因为我们只查询name, birthday, phone_number这三个索引列的值，所以在通过idx_name_birthday_phone_number索引得到结果后就不必到聚簇索引中再查找记录的剩余列，也就是country列的值了，这样就省去了回表操作带来的性能损耗。

#### 7.让索引列在比较表达式中单独出现

假设表中有一个整数列my_col，我们为这个列建立了索引。下边的两个WHERE子句虽然语义是一致的，但是在效率上却有差别：

`WHERE my_col * 2 < 4`

`WHERE my_col < 4/2`

第1个WHERE子句中my_col列并不是以单独列的形式出现的，而是以my_col * 2这样的表达式的形式出现的，存储引擎会依次遍历所有的记录，计算这个表达式的值是不是小于4。

所以这种情况下是使用不到为my_col列建立的B+树索引的。而第2个WHERE子句中my_col列并是以单独列的形式出现的，这样的情况可以直接使用B+树索引。

## 页分裂带来的性能损耗

我们假设一个页中只能存储5条数据：

![1](/images/mysql/index1.png);

如果这时候我插入一条id为4的数据，那么我们就要在分配一个新页。由于5>4,索引是有序的，所以需要将id=5这条数据移动到下一页中，并插入一条id=4新的数据到页10中：

![2](/images/mysql/index2.png);

#### 8. 减少对行锁的时间

两阶段锁协议：
在InnoDB事务中，行锁是在需要的时候才加上的，但并不是不需要了就立刻释放，而是要等到事务结束时才释放。

所以，如果你的事务中需要锁多个行，要把最可能造成锁冲突、最可能影响并发度的锁尽量往后放。

假设你负责实现一个电影票在线交易业务，顾客A要在影院B购买电影票。我们简化一点，这个业务需要涉及到以下操作：

从顾客A账户余额中扣除电影票价；
给影院B的账户余额增加这张电影票价；
记录一条交易日志。
也就是说，要完成这个交易，我们需要update两条记录，并insert一条记录。当然，为了保证交易的原子性，我们要把这三个操作放在一个事务中。

试想如果同时有另外一个顾客C要在影院B买票，那么这两个事务冲突的部分就是语句2了。因为它们要更新同一个影院账户的余额，需要修改同一行数据。

根据两阶段锁协议，不论你怎样安排语句顺序，所有的操作需要的行锁都是在事务提交的时候才释放的。所以，如果你把语句2安排在最后，比如按照3、1、2这样的顺序，那么影院账户余额这一行的锁时间就最少。这就最大程度地减少了事务之间的锁等待，提升了并发度。

#### 9.count函数优化

我们主要来看看count(*)、count(主键id)、count(字段)和count(1)这三者的性能差别。

对于count(主键id)来说，InnoDB引擎会遍历整张表，把每一行的id值都取出来，返回给server层。server层拿到id后，判断是不可能为空的，就按行累加。

对于count(1)来说，InnoDB引擎遍历整张表，但不取值。server层对于返回的每一行，放一个数字“1”进去，判断是不可能为空的，按行累加。

单看这两个用法的差别的话，你能对比出来，count(1)执行得要比count(主键id)快。因为从引擎返回id会涉及到解析数据行，以及拷贝字段值的操作。

对于count(字段)来说：

如果这个“字段”是定义为not null的话，一行行地从记录里面读出这个字段，判断不能为null，按行累加；
如果这个“字段”定义允许为null，那么执行的时候，判断到有可能是null，还要把值取出来再判断一下，不是null才累加。
也就是前面的第一条原则，server层要什么字段，InnoDB就返回什么字段。

但是count()是例外，并不会把全部字段取出来，而是专门做了优化，不取值。count()肯定不是null，按行累加。

所以结论是：按照效率排序的话，count(字段)<count(主键id)<count(1)≈count()，所以我建议你，尽量使用count()。

#### 10.order by性能优化

在MySQL排序中会用到内存来进行排序，`sort_buffer_size`，就是MySQL为排序开辟的内存`（sort_buffer）`的大小。如果要排序的数据量小于`sort_buffer_size`，排序就在内存中完成。但如果排序数据量太大，内存放不下，则不得不利用磁盘临时文件辅助排序。

如果查询要返回的字段很多的话，那么sort_buffer里面要放的字段数太多，这样内存里能够同时放下的行数很少，要分成很多个临时文件，排序的性能会很差。MySQL就会根据max_length_for_sort_data参数来限定排序的行数据的长度，如果单行的长度超过这个值，MySQL就认为单行太大，要根据rowid排序。

rowid排序只会在sort_buffer放入要排序的字段，减少要排序的数据的大小，但是rowid排序会多访问一次主键索引，多一次回表以便拿到需要返回的数据。

所以我们在写排序SQL的时候，需要尽量做到以下三点：

1.返回的数据列数尽量的少，不要返回不必要的数据列。

2.因为索引天然是有序的，所以如果要排序的列如果有必要的话，可以设置成索引，那么就不需要在sort_buffer中排序就可以直接返回了。

3.如果有必要的话可以使用覆盖索引，这样在返回数据的时候连通过主键回表都不需要做就可以直接查询得到数据。

#### 11.隐式类型转换#

例如：

`mysql> select * from tradelog where tradeid=110717;`
在这条sql中，交易编号tradeid这个字段上，本来就有索引，但是explain的结果却显示，这条语句需要走全表扫描。你可能也发现了，tradeid的字段类型是varchar(32)，而输入的参数却是整型，所以需要做类型转换。

因为在MySQL中，字符串和数字做比较的话，是将字符串转换成数字。所以上面的SQL相当于：


`mysql> select * from tradelog where  CAST(tradid AS signed int) = 110717;`

所以这条包含了隐式类型转换的SQL是无法走树搜索功能的。

#### 12.隐式字符编码转换

例如：

`mysql> select d.* from tradelog l, trade_detail d where d.tradeid=l.tradeid and l.id=2; /*语句Q1*/`

在这条SQL中，如果tradelog表的字符集编码是utf8mb4，trade_detail表的字符集编码是utf8，那么也是无法走索引的。

因为在这个SQL中，我们跑执行计划可以发现tradelog是驱动表，trade_detail是被驱动表，也就是从tradelog表中取tradeid字段，再去trade_detail表里查询匹配字段。

字符集utf8mb4是utf8的超集，所以当这两个类型的字符串在做比较的时候，MySQL内部的操作是，先把utf8字符串转成utf8mb4字符集，再做比较。

因此， 在执行上面这个语句的时候，需要将被驱动数据表里的字段一个个地转换成utf8mb4。所以是无法走索引的。

所以我们可以如下优化：

把trade_detail表上的tradeid字段的字符集也改成utf8mb4

`alter table trade_detail modify tradeid varchar(32) CHARACTER SET utf8mb4 default null;`

修改SQL语句

`mysql> select d.* from tradelog l , trade_detail d where d.tradeid=CONVERT(l.tradeid USING utf8) and l.id=2; `

#### 13.Join优化


在关联字段上使用索引
如：
我这里有两个表，t1和t2，表结果一模一样，字段a是索引字段

`select * from t1 straight_join t2 on (t1.a=t2.a);`

这样关联的数据执行逻辑就是：
1. 从表t1中读入一行数据 R；
2. 从数据行R中，取出a字段到表t2里去查找；
3. 取出表t2中满足条件的行，跟R组成一行，作为结果集的一部分；
4. 重复执行步骤1到3，直到表t1的末尾循环结束。

这个SQL由于使用了索引，所以在将t1表数据取出来后根据t1表的a字段实际上是对t2表的一个索引的等值查找，所以t1和t2比较的行数是相同的，这样使用被驱动表的索引关联称之为“Index Nested-Loop Join”，简称NLJ。

由于是驱动表t1去匹配被驱动表t2，那么匹配次数取决于t1有多少数据，所以在用索引关联的时候还需要注意，最好使用数据量少的表作为驱动表。

使用join_buffer来进行关联
如果我们将sql改成如下（在t2表中b字段是无索引的）：

`select * from t1 straight_join t2 on (t1.a=t2.b);`

这时候，被驱动表上没有可用的索引，算法的流程是这样的：

1. 把表t1的数据读入线程内存join_buffer中，由于我们这个语句中写的是select *，因此是把整个表t1放入了内存；
2. 扫描表t2，把表t2中的每一行取出来，跟join_buffer中的数据做对比，满足join条件的，作为结果集的一部分返回。

join_buffer的大小是由参数join_buffer_size设定的，默认值是256k。如果放不下表t1的所有数据话，策略很简单，就是分段放。如果分段放的话，那么被驱动表就要扫描多次，那么就会有性能问题。

所以如果join_buffer_size放不下的话就要使用小表作为驱动表，减少分段放的次数，在决定哪个表做驱动表的时候，应该是两个表按照各自的条件过滤，过滤完成之后，计算参与join的各个字段的总数据量，数据量小的那个表，就是“小表”，应该作为驱动表。

## 总结

很详细了，有需要再补充吧。
