---
title: MySQL - Manual - 11.3 - Data Types - 日期和时间类型
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 日期和时间类型

<!--more-->

用来表示时间值的日期和时间类型有：`DATE`, `TIME`, `DATETIME`, `TIMESTAMP`, 和
`YEAR`。每一个时态类型都有一个有效值的范围，以及一个当您指定 MySQL 不能表示的无
效值时可能使用的“零”值。`TIMESTAMP` 类型有特殊的自动更新行为，稍后描述。对于时
态类型的存储需求，请参阅 Section 11.8, “Data Type Storage Requirements”

在处理日期和时间类型时，请记住这些一般的注意事项：

* MySQL 检索给定日期或时间类型的值时以标准格式输出，但它会尝试解释您所提供的输入
  值的各种格式（例如，当您指定一个值要与日期或时间类型赋值或比较时）。有关日期和
  时间类型的许可格式的描述，请参阅 Section 9.1.3, “Date and Time Literals”。期
  望您提供有效的值。如果您使用其他格式的值，可能会发生不可预知的结果。

* 尽管 MySQL 试图解释多种格式的值，但日期部分必须总是按年月日顺序（例如，“
  98-09-04”），而不是在其他地方常用的月日年或日月年顺序（例如，“09-04-98”，“
  04-09-98”）。

* 包含两位数年值的日期是模糊的，因为世纪是未知的。MySQL 使用这些规则解释两位数的
  年份值：

    * 70-99年的年值被转换为1970-1999年。
    * 00-69的年值转换为2000-2069年。
    参考 Section 11.3.8, “Two-Digit Years in Dates”.

* 将值从一种时间类型转换为另一种时间类型，根据第 11.3 节中的规则，Section
  11.3.7,“Conversion Between Date and Time Types”

* 如果在数值上下文中使用值，MySQL 会自动将日期或时间值转换成数字。反之亦然。

* 在默认情况下，当遇到超出范围的日期或时间类型的值时，或者遇到无效的日期、时间类
  型的值时，MySQL 会将该值转换为该类型的“零”值。唯一的例外是，超出范围的
  `TIME` 值被剪切到 `TIME` 范围的适当端点。

* 通过将 SQL 模式设置为适当的值，您可以更准确地指定您希望 MySQL 支持哪种类型的日
  期(See Section 5.1.10, “Server SQL Modes”)。通过启用 `ALLOW_INVALID_DATES`
  SQL 模式，您可以让 MySQL 接受某些日期，比如“2009-11-31”。当您想要存储一个用
  户指定的（例如，在 web 表单中）“可能错误”的值到数据库中以便将来进行处理时，
  这是很有用的。在这种模式下，MySQL 只验证月的范围在 1 到 12 之间和天的范围从 1
  到 31。

* MySQL 允许您在 `DATE` 或 `DATETIME` 列中存储日为零或月和日为零的日期。这对于需
  要存储出生日期的应用程序非常有用，因为您可能不知道确切的日期。在这种情况下，您
  只需将日期存储为 `2009-00-00` 或 `2009-01-00` 。如果您存储诸如此类的日期，在使
  用比如 `DATE_SUB()` 或 `DATE_ADD()` 的函数时则不应该期望得到正确的结果，它们需
  要完整的日期。若要日期中的部分不允许零个月或零天，可启用 `NO_ZERO_IN_DATE` 模
  式。

* MySQL 允许您将 `0` 值存储为 `0000-00-00` 来作为一个 `虚拟日期` 。在某些情况下
  ，这比使用 NULL 值更方便，并且使用更少的数据和索引空间。若要不允许
  `0000-00-00` ，则请启用 `NO_ZERO_DATE` 模式。

* 由于 ODBC 无法处理“零”日期和时间这些值，所以通过 `Connector/ODBC` 使用的“零
  ”日期或时间值被自动转换为 NULL。

下表展示了每种类型的 `零` 值的格式。 `零` 值是特殊的，但是您可以使用表中所示的值
显式地储存或引用它们。您也可以使用值 '0' 或 0 来完成这项工作，这更容易编写。对于
包括日期部分（ `DATE` 、 `DATETIME` 和 `TIMESTAMP` ）的时态类型，如果启用了
`NO_ZERO_DATE` SQL 模式，使用这些值会产生警告。

    Data Type       “Zero” Value
    DATE            '0000-00-00'
    TIME            '00:00:00'
    DATETIME        '0000-00-00 00:00:00'
    TIMESTAMP       '0000-00-00 00:00:00'
    YEAR            0000

## 11.3.1 The DATE, DATETIME, and TIMESTAMP Types

 `DATE` 、 `DATETIME` 和 `TIMESTAMP` 类型是相关的。本节描述它们的特征，它们是如
 何相似的，以及它们之间的区别。MySQL 识别多种格式的 `DATE` 、 `DATETIME` 和
 `TIMESTAMP` 值，描述参考 Section 9.1.3, “Date and Time Literals” 。对于
 `DATE` 和 `DATETIME` 范围描述， `支持` 意味着尽管早期的值可能起作用，但不敢保证。

