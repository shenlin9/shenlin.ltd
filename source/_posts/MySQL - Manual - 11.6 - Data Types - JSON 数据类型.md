---
title: MySQL - Manual - 11.6 - Data Types - JSON 数据类型
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

MySQL JSON 数据类型

<!--more-->

JSON：JavaScript Object Notation，JavaScript 对象表示法

MySQL 支持由 RFC 7159 定义的原生 JSON 数据类型，它支持对 JSON 文档中的数据进行高
效访问。通过在字符串列中存储 JSON 格式的字符串，JSON 数据类型提供了这些优势：

* 自动验证存储在 JSON 列中的 JSON 文档。无效的文档会产生错误。

* 优化存储格式。存储在 JSON 列中的 JSON 文档被转换成一种内部格式，允许对文档元素
  进行快速读取访问。当服务器稍后必须读取以二进制格式存储的 JSON 值时，不需要从文
  本表示中解析值。这种二进制格式被结构化，使得服务器能够直接通过键或数组索引查找
  子对象或嵌套的值，而不需要在文档中读取其之前或之后的所有值。

MySQL 8.0 也支持 RFC 7396 定义的 `JSON Merge Patch` 格式，使用
`JSON_MERGE_PATCH()` 函数。查看此函数的描述，还有 `Normalization, Merging, and
Autowrapping of JSON Values`，举例和更多信息。

存储 JSON 文档所需的空间与 `LONGBLOB` 或 `LONGTEXT` 大致相同；有关更多信息，请参
阅 Section 11.8, “Data Type Storage Requirements”。重要的是要记住，存放在 JSON
列中的任何 JSON 文档的大小都限制在系统变量 `max_allowed_packet` 的值上。(当服务
器在内存内部操作 JSON 值时，它可以比这个大；当服务器存储它时，这个限制就起作用了
)。您可以使用 `JSON_STORAGE_SIZE()` 函数获得储存 JSON 文档所需的空间数量；请注意
，对于 JSON 列来说，这个函数返回的存储大小和值是先被本列使用，优先于在对列进行的
任何部分更新之前(note that for a JSON column, the storage size and thus the
value returned by this function is that used by the column prior to any partial
updates that may have been performed on it)(请参阅本节后面的 JSON 部分更新优化的
讨论)。

JSON 列不能有默认值

除了 JSON 数据类型之外，还可以使用一组 SQL 函数来执行 JSON 值上的操作，比如创建
、操作和搜索。下面的讨论展示了这些操作的示例。关于个别功能的细节，参考 Section
12.16, “JSON Functions”.

还可以使用一组用于 GeoJSON 值操作的空间函数，参考 Section 12.15.11,“Spatial
GeoJSON Functions”.

就像其他二进制类型的列一样，JSON 列没有直接编入索引；然而，您可以从 JSON 列提取
标量值生成列，然后在此生成的列上创建一个索引。请参阅索 `13.1.18.9 Secondary
Indexes and Generated Columns -- Indexing a Generated Column to Provide a JSON
Column Index` 以获得一个详细的示例。

MySQL 优化器还会在与 JSON 表达式匹配的虚拟列中寻找兼容的索引。

**Partial Updates of JSON Values**

在 MySQL 8.0 中，优化器可以对 JSON 列执行部分就地更新，而不是删除旧文档后将新文
档完整地写入此列。这种优化可以用于满足以下条件的更新：

* 被更新的列被声明为JSON。

* `UPDATE` 语句使用这三个函数中的任何一个 `JSON_SET()`, `JSON_REPLACE()`,
  `JSON_REMOVE()` 来更新列。列的直接赋值（例如 `UPDATE mytable SET jcol = '{"a":
  10, "b": 25}'`）不能作为部分更新执行。

  在一个 `UPDATE` 语句中更新多个 JSON 列可以用这种方式进行优化；MySQL 只可以对那
  些使用刚刚列出的三个函数来更新它们值的列进行部分更新。

* 输入列和目标列必须是同一列；像这样的语句 `UPDATE mytable SET jcol1 =
  JSON_SET(jcol2, '$.a', 100)` 不能作为部分更新执行。

  只要输入列和目标列相同，更新就可以用任何组合的方式嵌套调用前面列出的任何函数。

* 所有的变更都是用新值来替换现有的数组值或者对象值，并且不要向父对象或父数组中添
* 加任何新的元素。

* 被替换的值必须至少与替换值一样大。换句话说，新值不能比旧值大。

  当先前的部分更新为更大的值留下足够的空间时，可能会出现这种需求的例外情况。您可
  以使用函数 `JSON_STORAGE_FREE()` 查看 JSON 列的任何部分更新释放了多少空间。

这种局部更新可以使用紧凑的格式写入二进制日志以节省空间；这可以通过将系统变量
`binlog_row_value_options` 设置为 `PARTIAL_JSON` 来启用。有关更多信息，请参阅该
变量的描述。

接下来的几个部分将提供关于 JSON 值的创建和操作的基本信息。

## Creating JSON Values

一个 JSON 数组包含一个由逗号分隔的值列表，并包含在字符 `[` 和 `]` 中：
```
["abc", 10, null, true, false]
```

一个 JSON 对象包含一组由逗号分隔的键值对，并包含在字符 `{` 和 `}` 中：
```
{"k1": "value", "k2": 10}
```

