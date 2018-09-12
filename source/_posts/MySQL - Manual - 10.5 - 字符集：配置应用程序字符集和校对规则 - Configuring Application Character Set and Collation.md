---
title: MySQL - Manual - 10.5 - 字符集：配置应用程序字符集和校对规则 - Configuring Application Character Set and Collation
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL 字符集：配置应用程序字符集和校对规则

<!--more-->

对于使用 MySQL 默认的字符集和校对规则（`utf8mb4`，`utf8mb4_0900_ai_ci`）存储数据
的应用程序，不需要特殊的配置。如果应用程序需要使用不同的字符集或校对规则来进行数
据存储，那么您可以通过以下几种方式配置字符集信息：

* 为每个数据库指定字符设置。例如，使用一个数据库的应用程序可能使用 `utf8mb4` 的
  默认值，而使用另一个数据库的应用程序可能使用 `sjis`。

* 在服务器启动时指定字符设置。这将使得服务器为所有不做其他安排的应用程序使用给定
  的设置。

* 在配置时指定字符设置，如果您从源代码构建MySQL。这使得服务器将给定的设置用作所
  有应用程序的默认设置，而无需在服务器启动时指定它们。

当不同的应用程序需要不同的字符设置时，为每个数据库分别指定字符设置提供了很大的灵
活性。但如果大多数或所有应用程序使用相同的字符集，那么在服务器启动或配置时指定字
符设置可能是最方便的。

对每个数据库进行的字符设置或在服务器启动时进行的字符设置都是控制数据存储的字符集
。应用程序还必须告诉服务器客户端/服务器通信时使用哪个字符集，如下面的说明所述。

这里展示的示例假设在特定上下文中使用 `latin1` 字符集和 `latin1_swedish_ci` 校对
规则，作为默认值 `utf8mb4` 和 `utf8mb4_0900_ai_ci` 的替代。

* 为每个数据库指定字符设置：
 
  要创建一个数据库，它的表使用给定的缺省字符集和校对规
  则用于数据存储，请使用如下“CREATE DATABASE”语句：

    ```
    CREATE DATABASE mydb
    CHARACTER SET latin1
    COLLATE latin1_swedish_ci;
    ```

  在数据库中创建的表将默认为任何字符列使用 `latin1` 和 `latin1_swedish_ci`。

  使用此数据库的应用程序也应该在每次连接时配置它们与服务器的连接。这可以通过在连
  接后执行一个 `SET NAMES` 的语句来完成。无论什么连接方法（`mysql` 客户端、PHP
  脚本等等），都可以使用该语句。

  在某些情况下，可以通过配置连接以其他方式来使用所需的字符集。例如，要使用
  `mysql` 连接，您可以指定 `--default-character-set=latin1` 命令行选项，以达到与
  `SET NAMES 'latin1'` 相同的效果。

  有关配置客户端连接的更多信息，请参考 Section 10.4, “Connection Character Sets
  and Collations”

  注意：
  如果您使用 `ALTER DATABASE` 来改变数据库的默认字符集或校对规则，那么必须删除和
  重新创建数据库中的那些使用原来默认值的现有存储例程，以便它们使用新的缺省值。（
  在一个存储的例程中，如果字符集或校对规则没有显式指定的话，字符数据类型的变量使
  用数据库默认值。参考：Section 13.1.15, “CREATE PROCEDURE and CREATE FUNCTION
  Syntax”

* 在服务器启动时指定字符设置：
 
  要在服务器启动时选择一个字符集和校对规则，请使用
  `--character-set-server` 和 `--collation-server` 选项。例如，要在选项文件中指
  定选项，包括以下几行：
 
    ```
    [mysqld]
    character-set-server=latin1
    collation-server=latin1_swedish_ci
    ```

  这些设置应用于服务器范围，并应用于任何应用程序创建的数据库的默认值，以及在这些
  数据库中创建的表。

  如前所述，对于应用程序来说，在连接之后使用 `SET NAMES` 或等价物来配置它们的连
  接仍然是必要的。您可能会尝试使用`--init_connect="SET NAMES 'latin1'"` 启动服务
  器从而为每个连接的客户端自动执行 `SET NAMES`。然而，这可能会产生不一致的结果，
  因为对于具有 `CONNECTION_ADMIN` 或 `SUPER` 特权的用户来说，`init_connect` 值不
  会被执行。

* 在 MySQL 配置时指定字符设置：
 
  如果您从源代码配置和构建 MySQL 时要选择一个字符集和校对规则，请使用 CMake 选项
  `DEFAULT_CHARSET` 和 `DEFAULT_COLLATION`：

    ```
    cmake . -DDEFAULT_CHARSET=latin1 \
    -DDEFAULT_COLLATION=latin1_swedish_ci
    ```

  由此产生的服务器使用 `latin1` 和 `latin1_swedish_ci` 作为数据库和表以及客户端
  连接的默认值。在服务器启动时，没有必要使用 `--character-set-server` 和
  `--collation-server` 来指定这些缺省值。对于应用程序来说，在连接到服务器后，也
  没有必要使用 `SET NAMES` 或等价物来配置它们的连接。

无论如何配置 MySQL 字符集以供应用程序使用，您都必须考虑这些应用程序执行的环境。
例如，如果您使用从编辑器创建的文件中提取的 UTF-8 文本发送语句，您应该通过环境设
置的本地变量将文件编辑为 UTF-8 (you should edit the file with the locale of your
environment set to UTF-8)，以便文件编码是正确的，操作系统能正确地处理它。如果您
在终端窗口中使用 mysql 客户端，则必须将窗口配置为使用 UTF-8 否则字符可能无法正确
显示。对于在 Web 环境中执行的脚本，脚本必须正确地处理与 MySQL 服务器交互的字符编
码，并且必须生成正确表明了编码的页面，以便浏览器知道如何显示页面的内容。例如，你
可以在 `<head>` 中包含这个 `<meta>` 标签：
```
<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
```