DATE 类型用于具有日期部分但没有时间部分的值。MySQL 以“YYYY-MM-DD”的格式检索和
显示 DATE 值。所支持的范围是“1000-01-01”到“9999-12-31”。

The `DATETIME` type is used for values that contain both date and time parts. MySQL retrieves and displays `DATETIME` values in 'YYYY-MM-DD HH:MM:SS' format. The supported range is '1000-01-01 00:00:00' to '9999-12-31 23:59:59'.
“DATETIME”类型用于同时包含了日期和时间的值。MySQL 以 `yyyymm-dd HH:MM:SS`
格式检索和显示“DATETIME”值。所支持的范围是“1000-01-01:00:00”到“9999-12-31
23:59:59”。

“TIMESTAMP”类型用于同时包含了日期和时间的值。TIMESTAMP 范围 '1970-01-01
00:00:01' UTC 到 '2038-01-19 03:14:07' UTC

DATETIME 或 TIMESTAMP 值尾部可以包括一个分秒，最高可达微秒（6位）的精度。特别地
，插入到 DATETIME 或 TIMESTAMP 列中的值的任何小数部分都是存储而不是丢弃的。在包
含小数部分的情况下，这些值的格式是“YYY-MM-DD HH:MM:SS.[fraction]”，DATETIME 值
的范围是 '1000-01-01:00:00.000000' 到 '9999-12-31:59:59.999999'，TIMESTAMP 值的
范围是 '1970-01-01:00:01.000000' 到 '2038-01-19 03:14:07.999999'，小数秒部分应该
总是和其他部分的时间用小数点隔开，不能识别其他分秒分隔符。关于 MySQL 中分秒支持
的信息，参见 Section 11.3.6, “Fractional Seconds in Time Values”

TIMESTAMP 和 DATETIME 数据类型提供自动初始化和更新到当前日期和时间。更多信息参考
： Section 11.3.5, “Automatic Initialization and Updating for TIMESTAMP and
DATETIME”.

MySQL 存储 `TIMESTAMP` 值时从当前时区转换为 UTC ，并在检索时从 UTC 时间转换回当
前时区。（这不会发生在诸如 `DATETIME` 之类的其他类型中。）默认情况下，每个连接的
当前时区是服务器的时间。时区可以设置在每个连接的基础上。只要时区设置保持不变，就
会得到与存储相同的值。如果你存储一个 `TIMESTAMP` 值，然后改变时区并检索值，那么
检索到的值与你存储的值是不同的。这是因为同一时区不能用于两个方向的转换。系统变量
`time_zone` 的值可作为当前时区。要了解更多信息，请参阅 Section 5.1.12, “MySQL
Server Time Zone Support”.

无效的 `DATE`, `DATETIME`, `TIMESTAMP` 值被转换为合适类型的 “0” 值
('0000-00-00' or '0000-00-00 00:00:00')。

在 MySQL 中注意日期值解释的某些属性：

* MySQL 允许为字符串指定的值“宽松”格式，其中任何标点符号都可以用作日期部分或时
  间部分之间的分隔符。在某些情况下，这种语法可能是骗人的。例如，因为 `:` 符号像
  “10:11:12”这样的值可能看起来像一个时间值，但是如果在日期上下文中使用，则被解
  释为“2010-11-12”。“10:45:15”的值被转换为“0000-00-00”，因为“45”不是一个
  有效的月份。

  在日期和时间部分和分秒部分之间识别的唯一分隔符是小数点。

* 服务器要求月和日值都是有效的，而不仅仅是分别在1到12和1到31的范围内。由于禁用了
  严格模式，无效的日期如“2004-04-31”被转换为“0000-00-00”，并产生一个警告。在
  启用严格模式的情况下，无效的日期会产生错误。启用 `ALLOW_INVALID_DATES` 后可以
  允许这样无效的日期。请参阅 Section 5.1.10, “Server SQL Modes”以获得更多信息
  。

* MySQL 不接受在天或月列中包括零值或非有效日期值的 `TIMESTAMP` 值。这条规则的唯
  一例外是特殊的 “零”值 “0000-00-00:00:00”。

* 包含两位数年值的日期是模糊的，因为世纪是未知的。MySQL 使用这些规则解释两位数的
  年份值：

    * 70-99年的年值被转换为1970-1999年。
    * 00-69的年值转换为2000-2069年。
    参考 Section 11.3.8, “Two-Digit Years in Dates”.

## 11.3.2 The TIME Type

