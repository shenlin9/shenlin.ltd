---
title: MySQL - Manual - SQL Syntax - 13.1.14 - CREATE INDEX 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - CREATE INDEX
---

MySQL `CREATE INDEX` 语法

<!--more-->

```
CREATE [UNIQUE | FULLTEXT | SPATIAL] INDEX index_name
    [index_type]
    ON tbl_name (key_part,...)
    [index_option]
    [algorithm_option | lock_option] ...

key_part: {col_name [(length)] | (expr)} [ASC | DESC]

index_option:
    KEY_BLOCK_SIZE [=] value
    | index_type
    | WITH PARSER parser_name
    | COMMENT 'string'
    | {VISIBLE | INVISIBLE}

index_type:
    USING {BTREE | HASH}

algorithm_option:
    ALGORITHM [=] {DEFAULT | INPLACE | COPY}

lock_option:
    LOCK [=] {DEFAULT | NONE | SHARED | EXCLUSIVE}
```

Normally, you create all indexes on a table at the time the table itself is created with CREATE TABLE. See
Section 13.1.18, “CREATE TABLE Syntax”. This guideline is especially important for InnoDB tables, where
the primary key determines the physical layout of rows in the data file. CREATE INDEX enables you to add
indexes to existing tables.

CREATE INDEX is mapped to an ALTER TABLE statement to create indexes. See Section 13.1.8, “ALTER
TABLE Syntax”. CREATE INDEX cannot be used to create a PRIMARY KEY; use ALTER TABLE instead.
For more information about indexes, see Section 8.3.1, “How MySQL Uses Indexes”.

InnoDB supports secondary indexes on virtual columns. For more information, see Section 13.1.18.9,
“Secondary Indexes and Generated Columns”.

When the innodb_stats_persistent setting is enabled, run the ANALYZE TABLE statement for an
InnoDB table after creating an index on that table.

An index specification of the form (key_part1, key_part2, ...) creates an index with multiple
key parts. Index key values are formed by concatenating the values of the given key parts. For example
(col1, col2, col3) specifies a multiple-column index with index keys consisting of values from col1,
col2, and col3.

A key_part specification can end with ASC or DESC to specify whether index values are stored in
ascending or descending order. The default is ascending if no order specifier is given. ASC and DESC
are not permitted for HASH indexes. As of MySQL 8.0.12, ASC and DESC are not permitted for SPATIAL
indexes.

The following sections describe different aspects of the CREATE INDEX statement:
* Column Prefix Key Parts
* Functional Key Parts
* Unique Indexes
* Full-Text Indexes
* Spatial Indexes
* Index Options
* Table Copying and Locking Options

### Column Prefix Key Parts

For string columns, indexes can be created that use only the leading part of column values, using
col_name(length) syntax to specify an index prefix length:
• Prefixes can be specified for CHAR, VARCHAR, BINARY, and VARBINARY key parts.
• Prefixes must be specified for BLOB and TEXT key parts. Additionally, BLOB and TEXT columns can be
indexed only for InnoDB, MyISAM, and BLACKHOLE tables.
• Prefix limits are measured in bytes. However, prefix lengths for index specifications in CREATE TABLE,
ALTER TABLE, and CREATE INDEX statements are interpreted as number of characters for nonbinary
string types (CHAR, VARCHAR, TEXT) and number of bytes for binary string types (BINARY, VARBINARY,
BLOB). Take this into account when specifying a prefix length for a nonbinary string column that uses a
multibyte character set.

Prefix support and lengths of prefixes (where supported) are storage engine dependent. For example, a
prefix can be up to 767 bytes long for InnoDB tables that use the REDUNDANT or COMPACT row format.
The prefix length limit is 3072 bytes for InnoDB tables that use the DYNAMIC or COMPRESSED row
format. For MyISAM tables, the prefix length limit is 1000 bytes.

If a specified index prefix exceeds the maximum column data type size, CREATE INDEX handles the index
as follows:
• For a nonunique index, either an error occurs (if strict SQL mode is enabled), or the index length is
reduced to lie within the maximum column data type size and a warning is produced (if strict SQL mode
is not enabled).
• For a unique index, an error occurs regardless of SQL mode because reducing the index length might
enable insertion of nonunique entries that do not meet the specified uniqueness requirement.