如示例所示，JSON 对象中的键必须是字符串，JSON 数组和对象可以包含标量值：字符串或
数字、JSON null 字面量或 JSON 布尔值 true 或 false 的字面量，时间（日期、时间或
日期时间）标量值：
```
["12:18:29.000000", "2015-07-29", "2015-07-29 12:18:29.000000"]
```

在 JSON 数组元素和 JSON 对象键值中允许嵌套：
```
[99, {"id": "HK500", "cost": 75.99}, ["hot", "cold"]]
{"k1": "value", "k2": [10, 20]}
```

你也可以从 MySQL 提供的许多函数中获得 JSON 值(Section 12.16.2, “Functions That
Create JSON Values”)，也可以使用 `CAST(value AS JSON)` 将其他类型的值强制转换为
JSON 类型(参见下面章节 `Converting between JSON and non-JSON values`)。接下来的
几段描述了 MySQL 如何处理作为输入的 JSON 值。

在 MySQL 中，JSON 值被写成字符串。在需要 JSON 值的上下文中 MySQL 解析使用的任何
字符串，如果它不是有效的 JSON 字符串，则会产生错误。这些上下文包括：将一个值插入
到 JSON 数据类型的列中，将一个参数传递给一个期望 JSON 值的函数(MySQL 的 JSON 函
数在文档中通常显示为 `json_doc` 或 `json_val`)，如下示例所示：

* 将一个有效的 JSON 值插入一个 JSON 列会成功，无效的 JSON 值则失败：
    ```
    mysql> CREATE TABLE t1 (jdoc JSON);
    Query OK, 0 rows affected (0.20 sec)

    mysql> INSERT INTO t1 VALUES('{"key1": "value1", "key2": "value2"}');
    Query OK, 1 row affected (0.01 sec)

    mysql> INSERT INTO t1 VALUES('[1, 2,');
    ERROR 3140 (22032) at line 2: Invalid JSON text:
    "Invalid value." at position 6 in value (or column) '[1, 2,'.
    ```
  错误信息里的 “at position N”的位置是从 0 开始，但是应该被认为是一个值实际发
  生的问题的粗略指示。

* `JSON_TYPE()` 函数需要一个 JSON 参数，并尝试将其解析为 JSON 值。如果参数是有效
  的 JSON 值，它会返回值的 JSON 类型，否则产生一个错误：
    ```
    mysql> SELECT JSON_TYPE('["a", "b", 1]');
    +----------------------------+
    | JSON_TYPE('["a", "b", 1]') |
    +----------------------------+
    | ARRAY |
    +----------------------------+

    mysql> SELECT JSON_TYPE('"hello"');
    +----------------------+
    | JSON_TYPE('"hello"') |
    +----------------------+
    | STRING |
    +----------------------+

    mysql> SELECT JSON_TYPE('hello');
    ERROR 3146 (22032): Invalid data type for JSON data in argument 1
    to function json_type; a JSON string or JSON type is required.
    ```

MySQL 使用 `utf8mb4` 字符集和 `utf8mb4bin` 校对规则来处理 JSON 上下文中的字符串
。其他字符集中的字符串将根据需要转换为 `utf8mb4`。(对于 `ascii` 或 `utf8` 字符集
中的字符串，不需要转换，因为 `ascii` 和 `utf8` 是 `utf8mb4` 的子集。)

作为使用字符串字面量编写 JSON 值的替代方法，可以用函数从组件元素中组合 JSON 值。
`JSON_ARRAY()` 接受一个（可能是空的）值列表，并返回包含这些值的 JSON 数组：
```
mysql> SELECT JSON_ARRAY('a', 1, NOW());
+----------------------------------------+
| JSON_ARRAY('a', 1, NOW()) |
+----------------------------------------+
| ["a", 1, "2015-07-27 09:43:47.000000"] |
+----------------------------------------+
```

`JSON_OBJECT()` 接受一个（可能是空的）键值对列表，并返回包含那些键值对的 JSON 对
象：
```
mysql> SELECT JSON_OBJECT('key1', 1, 'key2', 'abc');
+---------------------------------------+
| JSON_OBJECT('key1', 1, 'key2', 'abc') |
+---------------------------------------+
| {"key1": 1, "key2": "abc"} |
+---------------------------------------+
```

`JSON_MERGE_PRESERVE()` 接受两个或更多个 JSON 文档并返回合并结果：
```
mysql> SELECT JSON_MERGE_PRESERVE('["a", 1]', '{"key": "value"}');
+-----------------------------------------------------+
| JSON_MERGE_PRESERVE('["a", 1]', '{"key": "value"}') |
+-----------------------------------------------------+
| ["a", 1, {"key": "value"}]                          |
+-----------------------------------------------------+
1 row in set (0.00 sec)
```

关于合并规则的更多信息，参考下面章节的 `Normalization, Merging, and Autowrapping
of JSON Values`.

(MySQL 8.0.3 和更高版本还支持 `JSON_MERGE_PATCH()`，在行为上有些不同，两个函数的
比较参考 `12.16.4 Functions That Modify JSON Values - JSON_MERGE_PATCH()
compared with JSON_MERGE_PRESERVE()`)

