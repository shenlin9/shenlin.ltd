---
title: MySQL - Manual - SQL Syntax - 13.1.8 - ALTER TABLE 语法
categories: 
 - MySQL
 - Manual
tags: 
 - SQL Syntax
 - DDL
 - ALTER TABLE
---

MySQL `ALTER TABLE` 语法

<!--more-->

```
ALTER TABLE tbl_name
    [alter_specification [, alter_specification] ...]
    [partition_options]

alter_specification:
    table_options
        | ADD [COLUMN] col_name column_definition
            [FIRST | AFTER col_name]
        | ADD [COLUMN] (col_name column_definition,...)
        | ADD {INDEX|KEY} [index_name]
            [index_type] (key_part,...) [index_option] ...
        | ADD [CONSTRAINT [symbol]] PRIMARY KEY
            [index_type] (key_part,...) [index_option] ...
        | ADD [CONSTRAINT [symbol]]
            UNIQUE [INDEX|KEY] [index_name]
            [index_type] (key_part,...) [index_option] ...
        | ADD FULLTEXT [INDEX|KEY] [index_name]
            (key_part,...) [index_option] ...
        | ADD SPATIAL [INDEX|KEY] [index_name]
            (key_part,...) [index_option] ...
        | ADD [CONSTRAINT [symbol]]
    FOREIGN KEY [index_name] (col_name,...)
    reference_definition
        | ALGORITHM [=] {DEFAULT|INSTANT|INPLACE|COPY}
        | ALTER [COLUMN] col_name {SET DEFAULT literal | DROP DEFAULT}
        | ALTER INDEX index_name {VISIBLE | INVISIBLE}
        | CHANGE [COLUMN] old_col_name new_col_name column_definition
            [FIRST|AFTER col_name]
        | [DEFAULT] CHARACTER SET [=] charset_name [COLLATE [=] collation_name]
        | CONVERT TO CHARACTER SET charset_name [COLLATE collation_name]
        | {DISABLE|ENABLE} KEYS
        | {DISCARD|IMPORT} TABLESPACE
        | DROP [COLUMN] col_name
        | DROP {INDEX|KEY} index_name
        | DROP PRIMARY KEY
        | DROP FOREIGN KEY fk_symbol
        | FORCE
        | LOCK [=] {DEFAULT|NONE|SHARED|EXCLUSIVE}
        | MODIFY [COLUMN] col_name column_definition
            [FIRST | AFTER col_name]
        | ORDER BY col_name [, col_name] ...
        | RENAME COLUMN old_col_name TO new_col_name
        | RENAME {INDEX|KEY} old_index_name TO new_index_name
        | RENAME [TO|AS] new_tbl_name
        | {WITHOUT|WITH} VALIDATION
        | ADD PARTITION (partition_definition)
        | DROP PARTITION partition_names
        | DISCARD PARTITION {partition_names | ALL} TABLESPACE
        | IMPORT PARTITION {partition_names | ALL} TABLESPACE
        | TRUNCATE PARTITION {partition_names | ALL}
        | COALESCE PARTITION number
        | REORGANIZE PARTITION partition_names INTO (partition_definitions)
        | EXCHANGE PARTITION partition_name WITH TABLE tbl_name [{WITH|WITHOUT} VALIDATION]
        | ANALYZE PARTITION {partition_names | ALL}
        | CHECK PARTITION {partition_names | ALL}
        | OPTIMIZE PARTITION {partition_names | ALL}
        | REBUILD PARTITION {partition_names | ALL}
        | REPAIR PARTITION {partition_names | ALL}
        | REMOVE PARTITIONING
        | UPGRADE PARTITIONING

key_part:
    {col_name [(length)] | (expr)} [ASC | DESC]

index_type:
    USING {BTREE | HASH}

index_option:
    KEY_BLOCK_SIZE [=] value
    | index_type
    | WITH PARSER parser_name
    | COMMENT 'string'
    | {VISIBLE | INVISIBLE}

table_options:
    table_option [[,] table_option] ...

table_option:
    AUTO_INCREMENT [=] value
    | AVG_ROW_LENGTH [=] value
    | [DEFAULT] CHARACTER SET [=] charset_name
    | CHECKSUM [=] {0 | 1}
    | [DEFAULT] COLLATE [=] collation_name
    | COMMENT [=] 'string'
    | COMPRESSION [=] {'ZLIB'|'LZ4'|'NONE'}
    | CONNECTION [=] 'connect_string'
    | {DATA|INDEX} DIRECTORY [=] 'absolute path to directory'
    | DELAY_KEY_WRITE [=] {0 | 1}
    | ENCRYPTION [=] {'Y' | 'N'}
    | ENGINE [=] engine_name
    | INSERT_METHOD [=] { NO | FIRST | LAST }
    | KEY_BLOCK_SIZE [=] value
    | MAX_ROWS [=] value
    | MIN_ROWS [=] value
    | PACK_KEYS [=] {0 | 1 | DEFAULT}
    | PASSWORD [=] 'string'
    | ROW_FORMAT [=] {DEFAULT|DYNAMIC|FIXED|COMPRESSED|REDUNDANT|COMPACT}
    | STATS_AUTO_RECALC [=] {DEFAULT|0|1}
    | STATS_PERSISTENT [=] {DEFAULT|0|1}
    | STATS_SAMPLE_PAGES [=] value
    | TABLESPACE tablespace_name
    | UNION [=] (tbl_name[,tbl_name]...)

partition_options:
    (see CREATE TABLE options)
```