The statement shown here creates an index using the first 10 characters of the name column (assuming
that name has a nonbinary string type):
```
CREATE INDEX part_of_name ON customer (name(10));
```
If names in the column usually differ in the first 10 characters, lookups performed using this index should
not be much slower than using an index created from the entire name column. Also, using column prefixes
for indexes can make the index file much smaller, which could save a lot of disk space and might also
speed up INSERT operations.

### Functional Key Parts

A “normal” index indexes column values or prefixes of column values. For example, in the following table,
the index entry for a given t1 row includes the full col1 value and a prefix of the col2 value consisting of
its first 10 bytes:
```
CREATE TABLE t1 (
col1 VARCHAR(10),
col2 VARCHAR(20),
INDEX (col1, col2(10))
);
```

MySQL 8.0.13 and higher supports functional key parts that index expression values rather than column or
column prefix values. Use of functional key parts enables indexing of values not stored directly in the table.
Examples:
```
CREATE TABLE t1 (col1 INT, col2 INT, INDEX func_index ((ABS(col1))));
CREATE INDEX idx1 ON t1 ((col1 + col2));
CREATE INDEX idx2 ON t1 ((col1 + col2), (col1 - col2), col1);
ALTER TABLE t1 ADD INDEX ((col1 * 40) DESC);
```
An index with multiple key parts can mix nonfunctional and functional key parts.

ASC and DESC are supported for functional key parts.

Functional key parts must adhere to the following rules. An error occurs if a key part definition contains
disallowed constructs.

* In index definitions, enclose expressions within parentheses to distinguish them from columns or column
prefixes. For example, this is permitted; the expressions are enclosed within parentheses:

* In index definitions, enclose expressions within parentheses to distinguish them from columns or column
prefixes. For example, this is permitted; the expressions are enclosed within parentheses:
```
INDEX ((col1 + col2), (col3 - col4))
```
This produces an error; the expressions are not enclosed within parentheses:
```
INDEX (col1 + col2, col3 - col4)
```
* A functional key part cannot consist solely of a column name. For example, this is not permitted:
```
INDEX ((col1), (col2))
```
Instead, write the key parts as nonfunctional key parts, without parentheses:
```
INDEX (col1, col2)
```
* A functional key part expression cannot refer to column prefixes. For a workaround, see the discussion
of SUBSTRING() and CAST() later in this section.
* Functional key parts are not permitted in foreign key specifications.

For CREATE TABLE ... LIKE, the destination table preserves functional key parts from the original
table.

Functional indexes are implemented as hidden virtual generated columns, which has these implications:
• Each functional key part counts against the limit on total number of table columns; see Section C.10.4,
“Limits on Table Column Count and Row Size”.
• Functional key parts inherit all restrictions that apply to generated columns. Examples:
• Only functions permitted for generated columns are permitted for functional key parts.
• Subqueries, parameters, variables, stored functions, and user-defined functions are not permitted.

For more information about applicable restrictions, see Section 13.1.18.8, “CREATE TABLE and
Generated Columns”, and Section 13.1.8.2, “ALTER TABLE and Generated Columns”.

UNIQUE is supported for indexes that include functional key parts. However, primary keys cannot include
functional key parts. A primary key requires the generated column to be stored, but functional key parts are
implemented as virtual generated columns, not stored generated columns.

SPATIAL and FULLTEXT indexes cannot have functional key parts.
If a table contains no primary key, InnoDB automatically promotes the first UNIQUE NOT NULL index to
the primary key. This is not supported for UNIQUE NOT NULL indexes that have functional key parts.
Nonfunctional indexes raise a warning if there are duplicate indexes. Indexes that contain functional key
parts do not have this feature.

To remove a column that is referenced by a functional key part, the index must be removed first.
Otherwise, an error occurs.

Although nonfunctional key parts support a prefix length specification, this is not possible for functional key
parts. The solution is to use SUBSTRING() (or CAST(), as described later in this section). For a functional
key part containing the SUBSTRING() function to be used in a query, the WHERE clause must contain
SUBSTRING() with the same arguments. In the following example, only the second SELECT is able to
use the index because that is the only query in which the arguments to SUBSTRING() match the index
specification:
```
CREATE TABLE tbl (
col1 LONGTEXT,
INDEX idx1 ((SUBSTRING(col1, 1, 10)))
);
SELECT * FROM tbl WHERE SUBSTRING(col1, 1, 9) = '123456789';
SELECT * FROM tbl WHERE SUBSTRING(col1, 1, 10) = '1234567890';
```

