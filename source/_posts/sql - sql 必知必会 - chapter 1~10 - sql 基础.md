---
title: SQL - SQL 必知必会 - chapter 1~10 - SQL 基础
categories: 
 - SQL
tags: 
 - SQL
---

SQL 基础知识。

<!--more-->

## 名词概念

**SQL**

structure query language 结构化查询语言

**ANSI SQL**

标准 SQL 由 ANSI 管理，从而称为 ANSI SQL 。所有主要的 DBMS ，即使有自己的扩展，也都支持 ANSI SQL 。各个实现有自己的名称，如 PL/SQL 、 Transact-SQL  等。

ANSI 美国国家标准学会（ American National Standards Institute）

**schema 模式**

关于数据库和表的布局及特性的信息。

表具有一些特性，这些特性定义了数据在表中如何存储，包含存储什么样的数据，数据如何分解，各部分信息如何命名等信息。

描述表的这组信息就是所谓的 模式（ schema ），模式可以用来描述数据库中特定的表，也可以用来描述整个数据库（和其中表的关系）。

**数据类型兼容性**

大多数的基本数据类型都得到了一致的支持，但许多高级数据类型并没有并一致支持，且有相同的数据类型在不同的 DBMS 里有不同的名称。

**行和记录**

行也可称为记录，但从技术上来说，行才是正确的术语。

**列和字段**

字段（ field ）基本上与 列（ column ）的意思相同，经常互换使用，不过数据库列一般称为列，而字段一般用来称呼经过计算的列，即计算字段

**主键**

主键用来唯一的标识表中的每一行，可以是一列或几列的组合

主键列的值不能修改更新，如果某行从表中删除，则它的主键值不能重用，即不能赋给后面的新行  ??? 确认下

**子句**

sql 语句由子句 clause 组成，有可选的有必需的，子句通常由关键字和数据组成。

## 结束分号

多个 sql 语句用分号分隔，单条 sql 语句有的 DBMS 也要求分号结束，建议每个 sql 语句后都加分号。

## 大小写

sql 语句的关键字不区分大小写，但 sql 语句中的表名、数据库名、列名等是否区分大小写要看具体 DBMS 的设置。

建议 sql 语句里关键字大写，表名、列名小写，清晰方便。

mysql
* 库名、表名区分大小写
* 列名不区分大小写
* 函数名不区分大小写

## 空白字符

sql 语句中的空白字符被忽略

```sql
SELECT prod_name
FROM Products;

SELECT prod_name FROM Products;

SELECT
prod_name
FROM
Products;
```

## distinct

distinct 不是仅对后面的第一个列起作用，而是对列在后面的每一个列都起作用

```sql
select distinct id,uname from wanjia;
```
* 表示 id 和 uname 的组合不重复

## 限制输出结果

只输出匹配结果的前几行，各 DBMS 的实现不一样

```sql
--sql server
select top 5 * from wanjia;

--db2
select * from wanjia fetch first 5 rows only;

--oracle
select * from wanjia where rownum <= 5;

-- MySQL、MariaDB、PostgreSQL、SQLite
select * from wanjia limit 5;

select * from wanjia limit 5 offset 2;

select * from wanjia limit 5,2;
```

## 注释

```sql
-- 单行注释

# 单行注释 

/* 
   多行 
   注释
*/
```
* 上面 3 种注释 mysql 都支持

## 排序

使用 order by 子句排序，且 order by 子句出现的话就应该是 selelct 的最后一条子句，否则出错

没有明确使用排序子句 order by 时，检索出的数据的顺序并不是随机的，而是以数据在底层表中的顺序显示，可能是数据最初添加到表中的顺序，但是数据被更新删除后，顺序则受到 DBMS 回收重用空间策略的影响。