MySQL 以 “HH:MM:SS”格式（对于较大的小时值以“HHH:MM:SS”格式）检索和显示
`TIME` 值。`TIME` 值可能从“-838:59:59”到 “838:59:59”。小时部分可能是如此之大
是因为时间类型不仅可以用来代表一天的某个时间(必须小于 24 小时)，而且还可以表示已
运行时间或两个事件之间的时间间隔(这可能远远大于24小时，甚至是负数)。

MySQL 可以识别多种格式 `TIME` 值，其中一些格式可以包括微秒（6位）精度的尾部小数
秒部分。参见 Section 9.1.3, “Date and Time Literals”。关于 MySQL 中分秒支持的
信息，参见 Section 11.3.6, “Fractional Seconds in Time Values”。特别地，插入到
`TIME` 列中的值的任何小数秒部分都是存储而不是丢弃的。在包含小数秒部分的情况下，
时间值的范围是 '-838:59:59.000000' 到 '838:59:59.000000'。

在一个 `TIME` 列中分配缩写值时要小心。缩写的带冒号的 `TIME` 值被 MySQL 解释为一
天的某个时间。也就是说 '11:12' 的意思是 '11:12:00'，而不是 '00:11:12'。在没有冒
号的情况下 MySQL 解释缩写值是假设两个最右边的数字代表秒（也就是说，作为运行时间
而不是一天的某个时间）。例如，你可能会认为“1112”和 1112 的意思是 “11:12:00”
（11点12分钟），但 MySQL 将其解释为 “00:11:12”（11分12秒）。类似地，“12”和
12 被解释为“00:00:12”。

在时间部分和分秒部分之间识别的唯一分隔符是小数点。

默认情况下，位于 `TIME` 范围之外的值，但在其他方面是有效的值，则被剪切到范围最近
的端点。例如，“-850:00:00”和 “850:00:00”被转换为“-838:59:59”和 “838:59:59
”。无效的 `TIME` 值被转换为 '00:00:00'。注意，因为 '00:00:00' 本身就是一个有效
的 `TIME` 值，所以没有办法区分表中存储的 “00:00:00”值是原始被指定为 '00:00:00'
还是由无效值转换而来。

对于无效的 `TIME` 值的更严格的处理，可以启用严格的 SQL 模式来导致错误发生。参见
Section 5.1.10, “Server SQL Modes”.

## 11.3.3 The YEAR Type

`YEAR` 类型是一种用来表示年份值的 1 个字节类型。它可以被声明为 YEAR 或年 YEAR(4)
，显示宽度为 4 个字符。

    注意：
    老版本 MySQL 支持的 YEAR(2) 数据类型，MySQL 8.0 不支持。有关转换为 YEAR(4)
    的说明，参考：Section 11.3.4, “Migrating YEAR(2) Columns to YEAR(4)”.

MySQL 以 `YYYY` 格式显示 `YEAR` 值。范围从 1901 到 2155，或者 0000。

您可以以各种格式指定输入 `YEAR` 值：
* 4 位数字，从 1901 到 2155
* 4 位字符串，从 '1901' 到 '2155'
* 1 位或 2 位数字，从 1 到 99。MySQL 将 1 到 69 转换为 2001 到 2069，将 70 到 99
  转换为 1970 到 1999
* 1 位或 2 位字符串，从 '0' 到 '99'。MySQL 将 '0' 到 '69' 转换为 '2000' 到
  '2069'，将 '70' 到 '99' 转换为 '1970' 到 '1999'
* 插入一个数字 0 结果显示和内部值都是 `0000`，要插入零后被解释为 2000，则请插入
  字符串 '0' 或 '00'
* 作为一个函数的结果，它返回一个在 `YEAR` 上下文中可以接受的值，比如 `NOW()`。

MySQL 转换无效的 `YEAR` 值为 0000.

See also Section 11.3.8, “Two-Digit Years in Dates”.

## 11.3.4 Migrating YEAR(2) Columns to YEAR(4)

MySQL 8.0 不支持老版本 MySQL 允许的 `YEAR(2)` 数据类型。已存在的 `YEAR(2)` 列要
变为可用的必须被转换为 `YEAR(4)`。

本节提供关于执行转换的信息。

### Removed YEAR(2) Support in MySQL 8.0

MySQL 8.0 按照如下所述处理 `YEAR(2)` 列：

* 对新表使用 `YEAR(2)` 列定义将产生一个 `ER_INVALID_YEAR_COLUMN_LENGTH` 错误：

    ```
    mysql> CREATE TABLE t1 (y YEAR(2));
    ERROR 1818 (HY000): Supports only YEAR or YEAR(4) column.
    ```

* 已存在表里的 `YEAR(2)` 列仍然是 `YEAR(2)`，但是在查询中使用 `YEAR(2)` 列将产生
  警告或错误。