Functional key parts enable indexing of values that cannot be indexed otherwise, such as JSON values.
However, this must be done correctly to achieve the desired effect. For example, this syntax does not
work:
```
CREATE TABLE employees (
    data JSON,
    INDEX ((data->>'$.name'))
);
```

The syntax fails because:
• The ->> operator translates into JSON_UNQUOTE(JSON_EXTRACT(...)).
• JSON_UNQUOTE() returns a value with a data type of LONGTEXT, and the hidden generated column thus
is assigned the same data type.
• MySQL cannot index LONGTEXT columns specified without a prefix length on the key part, and prefix
lengths are not permitted in functional key parts.

To index the JSON column, you could try using the CAST() function as follows:
```
CREATE TABLE employees (
data JSON,
INDEX ((CAST(data->>'$.name' AS CHAR(30))))
);
```

The hidden generated column is assigned the VARCHAR(30) data type, which can be indexed. But this
approach produces a new issue when trying to use the index:
• CAST() returns a string with the collation utf8mb4_0900_ai_ci (the server default collation).
• JSON_UNQUOTE() returns a string with the collation utf8mb4_bin (hard coded).
As a result, there is a collation mismatch between the indexed expression in the preceding table definition
and the WHERE clause expression in the following query:
```
SELECT * FROM employees WHERE data->>'$.name' = 'James';
```

The index is not used because the expressions in the query and the index differ. To support this kind of
scenario for functional key parts, the optimizer automatically strips CAST() when looking for an index
to use, but only if the collation of the indexed expression matches that of the query expression. For an
index with a functional key part to be used, either of the following two solutions work (although they differ
somewhat in effect):
• Solution 1. Assign the indexed expression the same collation as JSON_UNQUOTE():
```
CREATE TABLE employees (
data JSON,
INDEX idx ((CAST(data->>"$.name" AS CHAR(30)) COLLATE utf8mb4_bin))
);
INSERT INTO employees VALUES
('{ "name": "james", "salary": 9000 }'),
('{ "name": "James", "salary": 10000 }'),
('{ "name": "Mary", "salary": 12000 }'),
('{ "name": "Peter", "salary": 8000 }');
SELECT * FROM employees WHERE data->>'$.name' = 'James';
```

The `->>` operator is the same as `JSON_UNQUOTE(JSON_EXTRACT(...))`, and `JSON_UNQUOTE()`
returns a string with collation `utf8mb4_bin`. The comparison is thus case sensitive, and only one row
matches:
```
+------------------------------------+
| data |
+------------------------------------+
| {"name": "James", "salary": 10000} |
+------------------------------------+
```

* Solution 2. Specify the full expression in the query:
```
CREATE TABLE employees (
    data JSON,
    INDEX idx ((CAST(data->>"$.name" AS CHAR(30))))
);

INSERT INTO employees VALUES
('{ "name": "james", "salary": 9000 }'),
('{ "name": "James", "salary": 10000 }'),
('{ "name": "Mary", "salary": 12000 }'),
('{ "name": "Peter", "salary": 8000 }');

SELECT * FROM employees WHERE CAST(data->>'$.name' AS CHAR(30)) = 'James';
```

CAST() returns a string with collation utf8mb4_0900_ai_ci, so the comparison case insensitive and
two rows match:
```
+------------------------------------+
| data |
+------------------------------------+
| {"name": "james", "salary": 9000} |
| {"name": "James", "salary": 10000} |
+------------------------------------+
```