默认升序排序 asc ascending ，降序 desc descending ，desc 只对紧随其后的第一个列其作用，如果多个列要降序则必须每个列都加上 desc
```sql
mysql> select * from wanjia order by addr desc,credits;
+------+---------+-----------+---------+
| id   | uname   | addr      | credits |
+------+---------+-----------+---------+
|    1 | ssl     | handan    |       3 |
|    5 | shenlin | handan    |      12 |
| NULL | mxx     | changshan |      12 |
|    2 | ssy     | beijing   |       6 |
|    3 | gxx     | baoding   |       9 |
+------+---------+-----------+---------+
5 rows in set (0.01 sec)

mysql> select * from wanjia order by addr desc,credits desc;
+------+---------+-----------+---------+
| id   | uname   | addr      | credits |
+------+---------+-----------+---------+
|    5 | shenlin | handan    |      12 |    -- 这里 addr 相同，则按 credits 排序
|    1 | ssl     | handan    |       3 |
| NULL | mxx     | changshan |      12 |
|    2 | ssy     | beijing   |       6 |    -- 这里按 addr 排序
|    3 | gxx     | baoding   |       9 |
+------+---------+-----------+---------+
```

可以按列的位置排序，下面的排序等同于 order by id desc
```sql
mysql> select * from wanjia order by 1 desc;
+------+---------+-----------+---------+
| id   | uname   | addr      | credits |
+------+---------+-----------+---------+
|    5 | shenlin | handan    |      12 |
|    3 | gxx     | baoding   |       9 |
|    2 | ssy     | beijing   |       6 |
|    1 | ssl     | handan    |       3 |
| NULL | mxx     | changshan |      12 |
+------+---------+-----------+---------+
5 rows in set (0.01 sec)
```
* 缺点
    * 不明确排序列名可能使用了错误的列名排序
    * 对 select 清单进行更改时忘记修改排序子句则可能使用了错误的列名排序
    * 不能使用不在 select 清单里的列名排序
* 优点
    * 不用再写一遍列名

大小写的排序问题，如 A、a、z、Z 的排序问题是数据库的设置问题，不是 order by 子句的问题，大部分的 DBMS 的字典排序都把 a 和 A 视为相同 ???

## where 子句操作符

并非所有的 DBMS 都支持下列全部的操作符，但同时许多 DBMS 又扩展了标准的操作符集，具体支持情况需查阅 DBMS 帮助：

https://dev.mysql.com/doc/refman/5.7/en/comparison-operators.html

`=` 等于

`<>` 不等于
`!=` 不等于

`<` 小于
`<=` 小于等于

`>` 大于
`>=` 大于等于

`!<` 不小于 （mysql 不支持）
`!>` 不大于 （mysql 不支持）

`BETWEEN a AND b` 在指定的两个值之间，包含了起始点和终止点

`IS NULL` 为 NULL 值

`IS NOT NULL`

值为 NULL 的列在判断时必须用 `is null` `is not null` 过滤，使用 `id=2 or id<>2` 都不包含值为 NULL 的列 :
```sql
mysql> select * from wanjia;
+------+-------+-----------+---------+
| id   | uname | addr      | credits |
+------+-------+-----------+---------+
|    1 | ssl   | handan    |       3 |
|    2 | ssy   | beijing   |       6 |
|    3 | gxx   | baoding   |       9 |
| NULL | mxx   | changshan |      12 |
+------+-------+-----------+---------+
4 rows in set (0.01 sec)

mysql> select * from wanjia where id=2 or id <> 2;
+------+-------+---------+---------+
| id   | uname | addr    | credits |
+------+-------+---------+---------+
|    1 | ssl   | handan  |       3 |
|    2 | ssy   | beijing |       6 |
|    3 | gxx   | baoding |       9 |
+------+-------+---------+---------+
3 rows in set (0.00 sec)
```

## 组合 where 子句

and , or , in, not

IN 操作符通常比一组 OR 操作符执行的更快

NOT 是用来否定其后所跟任何条件的关键字，NOT 在复杂的 sql 语句中使用很方便，大多数 DBMS 支持使用 NOT 否定任何条件

```sql
mysql> select * from wanjia where NOT id is null;
+------+---------+---------+---------+
| id   | uname   | addr    | credits |
+------+---------+---------+---------+
|    1 | ssl     | handan  |       3 |
|    2 | ssy     | beijing |       6 |
|    3 | gxx     | baoding |       9 |
|    5 | shenlin | handan  |      12 |
+------+---------+---------+---------+
4 rows in set (0.01 sec)

mysql> select * from wanjia where NOT id in (1,2,3);
+------+---------+--------+---------+
| id   | uname   | addr   | credits |
+------+---------+--------+---------+
|    5 | shenlin | handan |      12 |
+------+---------+--------+---------+
1 row in set (0.02 sec)

mysql> select * from wanjia where NOT id = 3;
+------+---------+---------+---------+
| id   | uname   | addr    | credits |
+------+---------+---------+---------+
|    1 | ssl     | handan  |       3 |
|    2 | ssy     | beijing |       6 |
|    5 | shenlin | handan  |      12 |
+------+---------+---------+---------+
3 rows in set (0.01 sec)
```