* 一些儿程序或语句会自动转换 `YEAR(2)` 为 `YEAR(4)`：

    * `ALTER TABLE` 语句会导致表重建。
    * `REPAIR TABLE` (如果它发现表里包含 `YEAR(2)` 列，则会推荐你使用 `CHECK TABLE`)
    * `mysql_upgrade` (which uses `REPAIR TABLE`).
    * 用 `mysqldump` 转储并重新加载转储文件。与前三个项目所执行的转换不同，转储
      和重载有可能改变值。

    MySQL 升级通常涉及到后两项中的至少一项。然而，相对于 `YEAR(2)`，
    `mysql_upgrade` 是更好的选择。您应该避免使用 `mysqldump`，因为正如所指出的那
    样，它可以改变值。

### Migrating from YEAR(2) to YEAR(4)

要将 `YEAR(2)` 列转换为 `YEAR(4)` ，您可以在任何时候手动操作，而无需升级。或者，
您可以升级到已减少或取消对 `YEAR(2)` 支持的 MySQL 版本（MySQL 5.6.6 或更高版本）
，然后让 MySQL 自动转换 `YEAR(2)` 列。在后一种情况下，避免通过转储和重新加载数据
来升级，因为这会改变数据值。此外，如果您使用复制，那么您必须考虑到升级的注意事项
。

手动将 `YEAR(2)` 列转换为 `YEAR(4)`，使用 `ALTER TABLE` 或 `REPAIR TABLE`，假定
表 `t1` 的定义如下：
```
CREATE TABLE t1 (ycol YEAR(2) NOT NULL DEFAULT '70');
```

使用 `ALTER TABLE` 语句修改列：
```
ALTER TABLE t1 FORCE;
```
`ALTER TABLE` 语句转换表时并没有改变 `YEAR(2)` 的值，如果服务器是一组复制服务器
的主服务器，`ALTER TABLE` 语句将被复制到从服务器，并在每个对应的表上做更改。

另一种迁移方法是执行一个二进制升级：在不转储和重新加载数据的情况下安装 MySQL。然
后运行 `mysql_upgrade`，它使用 `REPAIR TABLE` 将 `YEAR(2)` 列转换为 `YEAR(4)` ，
而不改变数据值。如果服务器是一个复制主服务器，那么 `REPAIR TABLE` 语句就会复制到
从服务器，并在相应的每一个表上发生变化，除非您调用 `mysql_upgrade` 时使用了
`--skip-write-binlog` 选项。

Upgrades to replication servers usually involve upgrading slaves to a newer version of MySQL, then upgrading the master. For example, if a master and slave both run MySQL 5.5, a typical upgrade sequence involves upgrading the slave to 5.6, then upgrading the master to 5.6. With regard to the different treatment of `YEAR(2)` as of MySQL 5.6.6, that upgrade sequence results in a problem: Suppose that the slave has been upgraded but not yet the master. Then creating a table containing a `YEAR(2)` column on the master results in a table containing a `YEAR(4)` column on the slave. Consequently, these operations will have a different result on the master and slave, if you use statement-based replication:
复制服务器的升级通常包含了升级从服务器到更新版本的 MySQL，然后升级主服务器。例如
，如果一个主和从服务器都运行 MySQL 5.5，一个典型的升级顺序包括将从服务器升级到
5.6，然后将主服务器升级到 5.6。关于从 MySQL 5.6.6 起对 `YEAR(2)` 的不同处理方法
，是因为升级顺序导致了一个问题：假设从服务器已经升级完成了，但主服务器还没有，然
后在主服务器上创建一个包含 `YEAR(2)` 列的表，结果导致从服务器上一个包含
`YEAR(4)` 列的表。因此，如果您使用基于语句的复制，那么这些操作将对主和从服务器产
生不同的结果：

* 插入数字 0，结果在主服务器上内部值为 2000，在从服务器为 0000

* 将 `YEAR(2)` 转换为字符串，此操作使用主服务器上的显示值 `YEAR(2)`，从服务器上
  `YEAR(4)`

为了避免这些问题，在升级之前，将主服务器的所有 `YEAR(2)` 列修改为 `YEAR(4)` 。（
如前所述，使用 `ALTER TABLE`）然后您可以正常地升级（从服务器优先，然后是主服务器
），而不需要引入任何 `YEAR(2)` 到 `YEAR(4)` 主服务器和从服务器之间的差异。

应该避免一种迁移方法：不要使用 `mysqldump` 将数据转储，并在升级后重新加载转储文
件。如前所述，这有可能改变 `YEAR(2)` 的值。

从 `YEAR(2)` 到 `YEAR(4)` 的迁移还应该包括检查应用程序代码，以了解在下面这些条件
下发生改变行为的可能性：
* 要求选择 `YEAR` 列以精确的生成两个数字的代码。
* 没有考虑插入数字 0 要进行不同处理的代码：将 0 插入到 `YEAR(2)` 或 `YEAR(4)` 中
  ，结果内部值分别是 2000 或 0000。

## 11.3.5 Automatic Initialization and Updating for TIMESTAMP and DATETIME