JSON 可被赋值给用户自定义变量：
```
mysql> SET @j = JSON_OBJECT('key', 'value');

mysql> SELECT @j;
+------------------+
| @j               |
+------------------+
| {"key": "value"} |
+------------------+
```
但是，用户自定义变量不能是 JSON 数据类型，所以尽管前面的例子中的 `@j` 看起来像一
个 JSON 值，并且具有与 JSON 值相同的字符集和校对规则，但它不是 JSON 数据类型。相
反，当 `JSON_OBJECT()` 的结果赋值给用户自定义变量时被转换为字符串。

通过转换 JSON 值生成的字符串字符集为 `utf8mb4` 和 `utf8mb4_bin` 校对规则：
```
mysql> SELECT CHARSET(@j), COLLATION(@j);
+-------------+---------------+
| CHARSET(@j) | COLLATION(@j) |
+-------------+---------------+
| utf8mb4 | utf8mb4_bin |
+-------------+---------------+
```

因为 `utf8mb4_bin` 是一个二进制校对规则，所以 JSON 值的比较是大小写敏感的：
```
mysql> SELECT JSON_ARRAY('x') = JSON_ARRAY('X');
+-----------------------------------+
| JSON_ARRAY('x') = JSON_ARRAY('X') |
+-----------------------------------+
| 0 |
+-----------------------------------+
```

大小写敏感还适用于 JSON 的字面量：null，false，true，它们必须被写为小写形式：
```
mysql> SELECT JSON_VALID('null'), JSON_VALID('Null'), JSON_VALID('NULL');
+--------------------+--------------------+--------------------+
| JSON_VALID('null') | JSON_VALID('Null') | JSON_VALID('NULL') |
+--------------------+--------------------+--------------------+
| 1 | 0 | 0 |
+--------------------+--------------------+--------------------+

mysql> SELECT CAST('null' AS JSON);
+----------------------+
| CAST('null' AS JSON) |
+----------------------+
| null |
+----------------------+
1 row in set (0.00 sec)

mysql> SELECT CAST('NULL' AS JSON);
ERROR 3141 (22032): Invalid JSON text in argument 1 to function cast_as_json:
"Invalid value." at position 0 in 'NULL'.
```

JSON 字面量的大小写敏感不同于 SQL 的 NULL，TRUE，FALSE，后者可写为任何大小写形式：
```
mysql> SELECT ISNULL(null), ISNULL(Null), ISNULL(NULL);
+--------------+--------------+--------------+
| ISNULL(null) | ISNULL(Null) | ISNULL(NULL) |
+--------------+--------------+--------------+
| 1 | 1 | 1 |
+--------------+--------------+--------------+
```

有时需要将引用字符（" 或 '）插入到 JSON 文档中。假设您想要插入一些包含字符串的
JSON 对象，这些字符串表示关于 MySQL 的一些事实，每一个都与一个适当的关键字配对，
并使用下面所示的 SQL 语句创建的表：
```
mysql> CREATE TABLE facts (sentence JSON);
```

在这些关键字的句子中，有一个是：
```
mascot: The MySQL mascot is a dolphin named "Sakila".
```

将其作为 JSON 对象插入 `facts` 表的一种方法是使用 MySQL 的 `JSON_OBJECT()` 函数
。在这种情况下，您必须使用反斜杠来转义每个引用字符，如下所示：
```
mysql> INSERT INTO facts VALUES (JSON_OBJECT("mascot", "Our mascot is a dolphin
named \"Sakila\"."));
```

如果您以 JSON 对象字面量的方式将值插入就行不通，在这种情况下，您必须使用双反斜杠
转义序列，如下所列：
```
mysql> INSERT INTO facts VALUES ('{"mascot": "Our mascot is a dolphin named
\\"Sakila\\"."}');
```

使用双反斜杠使 MySQL 无法执行转义序列处理，而是使其将字符串字面量传递给存储引擎
进行处理。在使用刚刚展示的任何一种方法插入 JSON 对象后，您通过执行一个简单的
SELECT 都可以看到在 JSON 列值中出现了反斜杠，如下所示：
```
mysql> SELECT sentence FROM facts;
+---------------------------------------------------------+
| sentence |
+---------------------------------------------------------+
| {"mascot": "Our mascot is a dolphin named \"Sakila\"."} |
+---------------------------------------------------------+
```

用 `mascot` 作为关键字来查找这个特殊的句子，你可以使用列路径操作符 `->`，如下所
示：
```
mysql> SELECT col->"$.mascot" FROM qtest;
+---------------------------------------------+
| col->"$.mascot" |
+---------------------------------------------+
| "Our mascot is a dolphin named \"Sakila\"." |
+---------------------------------------------+
1 row in set (0.00 sec)
```

仍完整保留了反斜杠以及周围的引号。要显示所期望的使用 `mascot` 作为键的值，但是不
包括周围的引号或任何转义，使用内联(inline)路径操作符 `->>`，像这样：
```
mysql> SELECT sentence->>"$.mascot" FROM facts;
+-----------------------------------------+
| sentence->>"$.mascot" |
+-----------------------------------------+
| Our mascot is a dolphin named "Sakila". |
+-----------------------------------------+
```

注意：