## 通配符

`%` 匹配任何字符任何次数

`_` 下划线匹配单个字符

`[abc]` 匹配单个字符 a 或 b 或 c，mysql 不支持

```sql
mysql> select * from wanjia where uname like 'ss_';
+------+-------+---------+---------+
| id   | uname | addr    | credits |
+------+-------+---------+---------+
|    1 | ssl   | handan  |       3 |
|    2 | ssy   | beijing |       6 |
+------+-------+---------+---------+
2 rows in set (0.00 sec)
```

通配符尽量不要放到搜索模式的开始处，否则速度是最慢的

## 拼接字段

Access 、 SQLServer 使用 `+` 拼接
```sql
select id + name from wanjia;
```

PostgreSQL 、 SQLite 使用 `||` 拼接
```sql
select id || name from wanjia;
```

MySQL 需要使用函数
```sql
mysql> select concat(id,'--',uname) from wanjia;
+-----------------------+
| concat(id,'--',uname) |
+-----------------------+
| 1--ssl                |
| 2--ssy                |
| 3--gxx                |
| NULL                  |
+-----------------------+
4 rows in set (0.00 sec)
```

## 别名

别名有时也被称为导出列 derived column

别名使用关键字 AS， 许多 DBMS 中关键字 AS 是可选的，但建议使用。

在指定别名时，不应该使用表中实际的列名。虽然这样做也算合法，但许多 SQL 实现不支持，可能会产生模糊的错误消息。

```sql
mysql> select concat(id,'--',uname) iname from wanjia;
+--------+
| iname  |
+--------+
| 1--ssl |
| 2--ssy |
| 3--gxx |
| NULL   |
+--------+
4 rows in set (0.02 sec)

mysql> select concat(id,'--',uname) as iname from wanjia;
+--------+
| iname  |
+--------+
| 1--ssl |
| 2--ssy |
| 3--gxx |
| NULL   |
+--------+
4 rows in set (0.02 sec)
```

## 函数

与几乎所有 DBMS 都等同地支持 SQL 语句（如SELECT）不同，每一个 DBMS 都有特定的函数，所以 SQL 函数是不可移植的。如 PostgreSQL 和 SQLite 使用 SUBSTR() ； MySQL 和 SQL Server 使用 SUBSTRING() 等

大多数 DBMS 支持以下类型的函数：

* 文本字符串函数 : 
    * left,rihgt
    * rtrim,ltrim
    * lower,upper
    * length
    * soundex
    * soundex 是把文本字符串的发音用字母数字来表示，可以对字符串进行发音比较，模糊匹配
* 日期时间函数，是可移植性最差的一类函数，各 DBMS 实现差异很大，如提取年份的函数就有：
    * SQL Server : DATEPART(yy, order_date)
    * PostgreSQL : DATE_PART('year', order_date)
    * Oracle : to_number(to_char(order_date, 'YYYY'))
    * MySQL : YEAR(order_date)
    * SQLite : strftime('%Y', order_date)
* 数值处理函数，使用最少，但却是各 DBMS 最统一、最一致的一类函数
    * ABS() 返回一个数的绝对值
    * SIN() 返回一个角度的正弦
    * COS() 返回一个角度的余弦
    * EXP() 返回一个数的指数值
    * SQRT() 返回一个数的平方根
    * TAN() 返回一个角度的正切
    * PI() 返回圆周率
* 系统函数

soundex 模糊匹配拼错的单词
```sql
mysql> select * from wanjia where uname = 'shenln';
Empty set (0.02 sec)

mysql> select * from wanjia where soundex(uname) = soundex('shenln');
+------+---------+--------+---------+
| id   | uname   | addr   | credits |
+------+---------+--------+---------+
|    5 | shenlin | handan |      12 |
+------+---------+--------+---------+
1 row in set (0.01 sec)
```

## 聚集函数

