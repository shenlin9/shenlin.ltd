---
title: MySQL
categories: 
 - MySQL
tags: 
 - MySQL
---

MySQL

<!--more-->

## MySQL 和 mysql

MySQL 指的是整个 MySQL RDBMS，而 mysql 代表的是一个特定的客户机程序名

## GRANT

```sql
GRANT ALL ON samp_db.* TO paul@localhost IDENTIFIED BY "paul_password";
GRANT ALL ON samp_db.* TO paul@% IDENTIFIED BY "paul_password";
```

第一个命令在 paul 从 localhost（服务器运行的同一主机）连接时，允许它完全访问
samp_db 数据库及它的所有表。它还给出了一个口令 paul_password。

第二个命令与第一个类似，但允许 paul 从任何主机上连接（“%”为通配符）。也可以用
特定的主机名取代“%”，使 paul 只能从该主机上进行连接。

如果您的服务器允许从 localhost 匿名访问，由于服务器搜索授权表查找输入连接匹配的
方式的原因，这样一个GRANT 语句可能是必须的。

## 连接和退出

```bash
mysql -h host_name -u user_name -puser_password db_name
```
* -h host_name 或 --host=host_name
    * 登录的是本机 mysql 则可以省略 -h
* -u user_name 或 --user=user_name
    * unix 下登录的系统用户名和 mysql 用户名相同则可以省略 -u
    * windows 下缺省用户名是 ODBC
    * 还可以通过设置 USER 环境变量设置缺省用户名：set USER=paul
* -pYourPassword 或 --password=YourPassword 或 -p 回车在提示后输入
    * 回车提示输入密码，但不会显示，* 号也不显示，为的是安全，如果显示则同一个服
        务器上的其他用户可能通过`ps auxw`等命令看到密码
    * 将密码输入到命令行上过于危险，还是使用 `-p` 后面不加密码安全
    * -p 和密码之间一定不能有空格，其他的选项 -h -u 有或没有空格都可以
    * -p 后有空格和其他字符则会认为是要连接的数据库
    * 省略 -p 选项则 mysql 认为不需要密码，不会提示
    * MySQL 的密码长度没有限制，如果提示长度限制，则是因为调用的系统库(提示输入
        密码调用的是系统库)的问题，可以通过把密码放入选项文件来解决

有的 MySQL 安装允许匿名登录，则无需用户名
```bash
$ mysql
```

连接时指定用户
```bash
$ mysql -u root -p
```

连接时指定主机
```bash
$ mysql -h localhost -u root -p
```

连接时指定数据库
```bash
$ mysql -h localhost -u root -p test_db
$ mysql test_db
```
* 数据库名区分大小写

查看 mysql 版本
```sql
SELECT VERSION();
```

退出 mysql
```sql
quit
```
* quit 后的冒号可省略
* 或 ctrl + d

## 杂项

用大写字符表示 SQL 关键字和函数名，用小写字符表示数据库、表和列名
列名在 MySQL 中不区分大小写，数据库名和表名是否区分大小写取决于服务器的文件系统
，因此 Linux 区分 Windows 不区分

mysql 客户端命令提示符下需要见到分号`;`才会发送查询到服务器，以分号 `;` 或 `\g` 结尾表示一个完整查询
```sql
mysql> SELECT NOW(),
    -> USER(),
    -> VERSION()
    -> ;
```
* 在 PHP 等语言环境分号则不是必须的

如果已经开始键入一个多行的查询，而又不想立即执行它，可键入 '\c' 来放弃，如：
```sql
mysql> select NOW(),
    -> VERSION(),
    -> \c
mysql>
```

执行外部文件的 sql 语句
```bash
$ mysql samp_db < sql_file.sql
```

sql
```mysql
CREATE DATABASE samp_db;

CREATE DATABASE president(
    id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY,
    sex ENUM('F','M') NOT NULL,
    last_name VARCHAR(15) NOT NULL,
    first_name VARCHAR(15) NOT NULL,
    expiration DATE NULL DEFAULT '0000-00-00',
    birth DATE NOT NULL,
    death DATE NULL
);

CREATE TABLE absence(
    student_id INT UNSIGNED NOT NULL,
    date DATE NOT NULL,
    PRIMARY KEY (student_id,date)
);

SHOW DATABASES;

USE samp_db;

SELECT DATABASE();

SHOW TABLES;
```

查看表的列名、列数据类型、列宽、列顺序
```mysql
SHOW COLUMNS FROM president
DESCRIBE president;
```
* DESCRIBE 列的顺序和 MySQL 表中的顺序一致
* DESCRIBE 可缩写为 DESC ???

mysql 客户端命令 mysqlshow
```bash
$ mysqlshow                 # 等价于 SHOW DATABASES;
$ mysqlshow db_name         # 等价于 SHOW TABLES;
$ mysqlshow db_name tb_name # 等价于 DESCRIBE tb_name
```

```mysql
SELECT CONCAT(USER()," -- ",VERSION()) as title;
```

## 插入数据

单引号或双引号括起来字符串或日期

通过 LOAD DATA 语句或 mysqlimport 或 insert 语句