如果启用了服务器的 SQL 模式 `NO_BACKSLASH_ESCAPES`，则前面的例子不会像展示的那样
运行。如果该模式被设置，则可以使用一个反斜杠代替双反斜杠来插入 JSON 对象字面量，
并且保留反斜杠。如果启用了 `NO_BACKSLASH_ESCAPES` 模式，则在执行插入操作时使用
`JSON_OBJECT()` 函数，那么你必须交替使用单引号和双引号，如下所列：
```
mysql> INSERT INTO facts VALUES
> (JSON_OBJECT('mascot', 'Our mascot is a dolphin named "Sakila".'));
```
请参阅 `JSON_UNQUOTE()` 函数的描述，以获得关于此模式对 JSON 值中转义字符的影响的
更多信息。

## Normalization, Merging, and Autowrapping of JSON Values

当一个字符串被解析并被发现是一个有效的 JSON 文档时，它也被规范化了。这意味着，从
左到右读取文档，在文档后面找到的使用了重复键的成员，前面的就被丢弃了。下列
`JSON_OBJECT()` 调用所产生的对象值只包含第二个 `key1` 元素，因为该键名在值较早的
时候出现，如下所示：
```
mysql> SELECT JSON_OBJECT('key1', 1, 'key2', 'abc', 'key1', 'def');
+------------------------------------------------------+
| JSON_OBJECT('key1', 1, 'key2', 'abc', 'key1', 'def') |
+------------------------------------------------------+
| {"key1": "def", "key2": "abc"}                       |
+------------------------------------------------------+
```

当值被插入到 JSON 列中时，规范化也会执行，如下所示：
```
mysql> CREATE TABLE t1 (c1 JSON);

mysql> INSERT INTO t1 VALUES
> ('{"x": 17, "x": "red"}'),
> ('{"x": 17, "x": "red", "x": [3, 5, 7]}');

mysql> SELECT c1 FROM t1;
+------------------+
| c1 |
+------------------+
| {"x": "red"} |
| {"x": [3, 5, 7]} |
+------------------+
```

这种“最后的重复键获胜”的行为是被 RFC 7159 建议的的，并且由大多数 JavaScript 解
析器实现了。（Bug #86866，Bug #26369555）

在 MySQL 8.0.3 之前的版本，键重复的成员，后面的被丢弃。下面的函数
`JSON_OBJECT()` 调用产生的对象值不包括第二个 `key1` 元素，因为其键名先前已经出现
过了：
```
mysql> SELECT JSON_OBJECT('key1', 1, 'key2', 'abc', 'key1', 'def');
+------------------------------------------------------+
| JSON_OBJECT('key1', 1, 'key2', 'abc', 'key1', 'def') |
+------------------------------------------------------+
| {"key1": 1, "key2": "abc"} |
+------------------------------------------------------+
```

在 MySQL 8.0.3 之前，当将值插入到 JSON 列中时，这个“第一个重复键获胜”的规范也
会被执行。
```
mysql> CREATE TABLE t1 (c1 JSON);

mysql> INSERT INTO t1 VALUES
> ('{"x": 17, "x": "red"}'),
> ('{"x": 17, "x": "red", "x": [3, 5, 7]}');

mysql> SELECT c1 FROM t1;
+-----------+
| c1        |
+-----------+
| {"x": 17} |
| {"x": 17} |
+-----------+
```
MySQL 还丢弃了原始 JSON 文档中的键、值或元素之间的额外空格。为了使查找更高效，它
还对 JSON 对象的键进行排序。您应该意识到，这种顺序的结果可能会发生变化，并且不能
保证在不同版本之间保持一致。

产生 JSON 值的 MySQL 函数总是返回标准化的值，参考 Section 12.16.2, “Functions
That Create JSON Values”。

### Merging JSON Values

MySQL 8.0.3 以及以后的版本支持两种合并算法，分别由函数 `JSON_MERGE_PRESERVE()`
和 `JSON_MERGE_PATCH()` 实现。它们处理重复键的方式不同：`JSON_MERGE_PRESERVE()`
保留重复的键值。而 `JSON_MERGE_PATCH()` 丢弃所有但只保留最后的键值。接下来的几段
分别解释了这两个函数如何处理 JSON 文档的不同组合（即对象和数组）的合并。

    注意：
    `JSON_MERGE_PRESERVE()` 和以前版本 MySQL 的 `JSON_MERGE()` 函数是一样的，这
    是它在 MySQL 8.0.3 版本里重新的命名。MySQL 8.0 里仍然支持 `JSON_MERGE()` 作
    为 `JSON_MERGE_PRESERVE()` 的别名，但是在将来的版本中会被弃用并被移除。

#### Merging arrays

在合并多个数组的上下文中，数组被合并到一个单独的数组中，`JSON_MERGE_PRESERVE()`
通过将后面的指定数组连接到第一个数组的末尾来实现这一点。`JSON_MERGE_PATCH()` 把
每个参数看作是由单个元素组成的数组（因此 0 作为它的索引），然后应用“最后的重复
键获胜”逻辑来选择最后一个参数。您可以比较这个查询所显示的结果：
```
mysql> SELECT
-> JSON_MERGE_PRESERVE('[1, 2]', '["a", "b", "c"]', '[true, false]') AS Preserve,
-> JSON_MERGE_PATCH('[1, 2]', '["a", "b", "c"]', '[true, false]') AS Patch\G
*************************** 1. row ***************************
Preserve: [1, 2, "a", "b", "c", true, false]
Patch: [true, false]
```