聚集函数 Aggregate Function，可以汇总数据，包括：
* MAX() 返回某列的最大值，忽略值为 NULL 的列，也可用于文本列返回最大值
* MIN() 返回某列的最小值，忽略值为 NULL 的列，也可用于文本列返回最小值
* SUM() 返回某列值之和，忽略值为 NULL 的列
* AVG() 返回某列的平均值，忽略值为 NULL 的列
* COUNT() 返回行数

count(*)返回表中记录的总行数，count(某一列) 则返回此列值不为 NULL 的总行数
```sql
mysql> select count(id) from wanjia;
+-----------+
| count(id) |
+-----------+
|         4 |
+-----------+
1 row in set (0.00 sec)

mysql> select count(*) from wanjia;
+----------+
| count(*) |
+----------+
|        5 |
+----------+
1 row in set (0.00 sec)
```

min,max 用于文本列
```sql
mysql> select uname from wanjia order by uname;
+---------+
| uname   |
+---------+
| gxx     |
| mxx     |
| shenlin |
| ssl     |
| ssy     |
+---------+
5 rows in set (0.02 sec)

mysql> select min(uname) from wanjia;
+------------+
| min(uname) |
+------------+
| gxx        |
+------------+
1 row in set (0.03 sec)

mysql> select max(uname) from wanjia;
+------------+
| max(uname) |
+------------+
| ssy        |
+------------+
1 row in set (0.01 sec)
```

聚集函数可以和 distinct 或 all 参数搭配，默认是 all 参数
```sql
mysql> select credits from wanjia;
+---------+
| credits |
+---------+
|       3 |
|       6 |
|       9 |
|      12 |
|      12 |
+---------+
5 rows in set (0.02 sec)

-- 和 avg 搭配

mysql> select avg(distinct credits) from wanjia;
+-----------------------+
| avg(distinct credits) |
+-----------------------+
|                7.5000 |
+-----------------------+
1 row in set (0.00 sec)

mysql> select avg(all credits) from wanjia;         -- 使用 all 和不使用 all 参数效果是一样的
+------------------+
| avg(all credits) |
+------------------+
|           8.4000 |
+------------------+
1 row in set (0.00 sec)

mysql> select avg(credits) from wanjia;
+--------------+
| avg(credits) |
+--------------+
|       8.4000 |
+--------------+
1 row in set (0.01 sec)

-- 和 count 搭配

mysql> select count(credits) from wanjia;
+----------------+
| count(credits) |
+----------------+
|              5 |
+----------------+
1 row in set (0.01 sec)

mysql> select count(distinct credits) from wanjia;
+-------------------------+
| count(distinct credits) |
+-------------------------+
|                       4 |
+-------------------------+
1 row in set (0.00 sec)

mysql> select count(distinct *) from wanjia;                -- distinct 不能和 count(*) 搭配
ERROR 1064 (42000): You have an error in your SQL syntax;

-- 和 sum, min, max 搭配

mysql> select sum(distinct credits) from wanjia;
+-----------------------+
| sum(distinct credits) |
+-----------------------+
|                    30 |
+-----------------------+
1 row in set (0.02 sec)

mysql> select max(distinct credits) from wanjia;
+-----------------------+
| max(distinct credits) |
+-----------------------+
|                    12 |
+-----------------------+
1 row in set (0.00 sec)

mysql> select min(distinct credits) from wanjia;
+-----------------------+
| min(distinct credits) |
+-----------------------+
|                     3 |
+-----------------------+
1 row in set (0.01 sec)
```

## 分组数据

使用 GROUP BY 和 HAVING 可以把数据分为多个逻辑组，然后对每个逻辑组进行聚集计算。

GROUP BY 注意事项：
* GROUP BY 子句必须在 where 子句之后 order by 子句之前：`select addr,count(id) from wanjia where id < 9 GROUP BY addr order by addr`
* 除了聚集计算语句外，select 语句中的每一列都必须在 GROUP BY 子句中
* 如果在SELECT中使用表达式，则必须在GROUP BY子句中指定相同的表达式，不能使用别名。???

测试 mysql 可以在 group by 使用别名
```sql
mysql> select concat(addr,'xxx') as newaddr,sum(credits) as Cnt from wanjia group by newaddr having Cnt > 9;
+--------------+------+
| newaddr      | Cnt  |
+--------------+------+
| changshanxxx |   12 |
| handanxxx    |   15 |
+--------------+------+
2 rows in set (0.00 sec)
```

