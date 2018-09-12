---
title: MySQL - Manual - 10.4 - 字符集：连接字符集和校对规则 - Connection Character Sets and Collations
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：连接字符集和校对规则

<!--more-->

几个字符集和校对规则系统变量关系到客户端和服务器的交互，在前面的章节中已经提到了
其中一些：

* 服务器的字符集和校对规则分别取自系统变量 `character_set_server` 和
  `collation_sever` 的值。

* 默认数据库的字符集和校对规则分别取自系统变量 `character_set_database` 和
  `collation_database` 的值。

其他的字符集和校对规则系统变量涉及到处理客户端和服务器之间的连接流量。每个客户端
都有与连接相关的字符集和校对规则系统变量。

当连接到服务器时，“连接”就是您所做的。客户端通过与服务器的连接发送 SQL 语句，
比如查询。服务器通过连接向客户端发送响应，例如结果集或错误消息。这就引出了几个关
于客户端连接的字符集和校对规则处理的问题，每个问题都可以用系统变量来回答。

* 当语句离开客户端时，语句是什么字符集？

  服务器将系统变量 `character_set_client` 的值作为客户端所发送语句的字符集。

* 服务器在接收到语句后应该将它转换成什么字符集？

  对于这个问题，服务器使用的是系统变量 `character_set_connection` 和
  `collation_connection`：

  * 服务器将客户端发送的语句从 `character_set_client` 指定的字符集转换为
    `character_set_connection` 指定的字符集，除了使用了引入器的字符串字面量（例
    如，`_utf8mb4` or `_latin2`）。

  * `collation_connection` 对于字符串字面量的比较是很重要的。

  * 对于把列值和字符串进行比较，`collation_connection` 不重要因为列都有自己的校
    对规则，后者有更高的优先级。

* 在将结果集或错误消息返回给客户端之前，服务器应该将其转换成什么字符集？

  系统变量 `character_set_results` 指示服务器使用什么字符集将查询结果返回给客户
  端。这包括了诸如列值之类的结果数据，以及诸如列名和错误消息等结果元数据。

客户端可以调整这些变量的设置，或者依赖于缺省值（在这种情况下，您可以跳过本节的其
余部分）。如果您不使用默认值，则必须更改每个连接到服务器的字符设置。

两个语句影响与连接相关的字符集变量作为一个组：

* `SET NAMES 'charset_name' [COLLATE 'collation_name']`

  `SET NAMES` 表明客户端将使用什么字符集来发送 SQL 语句到服务器。因此，`SET
  NAMES 'cp1251'` 告诉服务器 “这个客户端即将到来的消息使用的字符集是 cp1251”。
  它还指定了服务器应该用什么字符集将结果发回给客户端。（例如，如果您使用
  `SELECT` 语句，它将指示用于列值的字符集。）

  一个 `SET NAMES 'charset_name'` 语句相当于下面三个语句：
    ```
    SET character_set_client = charset_name;
    SET character_set_results = charset_name;
    SET character_set_connection = charset_name;
    ```

  设置 `character_set_connection` 为 `charset_name` 同时也隐含着设置了
  `collation_connection` 为 `charset_name` 的默认校对规则。没有必须明确的设置校
  对规则。要指定一个特殊的校对规则，可以使用可选的 `COLLATE` 子句。
    ```
    SET NAMES 'charset_name' COLLATE 'collation_name'
    ```

* `SET CHARACTER SET 'charset_name'`

  `SET CHARACTER SET` 类似于 `SET NAMES`，但是设置 `character_set_connection` 和
  `collation_connection` 分别为 `character_set_database` 和 `collation_database`
  。一个 `SET CHARACTER SET charset_name` 语句等价于下面三个语句：
    ```
    SET character_set_client = charset_name;
    SET character_set_results = charset_name;
    SET collation_connection = @@collation_database;
    ```

  设置 `collation_connection` 也隐含着设置了 `character_set_connection` 为和校对
  规则关联的字符集(等价于执行 `SET character_set_connection =
  @@character_set_database`)。没必要明确的设置 `character_set_connection` 。

  注意：
  ucs2, utf16, utf16le, 和 utf32 不能被用作客户端字符集，也就意味着它们不能和
  `SET NAMES` 或 `SET CHARACTER SET` 搭配使用。