`TIMESTAMP` 和 `DATETIME` 列可以被自动初始化和更新到当前的日期和时间（也就是当前
的时间戳）

对于表中的任何 `TIMESTAMP` 或 `DATETIME` 列，您都可以指定默认值或自动更新值为当
前时间戳，或者两者都是：

* 插入的行如果没有指定列值，则自动初始化列的列支就被设置为当前的时间戳

* 当行的任何其他列的值被更改为不同于原来的值时，自动更新列就会自动更新到当前时间
  戳。如果所有其他列都被更改为还是当前的值没有改变，那么自动更新的列保持不变。要
  阻止自动更新列在其他列值发生变化时进行自动更新，则请显式地将其设置为当前值。或
  者在其他列值没有改变时要更新自动更新列，则也要显式地将其设置为它应该拥有的值（
  例如，将其设置为 `CURRENT_TIMESTAMP`）。

另外，如果禁用了系统变量 `explicit_defaults_for_timestamp`，则可以通过为
`TIMESTAMP` 列（不包括 `DATETIME` 列）赋值 `NULL` 来初始化或更新列值为当前的日期
和时间，除非此列定义时使用了 `NULL` 属性来允许保存 `NULL` 值。

要指定自动属性，在列定义中使用 `DEFAULT CURRENT_TIMESTAMP` 和 `ON UPDATE
CURRENT_TIMESTAMP` 子句，子句的顺序不重要，如果两个子句同时用于列定义，则任意一
个都可以先被执行。`CURRENT_TIMESTAMP` 的任何同义词也有相同的含义，包括：
`CURRENT_TIMESTAMP(), NOW(), LOCALTIME, LOCALTIME(), LOCALTIMESTAMP,
LOCALTIMESTAMP()`.

使用的 `DEFAULT CURRENT_TIMESTAMP` 和 `ON UPDATE CURRENT_TIMESTAMP` 子句是针对于
`TIMESTAMP` 和 `DATETIME` 列而言。`DEFAULT` 子句也可用于指定常量默认值，例如
`DEFAULT 0` 或 `DEFAULT '2000-01-01 00:00:00'`.

    注意：
    下面的例子中使用 `DEFAULT 0`，这个缺省值可能产生警告或错误，取决于是否启用了
    严格的 SQL 模式或 `NO_ZERO_DATE` 模式，注意 `TRADITIONAL` SQL 模式 包含了前
    面两个模式。See Section 5.1.10, “Server SQL Modes”

`TIMESTAMP` 或 `DATETIME` 列定义可以将默认值和自动更新值同时指定为当前的时间戳，
也可以只指定一个，或都不指定，不同的列可以有不同的自动属性组合，下列规则描述了各
种可能性：

* 同时使用 `DEFAULT CURRENT_TIMESTAMP` 和 `ON UPDATE CURRENT_TIMESTAMP` 子句，则
  列的默认值是当前时间戳，且会自动更新到当前时间戳。
    ```
    CREATE TABLE t1 (
        ts TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
        dt DATETIME DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
    );
    ```

* 使用 `DEFAULT` 子句但没有 `ON UPDATE CURRENT_TIMESTAMP` 子句，则列的默认值是当
  前时间戳，但不会自动更新到当前时间戳。

  默认值取决于 `DEFAULT` 子句是指定了 `CURRENT_TIMESTAMP` 还是一个常量值，使用
  `CURRENT_TIMESTAMP` 则是当前的时间戳值:
    ```
    CREATE TABLE t1 (
        ts TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
        dt DATETIME DEFAULT CURRENT_TIMESTAMP
    );
    ```
  使用常量，则默认值就是给定的值，下面的例子里，没有任何自动属性:
    ```
    CREATE TABLE t1 (
        ts TIMESTAMP DEFAULT 0,
        dt DATETIME DEFAULT 0
    );
    ```

* 使用 `ON UPDATE CURRENT_TIMESTAMP` 子句和一个常量的 `DEFAULT` 子句，则列会被自
  动更新到当前时间戳，默认值是给定的常量值：
    ```
    CREATE TABLE t1 (
        ts TIMESTAMP DEFAULT 0 ON UPDATE CURRENT_TIMESTAMP,
        dt DATETIME DEFAULT 0 ON UPDATE CURRENT_TIMESTAMP
    );
    ```

* 使用 `ON UPDATE CURRENT_TIMESTAMP` 子句但没有 `DEFAULT` 子句，则列会被自动更新
  到当前时间戳，但是默认值不是当前时间戳：

  这种情况下，默认值是依赖类型的。
  
  `TIMESTAMP` 的默认值为 0，除非定义了 `NULL`属性时，默认值才为 `NULL`。
    ```
    CREATE TABLE t1 (
        ts1 TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, -- default 0
        ts2 TIMESTAMP NULL ON UPDATE CURRENT_TIMESTAMP -- default NULL
    );
    ```

  `DATETIME` 默认值为 `NULL`，除非定义了 `NOT NULL` 属性时，默认值才为 0.
    ```
    CREATE TABLE t1 (
        dt1 DATETIME ON UPDATE CURRENT_TIMESTAMP, -- default NULL
        dt2 DATETIME NOT NULL ON UPDATE CURRENT_TIMESTAMP -- default 0
    );
    ```