```sql
mysql> select addr,sum(credits) from wanjia GROUP BY addr;
+-----------+--------------+
| addr      | sum(credits) |
+-----------+--------------+
| baoding   |            9 |
| beijing   |            6 |
| changshan |           12 |
| handan    |           15 |
+-----------+--------------+
4 rows in set (0.04 sec)

mysql> select addr,sum(credits) from wanjia group by 1; -- 通过相对位置指定列，搜索列从 1 开始，不推荐使用
+-----------+--------------+
| addr      | sum(credits) |
+-----------+--------------+
| baoding   |            9 |
| beijing   |            6 |
| changshan |           12 |
| handan    |           15 |
+-----------+--------------+
4 rows in set (0.01 sec)
```

where 子句在分组前过滤数据，Having 子句则在分组后过滤数据，即用于过滤分组数据

having 子句十分类似 where 子句，没有和 group by 搭配时大多数 DBMS 会同等对待两者，即可以这样执行：
```sql
mysql> select * from wanjia having id = 1;
+------+-------+--------+---------+
| id   | uname | addr   | credits |
+------+-------+--------+---------+
|    1 | ssl   | handan |       3 |
+------+-------+--------+---------+
1 row in set (0.00 sec)
```
* 虽然可执行，但 where 子句才是标准的行级过滤，having 还是应和 group by 搭配

having 和 group by 搭配过滤分组数据
```sql
mysql> select addr,sum(credits) from wanjia group by addr having sum(credits) > 9;
+-----------+--------------+
| addr      | sum(credits) |
+-----------+--------------+
| changshan |           12 |
| handan    |           15 |
+-----------+--------------+
2 rows in set (0.01 sec)
```

having 可以使用别名
```sql
mysql> select addr,sum(credits) as Cnt from wanjia group by addr having Cnt > 9;
+-----------+------+
| addr      | Cnt  |
+-----------+------+
| changshan |   12 |
| handan    |   15 |
+-----------+------+
2 rows in set (0.01 sec)
```

## 子句顺序

`select ... from ... where ... group by ... having ... order by ...`

## 子查询

子查询是嵌套在其他查询中的查询，子查询总是从内向外处理

```sql
mysql> select * from wanjia where id in (select uid from wanwu where id = 1);
+------+-------+--------+---------+
| id   | uname | addr   | credits |
+------+-------+--------+---------+
|    1 | ssl   | handan |       3 |
+------+-------+--------+---------+
1 row in set (0.03 sec)

mysql> select * from wanjia where id in (select uid,wname from wanwu where id = 1);
ERROR 1241 (21000): Operand should contain 1 column(s)

mysql> select *,(select count(*) from wanwu where wanwu.uid = wanjia.id) as wnum from wanjia;
+------+---------+-----------+---------+------+
| id   | uname   | addr      | credits | wnum |
+------+---------+-----------+---------+------+
|    1 | ssl     | handan    |       3 |    1 |
|    2 | ssy     | beijing   |       6 |    0 |
|    3 | gxx     | baoding   |       9 |    0 |
| NULL | mxx     | changshan |      12 |    0 |
|    5 | shenlin | handan    |      12 |    0 |
+------+---------+-----------+---------+------+
5 rows in set (0.01 sec)
```
* 子查询只能返回一个列，返回多个列时出错

## 联结查询

**可伸缩（ scale ）**

能够适应不断增加的工作量而不失败。设计良好的数据库或应用程序称为可伸缩性好（ scale well ）

**关系数据库**

关系数据库由关系表组成，关系表的设计就是要把信息分解成多个表，一类数据一个表。各表通过某些共同的值互相关联。

**完全限定名**

一个查询中涉及多个表有相同的列名时，使用`表名.列名`的格式来

**笛卡尔积**

cartesian product

**联结**

将数据分解为多个表能更有效地存储，更方便地处理，并且可伸缩性更好

如果数据存储在多个表中，怎样用一条SELECT语句就检索出数据呢？

方法就是使用联结

### 内联结

也称为等值联结 equijoin，基于两个表之间的相等测试