#### Merging objects

合并对象时，多个对象产生一个单一对象。

如果多个对象具有同一个键，则 `JSON_MERGE_PRESERVE()` 将该键的所有唯一值组合成一
个数组，这个数组随后被用作结果中该键的值。`JSON_MERGE_PATCH()` 丢弃了重复键的值
，从左到右执行，因此结果只包含该键的最后一个值。下面的查询说明了重复键 `a` 的结
果差异：
```
mysql> SELECT
-> JSON_MERGE_PRESERVE('{"a": 1, "b": 2}', '{"c": 3, "a": 4}', '{"c": 5, "d": 3}') AS Preserve,
-> JSON_MERGE_PATCH('{"a": 3, "b": 2}', '{"c": 3, "a": 4}', '{"c": 5, "d": 3}') AS Patch\G
*************************** 1. row ***************************
Preserve: {"a": [1, 4], "b": 2, "c": [3, 5], "d": 3}
Patch: {"a": 4, "b": 2, "c": 5, "d": 3}
```

#### Merging Nonarray values

在需要数组值的上下文中使用的非数组值是被自动封装的：值被 `[` 和 `]` 字符包围将其
转换为数组。在下面的语句中，每个参数都被自动封装为一个数组（[1], [2]），然后接着
将它们以上面数组合并的规则合并，最终产生单个结果数组；与前两种情况一样，
`JSON_MERGE_PRESERVE()` 组合了具有相同键的值，而 `JSON_MERGE_PATCH()` 丢弃所有重
复键的值，除了最后一个，如下所示：
```
mysql> SELECT
-> JSON_MERGE_PRESERVE('1', '2') AS Preserve,
-> JSON_MERGE_PATCH('1', '2') AS Patch\G
*************************** 1. row ***************************
Preserve: [1, 2]
Patch: 2
```

#### Merging arrays and objects

对象和数组混合着合并时，先将对象封装为只有一个元素的数组，然后再按数组合并的规则
合并：根据合并函数的选择（`JSON_MERGE_PRESERVE()` 或 `JSON_MERGE_PATCH()`），或
者组合值，或者“最后的重复键获胜”，在下例中可以看到：
```
mysql> SELECT
-> JSON_MERGE_PRESERVE('[10, 20]', '{"a": "x", "b": "y"}') AS Preserve,
-> JSON_MERGE_PATCH('[10, 20]', '{"a": "x", "b": "y"}') AS Patch\G
*************************** 1. row ***************************
Preserve: [10, 20, {"a": "x", "b": "y"}]
Patch: {"a": "x", "b": "y"}
```

## Searching and Modifying JSON Values

JSON 路径表达式用于选择 JSON 文档中的值。

路径表达式对于提取部分 JSON 文档或修改 JSON 文档的函数非常有用，可以指定该文档中
要操作的位置。例如，下列查询从一个 JSON 文档中根据 `name` 键提取出成员值：
```
mysql> SELECT JSON_EXTRACT('{"id": 14, "name": "Aztalan"}', '$.name');
+---------------------------------------------------------+
| JSON_EXTRACT('{"id": 14, "name": "Aztalan"}', '$.name') |
+---------------------------------------------------------+
| "Aztalan" |
+---------------------------------------------------------+
```

Path syntax uses a leading `$` character to represent the JSON document under consideration, optionally followed by selectors that indicate successively more specific parts of the document:
路径语法使用一个前导 `$` 字符来表示正在考虑的 JSON 文档，后面的可选地选择器会显
示文档中更多特定的部分：

* 一个句点后面跟着一个键名，表示一个对象中给定键的命名成员。如果没有引号的名称在
  路径表达式中不合法（例如包含空格），则必须指定键名时使用双引号。

* 如果 `path` 选择了一个数组，`path` 后面附加的 `[N]` 则指明了数组内位置 N 的值
  。数组位置为整型从 0 开始。如果 `path` 选择的不是一个数组值，则 `path[0]` 和
  `path` 的值相同：

    ```
    mysql> SELECT JSON_SET('"x"', '$[0]', 'a');
    +------------------------------+
    | JSON_SET('"x"', '$[0]', 'a') |
    +------------------------------+
    | "a" |
    +------------------------------+
    1 row in set (0.00 sec)
    ```

* `[M to N]` 指定了一个数组值的子集或范围，起始于位置 `M` 的值，结束于位置 `N`
  的值。

  `last` 为最右边数组元素索引的同义词。如果 `path` 选择的不是数组值，
  `path[last]` 的值与 `path` 相同，如本节后面所示（参见 Rightmost array element
  ）。

  还支持数组元素的相对寻址。

* 路径可以包含 `*` 或 `**` 通配符:
    * `.[*]` 表示 JSON 对象中所有成员的值。
    * `[*]` 表示 JSON 数组中所有元素的值。
    * `prefix**suffix` 表示所有从命名前缀开始并以命名后缀结尾的路径。

* 文档里不存在的路径等价于 NULL，因为是在对不存在的数据进行计算