除非显式的指定了自动属性，否则 `TIMESTAMP` 和 `DATETIME` 列没有自动属性，除了这
种情况：如果禁用了系统变量 `explicit_defaults_for_timestamp`，即使没有显式的指定
两个自动属性，第一个 `TIMESTAMP` 列也有 `DEFAULT CURRENT_TIMESTAMP` 和 `ON
UPDATE CURRENT_TIMESTAMP`，要取消第一个 `TIMESTAMP` 列的自动属性，可使用下面任意
一个策略：

* 启用系统变量 `explicit_defaults_for_timestamp`，这种情况下，`DEFAULT
  CURRENT_TIMESTAMP` 和 `ON UPDATE CURRENT_TIMESTAMP` 子句可分别用于指定自动初始
  化和更新，但除非被显式的包含在列定义中，这两个子句没有被赋予任何 `TIMESTAMP`
  列。

* 或者，如果禁用了 `explicit_defaults_for_timestamp`，则使用下面任意一个策略：

    * 定义列时使用 `DEFAULT` 子句指定一个常量默认值

    * 指定 `NULL` 属性。这也使得列允许 `NULL` 值，也意味着不能通过设置列为
      `NULL` 来为列赋值当前时间戳，赋值为 `NULL` 时值就是 `NULL`，不是当前的时间
      戳，要赋值为当前时间戳，需要赋值为 `CURRENT_TIMESTAMP` 或同义词如 `NOW()`.

思考下面的表定义：
```
CREATE TABLE t1 (
    ts1 TIMESTAMP DEFAULT 0,
    ts2 TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    ON UPDATE CURRENT_TIMESTAMP);

CREATE TABLE t2 (
    ts1 TIMESTAMP NULL,
    ts2 TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    ON UPDATE CURRENT_TIMESTAMP);

CREATE TABLE t3 (
    ts1 TIMESTAMP NULL DEFAULT 0,
    ts2 TIMESTAMP DEFAULT CURRENT_TIMESTAMP
    ON UPDATE CURRENT_TIMESTAMP);
```
这些表具有以下属性：
* 在每个表定义中，第一个 `TIMESTAMP` 列没有自动初始化或更新。
* 各表不同在于：ts1 列如何处理 `NULL` 值。
    * 对于 t1，ts1 是默认 `NOT NULL`，赋值为 `NULL` 会将其设置为当前时间戳。
    * 对于 t2 和 t3，ts1 列都允许 `NULL`，所以赋值为 `NULL` 则值就为 `NULL`。
* t2 和 t3 的不同在于 ts1 的默认值，ts1 被定义为允许 NULL，所以没有显式的
  `DEFAULT` 子句时，其默认值就是 `TIMESTAMP` 类型的默认值 NULL，对于 t3，是显式
  的默认值 0

如果 `TIMESTAMP` 或 `DATETIME` 的列定义在任何地方包含了一个显式的分秒精度值，那
么在整个列定义中都必须使用相同的值。这是允许的:
```
CREATE TABLE t1 (
    ts TIMESTAMP(6) DEFAULT CURRENT_TIMESTAMP(6) ON UPDATE CURRENT_TIMESTAMP(6)
);
```
这是不允许的:
```
CREATE TABLE t1 (
    ts TIMESTAMP(6) DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP(3)
);
```

### TIMESTAMP Initialization and the NULL Attribute

如果系统变量 `explicit_defaults_for_timestamp` 被禁用，`TIMESTAMP` 列默认值是
`NOT NULL`，不能包含 `NULL` 值，赋值为 `NULL` 将赋予当前的时间戳。

要允许 `TIMESTAMP` 列包含 `NULL` 值，则请声明列时显式的使用 `NULL` 属性。这种情
况下，默认值也是 `NULL`，除非使用 `DEFAULT` 子句指定了一个不同的默认值，`DEFAULT
NULL` 可以用来显式的指定 `NULL` 作为默认值。(如果 `TIMESTAMP` 列声明时没有使用
`NULL` 属性，`DEFAULT NULL` 无效) 如果 `TIMESTAMP` 列允许 `NULL` 值，则赋值为
`NULL` 则其值就是 `NULL`，而不是当前的时间戳。

下表包含的几个 `TIMESTAMP` 列都允许 `NULL` 值：
```
CREATE TABLE t
(
    ts1 TIMESTAMP NULL DEFAULT NULL,
    ts2 TIMESTAMP NULL DEFAULT 0,
    ts3 TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP
);
```