内联结有两种语法，简单语法和标准语法，ANSI SQL规范首先的是标准语法
```sql
-- 简单语法
mysql> select * from wanjia,wanwu where wanjia.id = wanwu.uid;
+------+-------+--------+---------+------+------+-------+--------------------+
| id   | uname | addr   | credits | id   | uid  | wname | wdesc              |
+------+-------+--------+---------+------+------+-------+--------------------+
|    1 | ssl   | handan |       3 |    1 |    1 | sword | sharp light weapon |
+------+-------+--------+---------+------+------+-------+--------------------+
1 row in set (0.02 sec)

-- 标准语法 
mysql> select * from wanjia inner join wanwu on wanjia.id = wanwu.uid;
+------+-------+--------+---------+------+------+-------+--------------------+
| id   | uname | addr   | credits | id   | uid  | wname | wdesc              |
+------+-------+--------+---------+------+------+-------+--------------------+
|    1 | ssl   | handan |       3 |    1 |    1 | sword | sharp light weapon |
+------+-------+--------+---------+------+------+-------+--------------------+
1 row in set (0.01 sec)
```

**表别名**

联结查询时可以给表取别名，即可以缩短 sql，还可以对一个表使用多次

```sql
mysql> select * from wanjia as j,wanjia as i,wanwu as w where j.id = w.uid and w.uid = i.id;
+------+-------+--------+---------+------+-------+--------+---------+------+------+-------+--------------------+
| id   | uname | addr   | credits | id   | uname | addr   | credits | id   | uid  | wname | wdesc              |
+------+-------+--------+---------+------+-------+--------+---------+------+------+-------+--------------------+
|    1 | ssl   | handan |       3 |    1 | ssl   | handan |       3 |    1 |    1 | sword | sharp light weapon |
+------+-------+--------+---------+------+-------+--------+---------+------+------+-------+--------------------+
1 row in set (0.03 sec)
```
* 列别名会返回到客户端，表别名不会

**笛卡尔积**

联结时 where 子句很重要，否则没有条件的联结表查询返回的是笛卡尔积（也称为叉联结 cross join），
即把第一个表的每一行和第二个表的每一行匹配，行数是第一个表的行数乘以第二个表的行数
```sql
mysql> select * from wanjia,wanwu;
+------+---------+-----------+---------+------+------+-------+--------------------+
| id   | uname   | addr      | credits | id   | uid  | wname | wdesc              |
+------+---------+-----------+---------+------+------+-------+--------------------+
|    1 | ssl     | handan    |       3 |    1 |    1 | sword | sharp light weapon |
|    2 | ssy     | beijing   |       6 |    1 |    1 | sword | sharp light weapon |
|    3 | gxx     | baoding   |       9 |    1 |    1 | sword | sharp light weapon |
| NULL | mxx     | changshan |      12 |    1 |    1 | sword | sharp light weapon |
|    5 | shenlin | handan    |      12 |    1 |    1 | sword | sharp light weapon |
+------+---------+-----------+---------+------+------+-------+--------------------+
5 rows in set (0.03 sec)
```

**自联结**

一个联结查询中关联的两个表是同一个表的情况称为自联结，通常自联结比子查询要快
```sql
-- 自联结
mysql> select j.* from wanjia w, wanjia j where w.addr = j.addr and w.id = 1 and w.id <> j.id;
+------+---------+--------+---------+
| id   | uname   | addr   | credits |
+------+---------+--------+---------+
|    5 | shenlin | handan |      12 |
+------+---------+--------+---------+
1 row in set (0.02 sec)

-- 子查询
mysql> select * from wanjia where addr = (select addr from wanjia where id = 1) and id <> 1;
+------+---------+--------+---------+
| id   | uname   | addr   | credits |
+------+---------+--------+---------+
|    5 | shenlin | handan |      12 |
+------+---------+--------+---------+
1 row in set (0.01 sec)
```

**自然联结**

联结查询中，相联结的列在两个表中会重复出现，则 select 列时，对一个表用通配符 `*`，对列一个表则指定列名避免重复列出现就是自然联结
```sql
mysql> select wanjia.*,wanwu.wname from wanjia,wanwu where wanjia.id=wanwu.uid;
+------+-------+--------+---------+-------+
| id   | uname | addr   | credits | wname |
+------+-------+--------+---------+-------+
|    1 | ssl   | handan |       3 | sword |
+------+-------+--------+---------+-------+
1 row in set (0.02 sec)
```