### INSERT

insert into...的 into 是可选的
```sql
INSERT INTO tb_name(col_name1,col_name2...)
VALUES(value1,value2...),(val1,val2...),(v1,v2...);
```

insert 可以通过 set 的方式给出列和值
```sql
INSERT INTO tb_name SET col_name1='val1', col_name2='val2'....;
```
* 这种形式不能一次插入多行

### LOAD DATA

LOAD DATA 语句假定列由 tab 分隔，行以换行符分隔，顺序按列在表中的存放次序给出
```sql
LOAD DATA LOCAL INFILE "member.txt" INTO TABLE member;
```

### mysqlimport

mysqlimport 实际也是调用的 LOAD DATA 语句
从数据文件名中导出表名，把文件名第一个圆点前的所有字符作为表名
```bash
$ mysqlimport --local samp_db member.txt
$ cat insert_*.sql | mysql samp_db
```

## 运算符

```sql
+ - * /
< <= = > >=
<> !=
AND OR NOT
```

## NULL
 
只能用 IS NULL 或 IS NOT NULL 来判断
不能和任何值比较，甚至 NULL 本身：
```sql
SELECT NULL < 0, NULL = 0, NULL != 0, NULL > 0;

SELECT NULL = NULL, NULL != NULL
```
* 结果都是 NULL

MySQL 有一个专有的比较运算符 `<=>`，可以用于 NULL ???
```mysql
SELECT * FROM president WHERE death <=> NULL;
```

## 排序

删除记录后在表中留下了未使用的“空位”，MySQL 在以后插入新记录时会试图填补,因此
不使用排序时默认检出的数据不一定和数据被插入的顺序是相同的

缺省为升序排列，有 NULL 值的列升序排最前，降序排最后
```sql
SELECT * FROM president ORDER BY birth
```

## 限制行数

```sql
SELECT * FROM president ORDER BY birth LIMIT 3
```

通过为 LIMIT 指定两个值来取出查询结果的中间部分
```sql
SELECT * FROM president ORDER BY birth LIMIT 5,3
```
* 第一个值为记录的编号，第二个值为记录的个数
* 记录编号从 0 开始

随机抽取记录
```mysql
SELECT * FROM president ORDER BY RAND() LIMIT 1
```

## 别名

别名包含空格时，需要用双引号括起来
```sql
SELECT CONCAT(firstname, "", last_name) AS "President Name" from president;
```

## 日期

MySQL 中日期按 `1970-10-12` 格式

YEAR,MONTH,DAYOFMONTH,MONTHNAME
```mysql
SELECT YEAR(birth),MONTH(birth),DAYOFMONTH(birth),MONTHNAME(birth) FROM president;
```
* DAYOFYEAR 比较闰年日期和非闰年日期会得到不正确的结果 ???

CURRENT_DATE
```mysql
SELECT * FROM president WHERE MONTH(birth) = MONTH(CURRENT_DATE) AND DAYOFMONTH(birth) = DAYOFMONTH(CURRENT_DATE)
```

TO_DAYS
```mysql
SELECT FLOOR((TO_DAYS(death) - TO_DAYS(birth))/365) AS age FROM president
```

DATE_ADD(),DATE_SUB()
```mysql
SELECT DATE_ADD("1970-1-1",INTERVAL 10 YEAR),DATE_SUB("1970-1-1",INTERVAL 10 YEAR)
```

## 模式匹配

`_` 匹配任意单个字符，`%` 匹配任意多个字符

LIKE , NOT LIKE 都不区分大小写

## 汇总数据

DISTINCT 删除重复行

COUNT(*) 对选中的行计数
COUNT(col_name) 对非 NULL 值进行计数
```mysql
SELECT COUNT(*), COUNT(suffix), COUNT(death) FROM president;
SELECT COUNT(DISTINCT(state)) FROM president;
```

GROUP BY
```mysql
SELECT sex, COUNT(*) AS count FROM student GROUP BY sex ORDER BY count DESC
SELECT sex, COUNT(*) AS count FROM student GROUP BY 1   ORDER BY 2     DESC
```

HAVING
```mysql
SELECT state, COUNT(*) AS count FROM president GROUP BY state ORDER BY count
DESC LIMIT 1
SELECT state, COUNT(*) AS count FROM president GROUP BY state HAVING count > 1
ORDER BY count DESC
```

MIN(),MAX(),SUM(),AVG()
```mysql
SELECT MIN(score),MAX(score),SUM(score),AVG(score) FROM score;
```

## 连接查询

INNER JOIN
```mysql
SELECT a.col_name,b.col_name FROM a,b WHERE a.col_id = b.col_id
```

LEFT JOIN 要求对 LEFT JOIN 关键字左边的表选择每行生成一个输出行
```mysql
SELECT a.col_name,b.col_name FROM a LFET JOIN b WHERE a.col_id = b.col_id
```

别名
```mysql
SELECT p1.last_name,p1.first_name,p1.birth
FROM president AS p1,president AS p2
WHERE MONTH(p1.birth) = MONTH(p2.birth)
AND DAYOFMONTH(p1.birth) = DAYOFMONTH(p2.birth)
AND (p1.last_name != p2.last_name OR p1.first_name != p2.first_name)
ORDER BY p1.last_name;
```