允许 `NULL` 值的 `TIMESTAMP` 列不会在插入时使用当前的时间戳，除非是在下列任意条
件下：
* 默认值被定义为 `CURRENT_TIMESTAMP` 且没有为列指定值时
* `CURRENT_TIMESTAMP` 或它的任意同义词如 `NOW()` 被显式的插入到列

换句话说，定义为 `TIMESTAMP` 的列只有它的定义包含 `DEFAULT CURRENT_TIMESTAMP` 时
才允许自动初始化为 `NULL` 值：
```
CREATE TABLE t (ts TIMESTAMP NULL DEFAULT CURRENT_TIMESTAMP);
```

如果 `TIMESTAMP` 列允许 `NULL` 值但它的定义里没有包含 `DEFAULT
CURRENT_TIMESTAMP`，则你必须显式的插入和当前日期和时间对应的值：

假设表 t1 和 t2 的定义如下：
```
CREATE TABLE t1 (ts TIMESTAMP NULL DEFAULT '0000-00-00 00:00:00');
CREATE TABLE t2 (ts TIMESTAMP NULL DEFAULT NULL);
```

则要在插入时将 `TIMESTAMP` 列设置为当前时间戳，需显式地给它赋值。例如:
```
INSERT INTO t2 VALUES (CURRENT_TIMESTAMP);
INSERT INTO t1 VALUES (NOW());
```

如果启用了系统变量 `explicit_defaults_for_timestamp`，则只有 `TIMESTAMP` 列声明
时使用了 `NULL` 属性时才允许 `NULL` 值。同时，无论声明时使用 `NULL` 或者 `NOT
NULL` 属性，`TIMESTAMP` 列都不允许赋值为 `NULL` 时变为自动赋值为当前的时间戳。要
赋值为当前的时间戳，需显式的为列赋值为 `CURRENT_TIMESTAMP` 或他的同义词例如
`NOW()`.

## 11.3.6 Fractional Seconds in Time Values

MySQL 8.0 的小数秒支持 `TIME`, `DATETIME` 和 `TIMESTAMP` 值，最高可达微秒（6位）
精度：

* 要定义一个包含小数秒部分的列，使用语法 `type_name(fsp)`，`type_name` 是
  `TIME`, `DATETIME`, `TIMESTAMP`, `fsp` 是小数秒的精度，例如：

    ```
    CREATE TABLE t1 (t TIME(3), dt DATETIME(6));
    ```

  如果给出了 `fsp` 的值，则范围必须是 0 到 6 之间。0 表示没有小数秒部分。如果省
  略，则默认的精度就是 0。(是为了兼容以前的 MySQL 版本，因此不同于标准 SQL 的默
  认值 6)

* 把一个带小数秒的 `TIME`, `DATE` 或 `TIMESTAMP` 值插入到同一个类型但更少小数位
  的列将导致四舍五入，例如：

    ```
    mysql> CREATE TABLE fractest( c1 TIME(2), c2 DATETIME(2), c3 TIMESTAMP(2) );
    Query OK, 0 rows affected (0.33 sec)

    mysql> INSERT INTO fractest VALUES
    > ('17:51:04.777', '2014-09-08 17:51:04.777', '2014-09-08 17:51:04.777');
    Query OK, 1 row affected (0.03 sec)

    mysql> SELECT * FROM fractest;
    +-------------+------------------------+------------------------+
    | c1          | c2                     | c3                     |
    +-------------+------------------------+------------------------+
    | 17:51:04.78 | 2014-09-08 17:51:04.78 | 2014-09-08 17:51:04.78 |
    +-------------+------------------------+------------------------+
    1 row in set (0.00 sec)
    ```

  当发生上述的四舍五入时，不会给出警告或错误。此行为遵循了 SQL 标准，且不受服务
  器 `sql_mode` 设置的影响。

* 使用时态参数的函数可以接受带小数秒的值。从时间函数返回的值包括适当的小数秒。例
  如，没有参数的 `NOW()` 返回的当前日期和时间也没有小数秒部分，但是可以使用一个
  取值从 0 到 6 的可选参数来指定返回值包含了几位小数秒部分。

* 时态字面量语法也产生时态值：`DATE 'str'`, `TIME 'str'`, `TIMESTAMP 'str'`, 等
  价于 ODBC 语法。所得到的值包括末尾的小数部分（如果指定了）。以前，时态类型关键
  字会被忽略，上述的构造只会产生字符串值。参见标准 SQL 和 ODBC 日期和时间字面量
  。

## 11.3.7 Conversion Between Date and Time Types

在某种程度上，你可以将一个值从一个时间类型转换成另一个时间类型。然而，可能会改变
数值和丢失信息。在所有情况下，时间类型之间的转换取决于结果类型的有效值的范围。例
如，虽然 `DATE`、`DATETIME` 和 `TIMESTAMP` 值都可以使用相同的格式来指定，但是类
型并不都具有相同的值范围。`TIMESTAMP` 的值不能早于 `1970 UTC`，也不能晚于
`2038-01-19 03:14:07 UTC`。这意味着一个如 `1968-01-01` 的日期，虽然作为 `DATE`
或 `DATETIME` 值有效，但作为 `TIMESTAMP` 值无效，会被转换为 0。