如果 `$` 引用的是下列有三个元素的 JSON 数组：

```
[3, {"a": [5, 6], "b": 10}, [99, 100]]
```

则：
* `$[0]` 计算结果为 3.
* `$[1]` 计算结果为 {"a": [5, 6], "b": 10}.
* `$[2]` 计算结果为 [99, 100].
* `$[3]` 计算结果为 NULL

因为 `$[1]` 和 `$[2]` 计算结果为非标量值，它们可以作为选择嵌套值的更具体的路径表
达式的基础，例如：
* `$[1].a` 计算结果为 [5, 6].
* `$[1].a[1]` 计算结果为 6.
* `$[1].b` 计算结果为 10.
* `$[2][0]` 计算结果为 99.

正如前面所提到的，如果未引用的键名在路径表达式中不合法，那么指出键名的的路径组件
必须引用。让 `$` 引用这个值：
```
{"a fish": "shark", "a bird": "sparrow"}
```

键名都包含空格必须被引用起来：
* `$."a fish"` evaluates to shark.
* `$."a bird"` evaluates to sparrow.

使用通配符的路径计算结果为包含多个值的一个数组：
```
mysql> SELECT JSON_EXTRACT('{"a": 1, "b": 2, "c": [3, 4, 5]}', '$.*');
+---------------------------------------------------------+
| JSON_EXTRACT('{"a": 1, "b": 2, "c": [3, 4, 5]}', '$.*') |
+---------------------------------------------------------+
| [1, 2, [3, 4, 5]] |
+---------------------------------------------------------+

mysql> SELECT JSON_EXTRACT('{"a": 1, "b": 2, "c": [3, 4, 5]}', '$.c[*]');
+------------------------------------------------------------+
| JSON_EXTRACT('{"a": 1, "b": 2, "c": [3, 4, 5]}', '$.c[*]') |
+------------------------------------------------------------+
| [3, 4, 5]                                                  |
+------------------------------------------------------------+
```

下面的例子中，路径 `$**.b` 计算结果为多个路径(`$.a.b` and `$.c.b`)，结果为匹配路
径值的数组：
```
mysql> SELECT JSON_EXTRACT('{"a": {"b": 1}, "c": {"b": 2}}', '$**.b');
+---------------------------------------------------------+
| JSON_EXTRACT('{"a": {"b": 1}, "c": {"b": 2}}', '$**.b') |
+---------------------------------------------------------+
| [1, 2]                                                  |
+---------------------------------------------------------+
```

**一个数组范围 Ranges from arrays**

使用 `to` 关键字指定 JSON 数组的子集范围，例如 `$[1 to 3]` 包括数组的第二、第三
、第四个元素，如下所示：
```
mysql> SELECT JSON_EXTRACT('[1, 2, 3, 4, 5]', '$[1 to 3]');
+----------------------------------------------+
| JSON_EXTRACT('[1, 2, 3, 4, 5]', '$[1 to 3]') |
+----------------------------------------------+
| [2, 3, 4] |
+----------------------------------------------+
1 row in set (0.00 sec)
```

语法是 `M to N`，`M` 和 `N` 分别是 JSON 数组一系列元素的第一个和最后一个的索引，
`N` 必须大于 `M`，`M` 必须大于等于 0。数组元素索引从 0 开始。

您可以在支持通配符的上下文中使用范围。

**最右边的数组元素 Rightmost array element**

关键字 `last` 是数组中最后一个元素的索引的同义词，表达式 `last - N` 可用于在定义
范围内相对寻址，像这样：
```
mysql> SELECT JSON_EXTRACT('[1, 2, 3, 4, 5]', '$[last-3 to last-1]');
+--------------------------------------------------------+
| JSON_EXTRACT('[1, 2, 3, 4, 5]', '$[last-3 to last-1]') |
+--------------------------------------------------------+
| [2, 3, 4] |
+--------------------------------------------------------+
1 row in set (0.01 sec)
```

如果指定的路径值不是数组，则相当于路径值被封装为只包含它自身一个元素的数组：
```
mysql> SELECT JSON_REPLACE('"Sakila"', '$[last]', 10);
+-----------------------------------------+
| JSON_REPLACE('"Sakila"', '$[last]', 10) |
+-----------------------------------------+
| 10 |
+-----------------------------------------+
1 row in set (0.00 sec)
```

可以使用 JSON 列识别符搭配 `column->path` 和 JSON 路径表达式作为
`JSON_EXTRACT(column, path)` 同义词。参考 Section 12.16.3, “Functions That
Search JSON Values”, 更多信息参考：13.1.18.9 Secondary Indexes and Generated
Columns -- Indexing a Generated Column to Provide a JSON Column Index.

有些函数使用现有的 JSON 文档，以某种方式修改它，并返回作为结果的修改后文档。路径
表达式指示在文档中哪里进行更改。例如，`JSON_SET()`, `JSON_INSERT()`,
`JSON_REPLACE()` 函数每个都使用一个 JSON 文档，外加一个或多个描述在哪里修改文档
和使用值的路径值对。这些函数在处理文档中现有和非现有值的方式上有所不同。