ALTER TABLE changes the structure of a table. For example, you can add or delete columns, create or
destroy indexes, change the type of existing columns, or rename columns or the table itself. You can also
change characteristics such as the storage engine used for the table or the table comment.

• To use ALTER TABLE, you need ALTER, CREATE, and INSERT privileges for the table. Renaming a
table requires ALTER and DROP on the old table, ALTER, CREATE, and INSERT on the new table.
• Following the table name, specify the alterations to be made. If none are given, ALTER TABLE does
nothing.
• The syntax for many of the permissible alterations is similar to clauses of the CREATE TABLE statement.
column_definition clauses use the same syntax for ADD and CHANGE as for CREATE TABLE. For
more information, see Section 13.1.18, “CREATE TABLE Syntax”.
• The word COLUMN is optional and can be omitted, except for RENAME COLUMN (to distinguish a column-
renaming operation from the RENAME table-renaming operation).
• Multiple ADD, ALTER, DROP, and CHANGE clauses are permitted in a single ALTER TABLE statement,
separated by commas. This is a MySQL extension to standard SQL, which permits only one of each
clause per ALTER TABLE statement. For example, to drop multiple columns in a single statement, do
this:
```
ALTER TABLE t2 DROP COLUMN c, DROP COLUMN d;
```
• If a storage engine does not support an attempted ALTER TABLE operation, a warning may result. Such
warnings can be displayed with SHOW WARNINGS. See Section 13.7.6.40, “SHOW WARNINGS Syntax”.
For information on troubleshooting ALTER TABLE, see Section B.5.6.1, “Problems with ALTER TABLE”.
• For information about generated columns, see Section 13.1.8.2, “ALTER TABLE and Generated
Columns”.
• For usage examples, see Section 13.1.8.3, “ALTER TABLE Examples”.
• With the mysql_info() C API function, you can find out how many rows were copied by ALTER
TABLE. See Section 27.7.7.36, “mysql_info()”.

There are several additional aspects to the ALTER TABLE statement, described under the following topics
in this section:

• Table Options
• Performance and Space Requirements
• Concurrency Control
• Adding and Dropping Columns
• Renaming, Redefining, and Reordering Columns
• Primary Keys and Indexes
• Foreign Keys
• Changing the Character Set
• Discarding and Importing InnoDB Tablespaces
• Row Order for MyISAM Tables
• Partitioning Options

### Table Options



### Performance and Space Requirements



### Concurrency Control



### Adding and Dropping Columns



### Renaming, Redefining, and Reordering Columns



### Primary Keys and Indexes



### Foreign Keys



### Changing the Character Set



### Discarding and Importing InnoDB Tablespaces



### Row Order for MyISAM Tables



### Partitioning Options




### 13.1.8.1 ALTER TABLE Partition Operations

Partitioning-related clauses for ALTER TABLE can be used with partitioned tables for repartitioning, to add, drop, discard, import, merge, and split partitions, and to perform partitioning maintenance.