Be aware that although the the optimizer supports automatically stripping CAST() with indexed generated
columns, the following approach does not work because it produces a different result with and without an
index (Bug#27337092):
```
mysql> CREATE TABLE employees (
    data JSON,
    generated_col VARCHAR(30) AS (CAST(data->>'$.name' AS CHAR(30)))
);
Query OK, 0 rows affected, 1 warning (0.03 sec)

mysql> INSERT INTO employees (data)
VALUES ('{"name": "james"}'), ('{"name": "James"}');
Query OK, 2 rows affected, 1 warning (0.01 sec)
Records: 2 Duplicates: 0 Warnings: 1

mysql> SELECT * FROM employees WHERE data->>'$.name' = 'James';
+-------------------+---------------+
| data | generated_col |
+-------------------+---------------+
| {"name": "James"} | James |
+-------------------+---------------+
1 row in set (0.00 sec)

mysql> ALTER TABLE employees ADD INDEX idx (generated_col);
Query OK, 0 rows affected, 1 warning (0.03 sec)
Records: 0 Duplicates: 0 Warnings: 1

mysql> SELECT * FROM employees WHERE data->>'$.name' = 'James';
+-------------------+---------------+
| data | generated_col |
+-------------------+---------------+
| {"name": "james"} | james |
| {"name": "James"} | James |
+-------------------+---------------+
2 rows in set (0.01 sec)
```

### Unique Indexes

A UNIQUE index creates a constraint such that all values in the index must be distinct. An error occurs if
you try to add a new row with a key value that matches an existing row. If you specify a prefix value for a
column in a UNIQUE index, the column values must be unique within the prefix length. A UNIQUE index
permits multiple NULL values for columns that can contain NULL.

If a table has a PRIMARY KEY or UNIQUE NOT NULL index that consists of a single column that has an
integer type, you can use `_rowid` to refer to the indexed column in SELECT statements, as follows:
* `_rowid` refers to the PRIMARY KEY column if there is a PRIMARY KEY consisting of a single integer
column. If there is a PRIMARY KEY but it does not consist of a single integer column, `_rowid` cannot be
used.
* Otherwise, `_rowid` refers to the column in the first UNIQUE NOT NULL index if that index consists of a
single integer column. If the first UNIQUE NOT NULL index does not consist of a single integer column,
`_rowid` cannot be used.

### Full-Text Indexes

FULLTEXT indexes are supported only for InnoDB and MyISAM tables and can include only CHAR,
VARCHAR, and TEXT columns. Indexing always happens over the entire column; column prefix indexing is
not supported and any prefix length is ignored if specified. See Section 12.9, “Full-Text Search Functions”,
for details of operation.

### Spatial Indexes

The MyISAM, InnoDB, NDB, and ARCHIVE storage engines support spatial columns such as POINT and
GEOMETRY. (Section 11.5, “Spatial Data Types”, describes the spatial data types.) However, support for
spatial column indexing varies among engines. Spatial and nonspatial indexes on spatial columns are
available according to the following rules.

Spatial indexes on spatial columns have these characteristics:
• Available only for InnoDB and MyISAM tables. Specifying SPATIAL INDEX for other storage engines
results in an error.
• As of MySQL 8.0.12, an index on a spatial column must be a SPATIAL index. The SPATIAL keyword is
thus optional but implicit for creating an index on a spatial column.
• Available for single spatial columns only. A spatial index cannot be created over multiple spatial
columns.
• Indexed columns must be NOT NULL.
• Column prefix lengths are prohibited. The full width of each column is indexed.
• Not permitted for a primary key or unique index.

Nonspatial indexes on spatial columns (created with INDEX, UNIQUE, or PRIMARY KEY) have these
characteristics:
• Permitted for any storage engine that supports spatial columns except ARCHIVE.
• Columns can be NULL unless the index is a primary key.
• The index type for a non-SPATIAL index depends on the storage engine. Currently, B-tree is used.
• Permitted for a column that can have NULL values only for InnoDB, MyISAM, and MEMORY tables.

### Index Options

Following the key part list, index options can be given. An index_option value can be any of the
following:

* KEY_BLOCK_SIZE [=] value

  For MyISAM tables, KEY_BLOCK_SIZE optionally specifies the size in bytes to use for index key blocks.
  The value is treated as a hint; a different size could be used if necessary. A KEY_BLOCK_SIZE value
  specified for an individual index definition overrides a table-level KEY_BLOCK_SIZE value.
  
  KEY_BLOCK_SIZE is not supported at the index level for InnoDB tables. See Section 13.1.18, “CREATE
  TABLE Syntax”.

* index_type

  Some storage engines permit you to specify an index type when creating an index. For example:

  ```
  CREATE TABLE lookup (id INT) ENGINE = MEMORY;
  CREATE INDEX id_index ON lookup (id) USING BTREE;
  ```

  Table 13.1, “Index Types Per Storage Engine” shows the permissible index type values supported by
  different storage engines. Where multiple index types are listed, the first one is the default when no index
  type specifier is given. Storage engines not listed in the table do not support an index_type clause in
  index definitions.

    Table 13.1 Index Types Per Storage Engine
    -------------------------------------------
    Storage Engine          Permissible Index Types
    InnoDB                  BTREE
    MyISAM                  BTREE
    MEMORY/HEAP             HASH, BTREE

The index_type clause cannot be used for FULLTEXT INDEX or (prior to MySQL 8.0.12) SPATIAL
INDEX specifications. Full-text index implementation is storage engine dependent. Spatial indexes are
implemented as R-tree indexes.

If you specify an index type that is not valid for a given storage engine, but another index type is
available that the engine can use without affecting query results, the engine uses the available type.
The parser recognizes RTREE as a type name. As of MySQL 8.0.12, this is permitted only for SPATIAL
indexes. Prior to 8.0.12, RTREE cannot be specified for any storage engine.

    Note
    Use of the index_type option before the ON tbl_name clause is deprecated;
    support for use of the option in this position will be removed in a future MySQL
    release. If an index_type option is given in both the earlier and later positions,
    the final option applies.

TYPE type_name is recognized as a synonym for USING type_name. However, USING is the
preferred form.

The following tables show index characteristics for the storage engines that support the index_type
option.

    Table 13.2 InnoDB Storage Engine Index Characteristics
    Index Class Index
    Type
    Stores NULL
    VALUES
    Permits Multiple
    NULL Values
    IS NULL Scan
    Type
    IS NOT NULL
    Scan Type
    Primary key BTREE No No N/A N/A
    Unique BTREE Yes Yes Index Index
    Key BTREE Yes Yes Index Index
    FULLTEXT N/A Yes Yes Table Table
    SPATIAL N/A No No N/A N/A

    Table 13.3 MyISAM Storage Engine Index Characteristics
    Index Class Index
    Type
    Stores NULL
    VALUES
    Permits Multiple
    NULL Values
    IS NULL Scan
    Type
    IS NOT NULL
    Scan Type
    Primary key BTREE No No N/A N/A
    Unique BTREE Yes Yes Index Index
    Key BTREE Yes Yes Index Index
    FULLTEXT N/A Yes Yes Table Table
    SPATIAL N/A No No N/A N/A

    Table 13.4 MEMORY Storage Engine Index Characteristics
    Index Class Index
    Type
    Stores NULL
    VALUES
    Permits Multiple
    NULL Values
    IS NULL Scan
    Type
    IS NOT NULL
    Scan Type
    Primary key BTREE No No N/A N/A
    Unique BTREE Yes Yes Index Index
    Key BTREE Yes Yes Index Index
    Primary key HASH No No N/A N/A
    Unique HASH Yes Yes Index Index
    Key HASH Yes Yes Index Index

• WITH PARSER parser_name

This option can be used only with FULLTEXT indexes. It associates a parser plugin with the index if full-
text indexing and searching operations need special handling. InnoDB and MyISAM support full-text
parser plugins. See Full-Text Parser Plugins and Section 28.2.4.4, “Writing Full-Text Parser Plugins” for
more information.

• COMMENT 'string'

  Index definitions can include an optional comment of up to 1024 characters.
  
  The MERGE_THRESHOLD for index pages can be configured for individual indexes using the
  index_option COMMENT clause of the CREATE INDEX statement. For example:
  ```
  CREATE TABLE t1 (id INT);
  CREATE INDEX id_index ON t1 (id) COMMENT 'MERGE_THRESHOLD=40';
  ```
  
  If the page-full percentage for an index page falls below the MERGE_THRESHOLD value when a row is
  deleted or when a row is shortened by an update operation, InnoDB attempts to merge the index page
  with a neighboring index page. The default MERGE_THRESHOLD value is 50, which is the previously
  hardcoded value.
  
  MERGE_THRESHOLD can also be defined at the index level and table level using CREATE TABLE
  and ALTER TABLE statements. For more information, see Section 15.6.12, “Configuring the Merge
  Threshold for Index Pages”.

• VISIBLE, INVISIBLE

  Specify index visibility. Indexes are visible by default. An invisible index is not used by the optimizer.
  Specification of index visibility applies to indexes other than primary keys (either explicit or implicit). For
  more information, see Section 8.3.12, “Invisible Indexes”.

### Table Copying and Locking Options

ALGORITHM and LOCK clauses may be given to influence the table copying method and level of
concurrency for reading and writing the table while its indexes are being modified. They have the same
meaning as for the ALTER TABLE statement. For more information, see Section 13.1.8, “ALTER TABLE
Syntax”

