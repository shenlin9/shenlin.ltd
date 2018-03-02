---
title: PHP - CodeIgniter 数据库
categories:
  - PHP
  - CodeIgniter
tags:
  - CodeIgniter
---

CodeIgniter

<!--more-->

## 连接

连接数据库
```php
$this->load->database();
```

关闭数据库连接
```php
$this->db->close();
```

## 查询

简单查询
```php
if ($this->db->simple_query('YOUR QUERY'))
{
    echo "Success!";
}
else
{
    echo "Query failed!";
}
```
* simple_query 函数直接返回数据库驱动器的 "execute" 方法的返回值。
* 对于写类型的 查询（如：INSERT、DELETE、UPDATE），返回代表操作是否成功的 TRUE 或 FALSE；
* 对于读类型的成功查询，则返回代表结果集的对象。

常规查询
```php
$query = $this->db->query('YOUR QUERY HERE');
```

查询绑定
```php
$sql = "SELECT * FROM some_table WHERE id IN ? AND status = ? AND author = ?";
$this->db->query($sql, array(array(3, 6), 'live', 'Rick'));
```
将转换为
```sql
SELECT * FROM some_table WHERE id IN (3,6) AND status = 'live' AND author = 'Rick'
```

错误处理
```php
if ( ! $this->db->simple_query('SELECT `example_field` FROM `example_table`'))
{
    $error = $this->db->error(); // Has keys 'code' and 'message'
}
```
* error() 方法返回包含错误代码和错误消息的数组

## 结果

result() 返回对象数组，
```php
$query = $this->db->query("YOUR QUERY");

foreach ($query->result() as $row)
{
    echo $row->title;
    echo $row->name;
    echo $row->body;
}
```

result_array() 返回纯数组
```php
$query = $this->db->query("YOUR QUERY");

foreach ($query->result_array() as $row)
{
    echo $row['title'];
    echo $row['name'];
    echo $row['body'];
}
```

row() 返回单独一行，多行也只返回第一行，也可加参数指定第几行
row_array() 同 row()，只是返回的是数组
```php
$query = $this->db->query("YOUR QUERY");

$row = $query->row();

if (isset($row))
{
    echo $row->title;
    echo $row->name;
    echo $row->body;
}
```

上面所有的这些方法都会把所有的结果加载到内存里（预读取）， 
当处理大结果集时最好使用 unbuffered_row() 方法，不会预读取所有的结果数据到内存中。

unbuffered_row() 返回单独一行，如果查询结果不止一行，它将返回当前一行，并通过内部实现的指针来移动到下一行。
```php
$query = $this->db->query("YOUR QUERY");

while ($row = $query->unbuffered_row())
{
    echo $row->title;
    echo $row->name;
    echo $row->body;
}
```
还可以指定返回值的类型
```php
$query->unbuffered_row();           // object
$query->unbuffered_row('object');   // object
$query->unbuffered_row('array');    // associative array
```

free_result() 释放结果占用内存
```php
$query = $this->db->query('SELECT title FROM my_table');
foreach ($query->result() as $row)
{
    echo $row->title;
}

$query->free_result();  // The $query result object will no longer be available

$query2 = $this->db->query('SELECT name FROM some_table');
$row = $query2->row();
echo $row->name;

$query2->free_result(); // The $query2 result object will no longer be available
```

insert_id() 执行 INSERT 语句时，返回新插入行的ID
```php
$this->db->insert_id()
```

affected_rows() 执行 INSERT、UPDATE 等写类型的语句时，返回受影响的行数。
```php
$this->db->affected_rows()
```

## 事务

```php
$this->db->trans_start();
$this->db->query('AN SQL QUERY...');
$this->db->query('ANOTHER QUERY...');
$this->db->query('AND YET ANOTHER QUERY...');
$this->db->trans_complete();
if ($this->db->trans_status() === FALSE)
{
    // generate an error... or use the log_message() function to log your error
}
```
* 使用 trans_start(TRUE) 则表示测试模式，即使运行成功也会回滚
* 数据库配置文件 config/database.php 中启用了错误报告（db_debug = TRUE）， 
* 提交没有成功时会看到一条标准的错误信息