* Simply using a `partition_options` clause with `ALTER TABLE` on a partitioned table repartitions the table according to the partitioning scheme defined by the `partition_options`. This clause always begins with `PARTITION BY`, and follows the same syntax and other rules as apply to the `partition_options` clause for `CREATE TABLE` (for more detailed information, see Section 13.1.18,“CREATE TABLE Syntax”), and can also be used to partition an existing table that is not already partitioned. For example, consider a (nonpartitioned) table defined as shown here:
  ```
  CREATE TABLE t1 (
      id INT,
      year_col INT
  );
  ```

  This table can be partitioned by HASH, using the id column as the partitioning key, into 8 partitions by means of this statement:
  ```
  ALTER TABLE t1
      PARTITION BY HASH(id)
      PARTITIONS 8;
  ```

  MySQL supports an ALGORITHM option with [SUB]PARTITION BY [LINEAR] KEY. ALGORITHM=1 causes the server to use the same key-hashing functions as MySQL 5.1 when computing the placement of rows in partitions; ALGORITHM=2 means that the server employs the key-hashing functions implemented and used by default for new KEY partitioned tables in MySQL 5.5 and later. (Partitioned tables created with the key-hashing functions employed in MySQL 5.5 and later cannot be used by a MySQL 5.1 server.) Not specifying the option has the same effect as using ALGORITHM=2. This option is intended for use chiefly when upgrading or downgrading [LINEAR] KEY partitioned tables between MySQL 5.1 and later MySQL versions, or for creating tables partitioned by KEY or LINEAR KEY on a MySQL 5.5 or later server which can be used on a MySQL 5.1 server.

  The table that results from using an ALTER TABLE ... PARTITION BY statement must follow the same rules as one created using CREATE TABLE ... PARTITION BY. This includes the rules governing the relationship between any unique keys (including any primary key) that the table might have, and the column or columns used in the partitioning expression, as discussed in Section 22.6.1,“Partitioning Keys, Primary Keys, and Unique Keys”. The CREATE TABLE ... PARTITION BY rules for specifying the number of partitions also apply to ALTER TABLE ... PARTITION BY.

  The partition_definition clause for `ALTER TABLE ADD PARTITION` supports the same options as the clause of the same name for the `CREATE TABLE` statement. (See Section 13.1.18, “CREATE TABLE Syntax”, for the syntax and description.) Suppose that you have the partitioned table created as shown here:
  ```
  CREATE TABLE t1 (
      id INT,
      year_col INT
  )
      PARTITION BY RANGE (year_col) (
      PARTITION p0 VALUES LESS THAN (1991),
      PARTITION p1 VALUES LESS THAN (1995),
      PARTITION p2 VALUES LESS THAN (1999)
  );
  ```

  You can add a new partition `p3` to this table for storing values less than 2002 as follows:

  `DROP PARTITION` can be used to drop one or more `RANGE` or `LIST` partitions. This statement cannot be used with `HASH` or `KEY` partitions; instead, use `COALESCE PARTITION` (see later in this section). Any data that was stored in the dropped partitions named in the `partition_names` list is discarded. For example, given the table `t1` defined previously, you can drop the partitions named `p0` and `p1` as shown here:
  ```
  ALTER TABLE t1 DROP PARTITION p0, p1;
  ```

  `ADD PARTITION` and `DROP PARTITION` do not currently support `IF [NOT] EXISTS`.

  The `DISCARD PARTITION ... TABLESPACE` and `IMPORT PARTITION ... TABLESPACE` options extend the Transportable Tablespace feature to individual InnoDB table partitions. Each InnoDB table partition has its own tablespace file (`.idb` file). The Transportable Tablespace feature makes it easy to copy the tablespaces from a running MySQL server instance to another running instance, or to perform a restore on the same instance. Both options take a comma-separated list of one or more partition names.

For example:
```
ALTER TABLE t1 DISCARD PARTITION p2, p3 TABLESPACE;
ALTER TABLE t1 IMPORT PARTITION p2, p3 TABLESPACE;
```
When running `DISCARD PARTITION ... TABLESPACE` and `IMPORT PARTITION ...  TABLESPACE` on subpartitioned tables, both partition and subpartition names are allowed. When a partition name is specified, subpartitions of that partition are included.