LEFT OUTER JOIN

## 删除记录

DELETE 后面不用加 `*` 号，因为 `*` 通常匹配所有列，而 DELETE 删除的是行
```sql
DELETE FROM tb WHERE col = val;
```
* DELETE 之前先把 DELETE 换成 SELECT 确认下数据再执行

## 更新记录

```sql
UPDATE tb SET col1=val1,col2=val2 WHERE col = val;
```
* UPDATE 之前先把 UPDATE 换成 SELECT 确认下数据再执行

## 改变表的结构

增加一列到表时，MYSQL 会对表中所有已存在的数据用缺省值初始化
??? 确认一下
```mysql
ALTER TABLE member
ADD member_id INT UNSIGNED NOT NULL AUTO_INCREMENT PRIMARY KEY;
```

## MySQL 交互技巧

### 简化连接过程

避免每次连接都要输入主机名、用户名、密码、数据库等连接参数

#### 利用选项文件存储连接参数

选项文件是一个文本文件，??? 文件名任意还是必须此文件名
```bash
$ vi ~/.my.cnf
    [client]
    host=host_name
    user=user_name
    password=pass_word

$ chmod 600 .my.conf        # 限定其他人不能读取
```
* 只有 `[client]` 是必须的
* 意味着可以只定义所需的参数，其他参数依然通过命令行输入

#### 利用 Shell 程序的别名或脚本定义 mysql 命令行快捷键

```bash
$ vi .bash_profile
    alias samp_db='mysql -h host_name -u user_name -p samp_db'
```

### 避免不必要的键入或重新键入

#### 利用 mysql 的输入行编辑功能

mysql 具有内建的 GNU Readline 库，允许对输入行进行编辑

|键序列|说明|
|---|---|
|Up 箭头，Ctrl-P|调前面的行|
|Down 箭头，Ctrl-N|调下一行|
|Left 箭头，Ctrl-B|光标左移（向后）|
|Right 箭头，Ctrl-F|光标右移（向前）|
|Escape Ctrl-B|向后移一个词|
|Escape Ctrl-F|向前移一个词|
|Ctrl-A|将光标移到行头|
|Ctrl-E|将光标移到行尾|
|Ctrl-D|删除光标下的字符|
|Delete|删除光标左边的字符|
|Escape D|删词|
|Escape Backspace|删除光标左边的词|
|Ctrl-K|删除光标到行尾的所有字符|
|Ctrl-_|撤消最后的更改；可以重复|

输入行编辑在 mysql 的 Windows 版中不起作用，但是可从 MySQL Web 站点取得免费的
cygwin_32 客户机分发包。在该分发包中的 mysqlc 程序与 mysql 一样，但它支持输入行
编辑命令。

#### 利用拷贝和粘贴

拷贝和粘贴是一种将数据传入 UNIX 文件的快速且简易的方法，但最适合较小的数据集，量较大的数据可能会超出系统拷贝缓冲区

量较大的数据最好是以无格式文本（制表符分隔）的形式保存电子表

在 mysql 中录入的命令行被保存在家目录下名为 `.mysql_history` 的文件中

#### 以批方式运行 mysql

对于定期运行的查询可以把 SQL 语句写入文件
```bash
$ vi intest.sql

    SELECT last_name,first_name FROM member
    WHERE interst LIKE '% asdf %'
    ORDER BY last_name,first_name;
    
$ mysql -t samp_db < intest.sql > output_file
```
* 最后的分号必须有，从而 mysql 知道查询语句已结束
* `< intest.sql` 表示从文件 intest.sql 中输入 SQL 语句
* `> output_file` 表示结果输出到文件 output_file，默认以制表符分隔列
* `-t` 选项表示不使用制表符，而是表格式，就像交互式运行 mysql 时输出一样

更为灵活的方法是将 SQL 语句放入一个 Shell 脚本中，并从命令行获取查询参数
```bash
$ vi interst.sh

    if [ $# -ne 1 ]; then echo "Please specify one keyword"; exit; fi
    mysql -t samp_db <<QUERY_INPUT
    SELECT last_name,first_name FROM member
    WHERE interst LIKE '%$1%'
    ORDER BY last_name,first_name;
    QUERY_INPUT

$ chomod +x interst.sh

$ interst.sh arg1

$ interst.sh arg2
```

#### 利用现有数据来创建新记录以避免键入 INSERT 语句

利用仅含有数据值的文件，然后利用 LOAD DATA 语句或 mysqlimport 实用程序从该文件中装入记录

下面的只是一个通过cat 命令追加数据到文件的方法，测试下即可，和这里的关系不大
获取数据到文件 data.txt
```bash
$ cat > data.txt // cat 命令等待输入

开始粘贴数据

粘贴完毕按 Enter

然后再按 Ctrl-D 指示文件结束
```
现在已经得到了包含有电子表中选择的数据块的 data.txt 文件
此文件已作好由 LOAD DATA 或 mysqlimport 加载到数据库的准备