**搭配聚合函数**

???测试失败
```sql
mysql> select wanjia.*,count(wanwu.id) from wanjia inner join wanwu on wanjia.id = wanwu.uid group by wanwu.uid;

ERROR 1055 (42000): Expression #2 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'wanzhu.wanjia.uname' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```

### 外联结

联结查询中涉及到多表关联查询，例如 A 表中 3 条数据，相关联的 B 表中缺少 1 条对应数据，只有 2 条对应数据，
查询结果只包含两个表相关联数据的称为内联结，查询结果包含了一个表中全部数据而不论另一个表是否有对应数据的称为外联结，
包含左边表数据的称为左外联结 left outer join，
包含右边表数据的称为右外联结  right outer join，
包含左、右两个表数据的称为全外联结 full outer join，mysql 不支持全外联结

```sql
-- 内联结
mysql> select * from wanjia inner join wanwu on  wanjia.id = wanwu.uid;
+------+-------+--------+---------+------+------+-------+--------------------+
| id   | uname | addr   | credits | id   | uid  | wname | wdesc              |
+------+-------+--------+---------+------+------+-------+--------------------+
|    1 | ssl   | handan |       3 |    1 |    1 | sword | sharp light weapon |
+------+-------+--------+---------+------+------+-------+--------------------+
1 row in set (0.00 sec)

-- 左外联结
mysql> select * from wanjia left outer join wanwu on  wanjia.id = wanwu.uid;
+------+---------+-----------+---------+------+------+-------+--------------------+
| id   | uname   | addr      | credits | id   | uid  | wname | wdesc              |
+------+---------+-----------+---------+------+------+-------+--------------------+
|    1 | ssl     | handan    |       3 |    1 |    1 | sword | sharp light weapon |
|    2 | ssy     | beijing   |       6 | NULL | NULL | NULL  | NULL               |
|    3 | gxx     | baoding   |       9 | NULL | NULL | NULL  | NULL               |
| NULL | mxx     | changshan |      12 | NULL | NULL | NULL  | NULL               |
|    5 | shenlin | handan    |      12 | NULL | NULL | NULL  | NULL               |
+------+---------+-----------+---------+------+------+-------+--------------------+
5 rows in set (0.00 sec)

-- 右外联结
mysql> select * from wanjia right outer join wanwu on  wanjia.id = wanwu.uid;
+------+-------+--------+---------+------+------+-------+--------------------+
| id   | uname | addr   | credits | id   | uid  | wname | wdesc              |
+------+-------+--------+---------+------+------+-------+--------------------+
|    1 | ssl   | handan |       3 |    1 |    1 | sword | sharp light weapon |
+------+-------+--------+---------+------+------+-------+--------------------+
1 row in set (0.02 sec)

-- 全外联结
mysql 不支持
```

## 组合查询

sql 允许多个 select 查询的结果作为一个返回，可以使用 union 操作符来完成

union 连接的两个 select 查询列名、表达式、别名或聚集函数必须相同，但顺序可以不同；列的数据类型可以不完全相同，但要兼容

组合查询可以多条 select 语句查询一个表，也可以查询多个表，查询同一个表时总是可以使用 where 子句替换，

但到底是 union 查询效率更高还是 where 子句，需要针对具体 DBMS 测试

union 默认是 `union distinct` 不包含重复的行，`union all` 则包含了重复行

```sql
mysql> select * from wanjia where id < 4 union select * from wanjia where id < 5;
+------+-------+---------+---------+
| id   | uname | addr    | credits |
+------+-------+---------+---------+
|    1 | ssl   | handan  |       3 |
|    2 | ssy   | beijing |       6 |
|    3 | gxx   | baoding |       9 |
+------+-------+---------+---------+
3 rows in set (0.00 sec)

mysql> select * from wanjia where id < 4 union distinct select * from wanjia where id < 5;
+------+-------+---------+---------+
| id   | uname | addr    | credits |
+------+-------+---------+---------+
|    1 | ssl   | handan  |       3 |
|    2 | ssy   | beijing |       6 |
|    3 | gxx   | baoding |       9 |
+------+-------+---------+---------+
3 rows in set (0.03 sec)

mysql> select * from wanjia where id < 4 union all select * from wanjia where id < 5;
+------+-------+---------+---------+
| id   | uname | addr    | credits |
+------+-------+---------+---------+
|    1 | ssl   | handan  |       3 |
|    2 | ssy   | beijing |       6 |
|    3 | gxx   | baoding |       9 |
|    1 | ssl   | handan  |       3 |
|    2 | ssy   | beijing |       6 |
|    3 | gxx   | baoding |       9 |
+------+-------+---------+---------+
6 rows in set (0.01 sec)
```