The Transportable Tablespace feature also supports copying or restoring partitioned InnoDB tables (all partitions at once). For additional information, see Section 15.7.6, “Copying File-Per-Table Tablespaces to Another Instance”, as well as, Section 15.7.6.1, “Transportable Tablespace Examples”.

Renames of partitioned tables are supported. You can rename individual partitions indirectly using `ALTER TABLE ... REORGANIZE PARTITION`; however, this operation copies the partition's data.

To delete rows from selected partitions, use the `TRUNCATE PARTITION` option. This option takes a list of one or more comma-separated partition names. Consider the table `t1` created by this statement:
```
CREATE TABLE t1 (
    id INT,
    year_col INT
)
PARTITION BY RANGE (year_col) (
    PARTITION p0 VALUES LESS THAN (1991),
    PARTITION p1 VALUES LESS THAN (1995),
    PARTITION p2 VALUES LESS THAN (1999),
    PARTITION p3 VALUES LESS THAN (2003),
    PARTITION p4 VALUES LESS THAN (2007)
);
```

To delete all rows from partition `p0`, use the following statement:
```
ALTER TABLE t1 TRUNCATE PARTITION p0;
```

The statement just shown has the same effect as the following `DELETE` statement:
```
DELETE FROM t1 WHERE year_col < 1991;
```

When truncating multiple partitions, the partitions do not have to be contiguous: This can greatly simplify
delete operations on partitioned tables that would otherwise require very complex `WHERE` conditions if
done with `DELETE` statements. For example, this statement deletes all rows from partitions `p1` and `p3`:
```
ALTER TABLE t1 TRUNCATE PARTITION p1, p3;
```

An equivalent `DELETE` statement is shown here:
```
DELETE FROM t1 WHERE
    (year_col >= 1991 AND year_col < 1995)
OR
    (year_col >= 2003 AND year_col < 2007);
```

If you use the `ALL` keyword in place of the list of partition names, the statement acts on all table
partitions.

`TRUNCATE PARTITION` merely deletes rows; it does not alter the definition of the table itself, or of any
of its partitions.

To verify that the rows were dropped, check the `INFORMATION_SCHEMA.PARTITIONS` table, using a
query such as this one:
```
SELECT PARTITION_NAME, TABLE_ROWS
FROM INFORMATION_SCHEMA.PARTITIONS
WHERE TABLE_NAME = 't1';
```

`COALESCE PARTITION` can be used with a table that is partitioned by `HASH` or `KEY` to reduce the
number of partitions by number. Suppose that you have created table `t2` as follows:
```
CREATE TABLE t2 (
    name VARCHAR (30),
    started DATE
)
PARTITION BY HASH( YEAR(started) )
PARTITIONS 6;
```

To reduce the number of partitions used by t2 from 6 to 4, use the following statement:
```
ALTER TABLE t2 COALESCE PARTITION 2;
```

The data contained in the last number partitions will be merged into the remaining partitions. In this case, partitions 4 and 5 will be merged into the first 4 partitions (the partitions numbered 0, 1, 2, and 3).

To change some but not all the partitions used by a partitioned table, you can use `REORGANIZE PARTITION`. This statement can be used in several ways:

* To merge a set of partitions into a single partition. This is done by naming several partitions in the `partition_names` list and supplying a single definition for `partition_definition`.

* To split an existing partition into several partitions. Accomplish this by naming a single partition for `partition_names` and providing multiple `partition_definitions`.

* To change the ranges for a subset of partitions defined using `VALUES LESS THAN` or the value lists for a subset of partitions defined using `VALUES IN`.

* To move a partition from one tablespace to another. For an example, see `15.7.10 InnoDB General Tablespaces - Moving Table Partitions Between Tablespaces Using ALTER TABLE`.

    Note
    For partitions that have not been explicitly named, MySQL automatically provides
    the default names p0, p1, p2, and so on. The same is true with regard to
    subpartitions.

For more detailed information about and examples of ALTER TABLE ... REORGANIZE PARTITION
statements, see Section 22.3.1, “Management of RANGE and LIST Partitions”.

• To exchange a table partition or subpartition with a table, use the ALTER TABLE ... EXCHANGE
  PARTITION statement—that is, to move any existing rows in the partition or subpartition to the
  nonpartitioned table, and any existing rows in the nonpartitioned table to the table partition or
  subpartition.
  For usage information and examples, see Section 22.3.3, “Exchanging Partitions and Subpartitions with
  Tables”.