MySQL 客户端程序 mysql、mysqladmin、mysqlcheck、mysqlimport 和 mysqlshow 根据如
下所示决定使用的默认字符集：

* 在没有其他信息的情况下，程序使用嵌入的默认字符集，通常是 `utf8mb4`。

* 对于从操作系统中可以使用本地变量的系统，客户端使用它来设置默认字符集，而不是使
  用内部编译的缺省字符集。这些程序可以根据操作系统设置自动检测出要使用的字符集，
  比如 Unix 系统上本地环境变量 `LANG` 或 `LC_ALL` 的值，或者 Windows 系统上的代
  码页设置。例如，把 `LANG` 设置为 `ru_RU.KOI8-R` 可以使得程序使用 `koi8r` 字符
  集。因此，用户可以在他们的环境中配置本地变量，供 MySQL 客户端使用。

  如果没有精确匹配，则 OS 字符集被映射到最近的 MySQL 字符集。如果客户端不支持匹
  配的字符集，那么它就会使用内部编译的缺省字符集。例如，“ucs2”不支持作为连接字
  符集。“utf8”和“utf-8”映射到“utf8mb4”。

  C 应用程序可以在连接到服务器之前，通过如下调用 `mysqloptions()` 来使用基于操作
  系统设置的字符集自动检测：
    ```
    mysql_options(mysql,
    MYSQL_SET_CHARSET_NAME,
    MYSQL_AUTODETECT_CHARSET_NAME);
    ```
* 这些程序支持 `--default-character-set` 选项，该选项允许用户显式地指定字符集，
  以覆盖客户端确定的任何缺省值。

当客户端连接到服务器时，它会发送要用于与服务器通信的字符集的名称。服务器使用收到
的字符集名来设置 `character_set_client`, `character_set_results`, 和
`character_set_connection` 三个系统变量。实际上，服务器使用字符集名称执行了 `SET
NAMES` 操作。

`mysql` 客户端要使用与默认设置不同的字符集，您可以在每次启动时显式地执行 `SET
NAMES`。为了更容易地完成相同的结果，将 `--default-character-set` 选项设置添加到
您的 `mysql` 命令行或选项文件中。例如，下面的选项文件设置会在每次调用 `mysql` 时
将三个和连接相关的字符集变量设置为 `koi8r`：
```
[mysql]
default-character-set=koi8r
```

如果您使用的是启用自动重新连接（不推荐）的 `mysql` 客户端，那么最好使用
`charset` 命令而不是 `SET NAMES`。例如:
```
mysql> charset utf8
Charset changed
```
`charset` 命令执行一个 `SET NAMES` 语句，并且改变 `mysql` 在连接中断后重新连接时
使用的默认字符集。

例如: 假如 `column1` 被定义为 `CHAR(5) CHARACTER SET latin2`。如果你不说 `SET
NAMES` 或 `SET CHARACTER SET`，则对于 `SELECT column1 FROM t`，服务器返回
`column1` 的所有值都使用客户端连接时指定的字符集。另一方面，如果在执行 `SELECT`
语句前你说 `SET NAMES 'latin1'` 或 `SET CHARACTER SET latin1`，服务器则在返回结
果前把 `latin2` 值转换为 `latin1`，如果有字符不是同时存在于两个字符集里则转换有
数据丢失。

如果你想要服务器不要转换结果集或错误信息，设置 `character_set_results` 为 `NULL`
或 `binary`:
```
SET character_set_results = NULL;
SET character_set_results = binary;
```

要查看应用于您当前连接的字符集和校对规则的系统变量的值，请使用以下语句：
```
SHOW VARIABLES LIKE 'character_set%';
SHOW VARIABLES LIKE 'collation%';
```

您还必须考虑 MySQL 应用程序执行的环境，参考：Section 10.5,“Configuring
Application Character Set and Collation”.

关于字符集和错误信息的更多信息，参考：Section 10.6, “Error Message Character
Set”.