使用 union 连接的两个 select 语句只允许在末尾有一个 order by 子句，会排序所有 select 语句的结果
```sql
mysql> select * from wanjia where id < 4 order by id union select * from wanjia where id < 5;
ERROR 1221 (HY000): Incorrect usage of UNION and ORDER BY

mysql> select * from wanjia where id < 4 order by id union select * from wanjia where id < 5 order by id;
ERROR 1221 (HY000): Incorrect usage of UNION and ORDER BY

mysql> select * from wanjia where id < 4  union select * from wanjia where id < 5 order by id;
+------+-------+---------+---------+
| id   | uname | addr    | credits |
+------+-------+---------+---------+
|    1 | ssl   | handan  |       3 |
|    2 | ssy   | beijing |       6 |
|    3 | gxx   | baoding |       9 |
+------+-------+---------+---------+
3 rows in set (0.03 sec)
```

某些 DBMS 还支持另外两种 UNION：EXCEPT（有时称为MINUS）可用来检索只在第一个表中存在而在第二个表中不存在的行；而 INTERSECT 可用来检索两个表中都存在的行。实际上，这些UNION很少使用，因为相同的结果可利用联结得到。

## 插入

Insert 之后的 Into 关键字，在某些 DBMS 中是可选的，但建议保留以保证 sql 语句的可移植性。

基本的插入数据方法
```sql
insert into wanjia values(1,'ssl','handan',12);
```
* 高度依赖于表中列的定义次序，在表结构变动后不能保证次序完全相同，因此不安全

更安全的数据插入方法
```sql
insert into wanjia(id,uname,addr,credits) values(1,'ssl','handan',12);
```

插入部分列
```sql
insert into wanjia(id,uname,addr) values(1,'ssl','handan');
```

**insert select**

把检索出的数据插入一个 **已存在** 的表

```sql
mysql> insert into wanjia2 select * from wanjia;
Query OK, 5 rows affected (0.07 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> insert into wanjia222 select * from wanjia;
ERROR 1146 (42S02): Table 'wanzhu.wanjia222' doesn't exist
```

**select into**

把表的数据复制到另一个 **新** 表

```sql
mysql> create table wanjia3 as select * from wanjia;
Query OK, 5 rows affected (0.12 sec)
Records: 5  Duplicates: 0  Warnings: 0
```

## 更新

有的 DBMS 支持 update from 子句

mysql 是否支持 ???

## 删除

某些 DBMS 中 delete 后面的 from 是可选的，但建议加上方便移植性

delete 删除整行而不是整列所以不需要指定列名或通配符
```sql
delete from wanjia where id is null;
```

从表中删除所有行，truncate 速度更快，因为不记录数据的变动
```sql
truncate table wanjia2;
```

执行 update 或 delete 前应先使用 select 测试 where 子句选择的数据是否准确

## 操作表

### 创建表

```sql
create table tb_name (
    column1_name char(10) not null default '',
    column2_name char(10) not null default '',
);
```

### 修改表

```sql
alter table add new_column1 smallint;
alter table change column1_name column_new_name smallint;
alter table drop column column1_name;
```

### 删除表

```sql
drop table tb_name;
```

### 重命名表

```sql
rename table tb_name tb_new_name;
```

## 视图

**只读视图**

视图可以创建和读取，但内容不能更改，SQLite 只支持只读视图。

所有 DBMS 都支持的视图创建语法
```sql
create view vw_wanjia as
select * from wanjia
where not id is null;
```

## 存储过程

```sql
create procedure pd_s
as
select * from wanjia
where id is null; 

execute pd_s('id');
```

## 事务

```sql

```

## 游标


## 约束

### 主键约束

```sql
alter table tb_name
add constraint primary key on id;
get date();
current_date();
```

### 外键约束

```sql
alter table tb_name
```

### 唯一约束

```sql

```

### 检查约束

## 索引


## 触发器