转换 `DATE` 值：
* 转换为 `DATETIME` 或 `TIMESTAMP` 值会添加时间部分为 '00:00:00'，因为 `DATE` 值
  没有包含时间信息
* 转换为 `TIME` 值没什么用，结果是 '00:00:00'.

转换 `DATETIME` 和 `TIMESTAMP` 值：
* 转换为 `DATE` 值会考虑小数秒在内对 `TIME` 部分进行四舍五入。例如：'1999-12-31
  23:59:59.499' 变为 '1999-12-31'，而 '1999-12-31 23:59:59.500' 变为
  '2000-01-01'
* 转换为 `TIME` 值将放弃 `DATE` 部分，因为 `TIME` 类型不包含 `DATE` 信息

转换 `TIME` 值为其他时态类型时，`DATE` 部分使用 `CURRENT_DATE()` 的值，`TIME` 被
解释为运行时间，而不是一天的某个时间，然后再加上日期。这意味着如果 `TIME` 值位于
'00:00:00' 到 '23:59:59' 这个范围之外，则结果的 `DATE` 部分不同于当前的 `DATE`。

假设当前的 `DATE` 是 '2012-01-01'，则三个 `TIME` 值 '12:00:00', '24:00:00',
'-12:00:00' 转换为 `DATETIME` 或 `TIMESTAMP` 值时，结果分别是：'2012-01-01
12:00:00', '2012-01-02 00:00:00', '2011-12-31 12:00:00'

转换 `TIME` 类型为 `DATE` 类型类似，但是从结果中丢弃 `TIME` 部分，上例中结果分别
为：'2012-01-01', '2012-01-02', '2011-12-31'

显式的转换可以覆盖隐式的转换，例如，比较 `DATETIME` 和 `DATE` 值时，通过在
`DATE` 值的后面添加 `TIME` 部分 '00:00:00'，可以将其强制转换为 `DATETIME` 类型。
要执行忽略 `DATETIME` 中 `TIME` 部分的比较，按下面的方式使用 `CAST()` 函数：
```
date_col = CAST(datetime_col AS DATE)
```

把 `TIME` 和 `DATETIME` 值转换为数值形式（例如，通过添加 `+0`）时取决于值是否包
含小数秒部分。当 N 为 0 或 N 被省略时，`TIME(N)` 或 `DATETIME(N)` 被转换为整型，
当 N 大于 0 时，被转换为有 N 位小数的 `DECIMAL` 值：
```
mysql> SELECT CURTIME(), CURTIME()+0, CURTIME(3)+0;
+-----------+-------------+--------------+
| CURTIME() | CURTIME()+0 | CURTIME(3)+0 |
+-----------+-------------+--------------+
| 09:28:00  | 92800       | 92800.887    |
+-----------+-------------+--------------+

mysql> SELECT NOW(), NOW()+0, NOW(3)+0;
+---------------------+----------------+--------------------+
| NOW()               | NOW()+0        | NOW(3)+0 |
+---------------------+----------------+--------------------+
| 2012-08-15 09:28:00 | 20120815092800 | 20120815092800.889 |
+---------------------+----------------+--------------------+
```

## 11.3.8 Two-Digit Years in Dates

两位数年份的日期值是模糊的，因为世纪是未知的。这些值必须被解释为四位数形式，因为
在 MySQL 内部使用了 4 位数字存储年份。

对于 `DATETIME`, `DATE`, `TIMESTAMP` 类型，MySQL 使用下列规则解释年值模糊的日期：
* 00-69的年值转换为2000-2069年。
* 70-99年的年值被转换为1970-1999年。

对于 `YEAR` 类型，规则和上面一样。但也有例外情况：一个数值 `00` 插入到 `YEAR(4)`
列，结果是 `0000`，而不是 `2000`。要想在 `YEAR(4)` 列中插入零值，但是被解释为
`2000`，请插入字符串 `'0'` 或 `'00'`

Remember that these rules are only heuristics that provide reasonable guesses as to what your data values mean. If the rules used by MySQL do not produce the values you require, you must provide unambiguous input containing four-digit year values.
请记住，这些规则仅仅是对您的数据值意味着什么的合理猜测。如果 MySQL 所使用的规则不能产生您所需要的值，那么您必须提供包含四位数年值的明确输入。

`ORDER BY` 子句可以正确的排序两位数的年。

一些函数如 `MIN()` 和 `MAX()` 将 `YEAR` 转换为数字。这意味着这些函数不能正确地处
理两位数的年值。在这种情况下，解决方案是将 `YEAR` 转换为四位数年格式。