• Several options provide partition maintenance and repair functionality analogous to that implemented
  for nonpartitioned tables by statements such as CHECK TABLE and REPAIR TABLE (which are
  also supported for partitioned tables; for more information, see Section 13.7.3, “Table Maintenance
  Statements”). These include ANALYZE PARTITION, CHECK PARTITION, OPTIMIZE PARTITION,
  REBUILD PARTITION, and REPAIR PARTITION. Each of these options takes a partition_names
  clause consisting of one or more names of partitions, separated by commas. The partitions must already
  exist in the target table. You can also use the ALL keyword in place of partition_names, in which
  case the statement acts on all table partitions. For more information and examples, see Section 22.3.4,
  “Maintenance of Partitions”.
  InnoDB does not currently support per-partition optimization; ALTER TABLE ... OPTIMIZE
  PARTITION causes the entire table to rebuilt and analyzed, and an appropriate warning to be issued.
  (Bug #11751825, Bug #42822) To work around this problem, use ALTER TABLE ... REBUILD
  PARTITION and ALTER TABLE ... ANALYZE PARTITION instead.
  The ANALYZE PARTITION, CHECK PARTITION, OPTIMIZE PARTITION, and REPAIR PARTITION
  options are not supported for tables which are not partitioned.
• REMOVE PARTITIONING enables you to remove a table's partitioning without otherwise affecting the
  table or its data. This option can be combined with other ALTER TABLE options such as those used to
  add, drop, or rename columns or indexes.
• Using the ENGINE option with ALTER TABLE changes the storage engine used by the table without
  affecting the partitioning. The target storage engine must provide its own partitioning handler. Only the
  InnoDB and NDB storage engines have native partitioning handlers; NDB is not currently supported in
  MySQL 8.0.

It is possible for an ALTER TABLE statement to contain a PARTITION BY or REMOVE PARTITIONING
clause in an addition to other alter specifications, but the PARTITION BY or REMOVE PARTITIONING
clause must be specified last after any other specifications.

The ADD PARTITION, DROP PARTITION, COALESCE PARTITION, REORGANIZE PARTITION,
ANALYZE PARTITION, CHECK PARTITION, and REPAIR PARTITION options cannot be combined with
other alter specifications in a single ALTER TABLE, since the options just listed act on individual partitions.
For more information, see Section 13.1.8.1, “ALTER TABLE Partition Operations”.

Only a single instance of any one of the following options can be used in a given ALTER TABLE
statement: PARTITION BY, ADD PARTITION, DROP PARTITION, TRUNCATE PARTITION, EXCHANGE
PARTITION, REORGANIZE PARTITION, or COALESCE PARTITION, ANALYZE PARTITION, CHECK
PARTITION, OPTIMIZE PARTITION, REBUILD PARTITION, REMOVE PARTITIONING.

For example, the following two statements are invalid:
```
ALTER TABLE t1 ANALYZE PARTITION p1, ANALYZE PARTITION p2;
ALTER TABLE t1 ANALYZE PARTITION p1, CHECK PARTITION p2;
```
In the first case, you can analyze partitions p1 and p2 of table t1 concurrently using a single statement
with a single ANALYZE PARTITION option that lists both of the partitions to be analyzed, like this:
```
ALTER TABLE t1 ANALYZE PARTITION p1, p2;
```
In the second case, it is not possible to perform ANALYZE and CHECK operations on different partitions of
the same table concurrently. Instead, you must issue two separate statements, like this:
```
ALTER TABLE t1 ANALYZE PARTITION p1;
ALTER TABLE t1 CHECK PARTITION p2;
```
REBUILD operations are currently unsupported for subpartitions. The REBUILD keyword is expressly
disallowed with subpartitions, and causes ALTER TABLE to fail with an error if so used.
CHECK PARTITION and REPAIR PARTITION operations fail when the partition to be checked or
repaired contains any duplicate key errors.

For more information about these statements, see Section 22.3.4, “Maintenance of Partitions”.

### 13.1.8.2 ALTER TABLE and Generated Columns



### 13.1.8.3 ALTER TABLE Examples