`JSON_SET()` 对于存在的路径替换值，对于不存在的路径添加值：
```
mysql> SET @j = '["a", {"b": [true, false]}, [10, 20]]';

mysql> SELECT JSON_SET(@j, '$[1].b[0]', 1, '$[2][2]', 2);
+--------------------------------------------+
| JSON_SET(@j, '$[1].b[0]', 1, '$[2][2]', 2) |
+--------------------------------------------+
| ["a", {"b": [1, false]}, [10, 20, 2]]      |
+--------------------------------------------+
```
* 这个例子中，路径 `$[1].b[0]` 选择的是一个已存在的值 true，被替换为了路径参数后
  面的值 1，路径 `$[2][2]` 不存在，所以对应的值 2 就被添加到了 `$[2]` 选定的位置
  。 

`JSON_INSERT()` 添加新值但不替换已存在的值：
```
mysql> SET @j = '["a", {"b": [true, false]}, [10, 20]]';

mysql> SELECT JSON_INSERT(@j, '$[1].b[0]', 1, '$[2][2]', 2);
+-----------------------------------------------+
| JSON_INSERT(@j, '$[1].b[0]', 1, '$[2][2]', 2) |
+-----------------------------------------------+
| ["a", {"b": [true, false]}, [10, 20, 2]] |
+-----------------------------------------------+
```

`JSON_REPLACE()` 替换已存在的值但忽略新值，即不添加新值：
```
mysql> SET @j = '["a", {"b": [true, false]}, [10, 20]]';

mysql> SELECT JSON_REPLACE(@j, '$[1].b[0]', 1, '$[2][2]', 2);
+------------------------------------------------+
| JSON_REPLACE(@j, '$[1].b[0]', 1, '$[2][2]', 2) |
+------------------------------------------------+
| ["a", {"b": [1, false]}, [10, 20]] |
+------------------------------------------------+
```

路径值对从左到右匹配，一对路径值匹配后产生的文档，成为下一对路径值使用的文档。

`JSON_REMOVE()` 使用一个 JSON 文档参数和一个或多个路径参数，路径参数指定了要从文
档里移除的值，返回值就是原始文档减去移除值后的文档：
```
mysql> SET @j = '["a", {"b": [true, false]}, [10, 20]]';

mysql> SELECT JSON_REMOVE(@j, '$[2]', '$[1].b[1]', '$[1].b[1]');
+---------------------------------------------------+
| JSON_REMOVE(@j, '$[2]', '$[1].b[1]', '$[1].b[1]') |
+---------------------------------------------------+
| ["a", {"b": [true]}] |
+---------------------------------------------------+
```

这些路径有这些影响：
* `$[2]` 匹配 `[10, 20]` 并移除它
* `$[1].b[1]` 的第一个实例匹配 b 元素里的 false 并移除它
* `$[1].b[1]` 的第二个实力没有匹配，因为上一个匹配已经把它移除了，路径已不存在，
  所以没效果。

## Comparison and Ordering of JSON Values

JSON 值可以使用这些操作符进行比较：`=, <, <=, >, >=, <>, !=, <=>`

下列的比较操作符和函数还不支持 JSON　值：

* BETWEEN
* IN()
* GREATEST()
* LEAST()

上面列出的比较运算符和函数的一个变通方案是将 JSON 值转换成原生 MySQL 数值或字符
串数据类型，这样它们就有一个一致的非 JSON 标量类型。

JSON 值的比较在两个级别上进行。第一个级别比较是基于比较值的 JSON 类型。如果类型
不同，比较结果只由哪种类型具有更高的优先级来决定。如果两个值具有相同的 JSON 类型
，则使用特定于类型的规则进行第二级别比较。

下面的列表显示了 JSON 类型的优先级，从最高优先级到最低级。（类型名称是由
`JSON_TYPE()` 函数返回的名称）。在一行中显示的类型具有相同的优先级。在列表中先列
出的 JSON 类型的任何一个值都比其后面列出的 JSON 类型的任何值大。
```
BLOB
BIT
OPAQUE
DATETIME
TIME
DATE
BOOLEAN
ARRAY
OBJECT
STRING
INTEGER, DOUBLE
NULL
```

对于相同优先级的 JSON 值，比较规则是特定类型的：

* BLOB

比较了两个值的第一个 `N` 个字节，其中 `N` 是较短值中的字节数。如果两个值的第一个
`N` 个字节是完全相同的，那么较短的值就会在较长的值之前排序。

* BIT

规则和 BLOB 相同

* OPAQUE

和 BLOB 一样的规则。OPAQUE 值是不属于其他类型之一的值。

* DATETIME

一个代表更早时间点的值排序在表示更晚时间点的值之后。如果两个值最初分别来自 MySQL
的 DATETIME 和 TIMESTAMP 类型，如果它们代表相同的时间点，它们就是相等的。

* TIME

两个时间值中较小的值排序在较大的值之前。

* DATE

较早的日期排序在最近的日期之前。

* ARRAY

如果两个 JSON 数组的长度和数组中的对应位置的值是相等的，则两个数组是相等的。如果
数组不相等，它们的顺序是由第一个不同元素的位置决定的。这个位置较小的的值排在前面
。如果较短的数组的所有值都等于较长的数组中的对应值，则较短的数组排在前面。

例如：
```
[] < ["a"] < ["ab"] < ["ab", "cd", "ef"] < ["ab", "ef"]
```

* BOOLEAN

JSON false 字面量小于 JSON true 字面量

* OBJECT

如果两个 JSON 对象有相同的键集，并且每个键在两个对象中都有相同的值，则它们是相等
的。

例如：
```
{"a": 1, "b": 2} = {"b": 2, "a": 1}
```
The order of two objects that are not equal is unspecified but deterministic.

* STRING

被比较的两个字符串是按其字符集 `utf8mb4` 表示的第一个 `N` 字节的词汇表进行比较的
，`N` 是较短的字符串的长度。如果两个字符串的第一个 `N` 个字节是相同的，则短的字
符串被认为比长的字符串更小。

例如：
```
"a" < "ab" < "b" < "bc"
```

下面的排序和 SQL 字符串使用 `utf8mb4_bin` 校对规则的排序相等，因为 `utf8mb4_bin`
是一个二进制校对规则，JSON 值的比较是区分大小写的：
```
"A" < "a"
```

* INTEGER, DOUBLE

JSON 值可以包含精确值数字和近似值数字，这些数字的普通讨论，参考：Section 9.1.2,
“Numeric Literals”。关于原生 MySQL 数值类型的比较参考 Section 12.2, “Type
Conversion in Expression Evaluation”，但是比较 JSON 值内的数字的规则有一些不一
样：

* 两列数据类型分别是本地 MySQL INT 和 DOUBLE 的数值比较时，因为所有的比较都涉及
  一个整数和一个双精度已经总所周知，因此所有行的整数被转换为双精度。也就是说，精
  确值数字被转换成近似值数字。

* 另一方面，如果查询比较的 JSON 列包含两个数字，则不能预先知道数字是整数还是双精
  度数。为了在所有行中提供最一致的行为，MySQL 将近似值的数字转换为精确值数字。结
  果的顺序是一致的，并且没有丢失精确值数字的精度。例如，给定的标量
  9223372036854775805、9223372036854775806、9223372036854775807 和
  9.223372036854776e18，顺序如下：

    ```
    9223372036854775805 < 9223372036854775806 < 9223372036854775807
    < 9.223372036854776e18 = 9223372036854776000 < 9223372036854776001
    ```

如果 JSON 比较使用非 JSON 数值比较规则，则可能出现不一致的排序。通常的 MySQL 数
字比较规则会产生以下顺序：

* 整数比较

```
9223372036854775805 < 9223372036854775806 < 9223372036854775807
```
(not defined for 9.223372036854776e18)

* 双精度比较

```
9223372036854775805 = 9223372036854775806 = 9223372036854775807 = 9.223372036854776e18
```

任何 JSON 值和 SQL NULL 比较，结果都是 UNKNOWN.

比较 JSON 值和非 JSON 值时，非 JSON 值根据下表里的规则转换为 JSON 值，然后像上面
描述的一样进行比较。

## Converting between JSON and non-JSON values

下表提供了 MySQL 在 JSON 值和其他类型值之间转换时遵循的规则的总结：

    other type      CAST(other type AS JSON)            CAST(JSON AS other type)

    JSON            No change                           No change
    utf8 character
    type
    (utf8mb4, utf8, ascii)


`ORDER BY` 和 `GROUP BY` 用于 JSON 值时根据这些原则：

* 标量 JSON 值的排序与前面讨论的规则相同。

* 对于升序排序，SQL NULL 排在所有 JSON 值之前，包括 JSON null 字面量；对于降序排
  序，SQL NULL 排序在所有 JSON 值后面，包括 JSON null 字面量

* JSON 值的排序键受系统变量 `max_sort_length` 的值约束，所以只在
  `max_sort_length` 规定的字节以后才不同的键，比较时是相等的。

* 目前不支持对非标量值排序，会发生警告。

对于排序，将一个 JSON 标量转换成其他本地 MySQL 类型是有益的。例如，如果一个名为
jdoc 的列包含 JSON 对象，它的成员包含一个 id 键和一个非负值，那么根据 id 值排序
时可以使用这个表达式：
```
ORDER BY CAST(JSON_EXTRACT(jdoc, '$.id') AS UNSIGNED)
```

如果有一个生成的列，碰巧它被定义为使用与 `ORDER BY` 相同的表达式，那么 MySQL 优
化器就会识别这一点，并考虑为查询执行计划使用索引。Section 8.3.11, “Optimizer
Use of Generated Column Indexes”。

## Aggregation of JSON Values

For aggregation of JSON values, SQL NULL values are ignored as for other data types. Non-NULL values are converted to a numeric type and aggregated, except for `MIN()`, `MAX()`, and `GROUP_CONCAT()`.  The conversion to number should produce a meaningful result for JSON values that are numeric scalars, although (depending on the values) truncation and loss of precision may occur. Conversion to number of other JSON values may not produce a meaningful result.
对于 JSON 值的聚合：
* SQL NULL 值被忽略为其他数据类型；
* 非 NULL 值被转换成数值类型并聚合，除了 `MIN()`、`MAX()` 和 `GROUP_CONCAT()` 之外。到数值的转换应该为 JSON 值生成一个有意义的数值标量结果，尽管（根据值的不同）可能发生截断和精度损失。其他 JSON 值转换为数值可能不会产生有意义的结果。




