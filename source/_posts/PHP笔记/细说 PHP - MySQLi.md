---
title: 细说 PHP - MySQLi
categories:
  - PHP
tags:
  - PHP
---

在PHP中使用MySQL扩展库操作数据库

	mysql_connect
	mysql_pconnect
	mysql_select_db
		一个页面有多个链接时，需要指定第二个参数，否则默认第二个参数为后建立的链接
	mysql_query
	mysql_insert_id	获取最后增长的ID
	mysql_affected_rows
	mysql_errno
	mysq_error
	mysql_close


使用PHP中mysql扩展处理结果集

	从结果集获取记录
		mysql_fetch_row		//返回索引数组，可以和list配合
			while(list($id,$name,$price)=mysql_fetch_row($result)){
				echo $id.$name.$price
			}
		mysql_fetch_assoc	//返回关联数组，下标为列名
		mysql_fetch_array	//返回索引和关联两个数组
		mysql_fetch_object	//将一条记录以对象的形式返回

		使用一次就从结果集中取出一条记录，取完后将指针移动到下一条记录，默认为第一条记录，到结尾则返回false

		mysql_data_seek
			移动结果集的指针

	获取行列的信息
		mysql_num_fields
		mysql_filed_name
		mysql_num_rows

	释放结果集
		mysql_free_result

	翻页类：
	$_SERVER["REQUEST_URI"]
	parse_url
	parse_str
	http_build_query


PHP的mysqli扩展库操作MySQL数据库

	从php5.0开始可以使用mysqli，i表示改进
	mysqli是一个面向对象的技术，但也支持过程化的使用方式，建议使用面向对象开发
	相对mysql改进：
		1、功能增加
		2、效率提高，新项目建议使用mysqli
		3、更稳定

	使用mysqli需要：
		为php安装mysqli扩展
		在php的配置文件中打开mysqli的扩展

	mysqli提供了三个类：
		1、mysqli
			和连接有关的类
		2、mysqli_result
			和结果集有关的类

			上述mysqli的两个类即完成了mysql的功能
		3、mysqli_stmt
			预处理类

    ```php
	$mysqli=@new mysqli("localhost","root","123","mydb"); //注意new前的@屏蔽了错误输出
	if(mysqli_connect_errno()){
		echo mysqli_connect_error();
		$mysqli=null;
		exit;
	}
	echo $mysqli->character_set_name()."<br>";
	echo $mysqli->get_client_info()."<br>";
	echo $mysqli->host_info."<br>";
	echo $mysqli->server_info."<br>";
	echo $mysqli->server_version."<br>";

	$result=$mysqli->query($sql);

	if(!$result){
		echo $mysqli->errno.$mysqli->error;
	}

	echo $mysqli->affected_rows;
	echo $mysqli->insert_id;
    ```

使用PHP中mysqli扩展处理结果集

	$result->num_rows;
	$result->field_count;

	$result->fetch_row;
	$result->fetch_assoc;
	$result->fetch_array;
	$result->fetch_object;

	$result->data_seek(5);

	$result->fetch_field();
	$result->fetch_fields();
	$result->field_seek();

	$result->free();
	$result->free_result();
	$mysqli->close();


使用PHP的mysqli扩展完成事务处理和一次执行多条SQL语句


使用PHP的mysqli扩展中预处理语句

   1、多条语句

	$mysqli->multi_query("sql1;sql2;sql3;");
	do{

		$result->$mysqli->store_result();

		if($mysqli->more_results()){
			echo "还有结果集";
		}
	}while($mysqli->next_result());

   2、事务

	目前只有InnoDB 和 BDB 表类型支持事务

	默认表都是自动提交的(autocommit)，因此使用事务需要先关闭自动提交，改为手动提交

	命令行中的操作：
		1、关闭自动提交
			set autocommit=0;
		2、开启事务
			start transaction;
		3、提交
			commit;
		4、回滚
			rollback;
	程序中的操作：
		1、关闭自动提交
			$mysqli->autocommit(0);
		2、开启事务
			start transaction;
		3、提交
			$mysqli->commit();
		4、回滚
			$mysqli->rollback();
		5、开启自动提交
			$mysqli->autocommit(1);

   3、mysqli中其他类成员的使用
	
	更改字符集
		$mysqli->query("set names gb2312");
		$mysqli->query("insert into t1(id,name) values(1,{$_GET("prname")})");

		$mysqli->set_charset("utf8");


使用PHP的mysqli扩展中预处理语句

	预处理类mysqli_stmt，和mysqli，mysqli_result相比优点：
		1、mysqli和mysqli_result完成的功能，都能使用mysqli_stmt完成
		2、效率高，编译一次，执行多次
		3、安全，使用问号占位符防止sql注入
	因此推荐使用

	第一种方法：
		$stmt=$mysqli->stmt_init();

		$sql="insert into t1(id,name,price,desn) values(?,?,?,?)";
			sql中的?即为问号占位符

		$stmt->prepare($sql);

	第二种方法：
		$stmt=$mysqli->prepare($sql);


	给占位符的每个?传值，也称为绑定参数
		$stmt->bind_param("idsb",$a,$b,$c,$d);
			i   corresponding variable has type integer
			d   corresponding variable has type double
			s   corresponding variable has type string
			b   corresponding variable is a blob and will be sent in packets

			$a,$b,$c,$d必须传变量，不能直接传值，因为这里的参数需要是引用


	执行，可以先绑定参数，后为参数赋值
		$a="1";
		$b="2";
		$c="3";
		$d="4";
		$stmt->execute();

		$a="1";
		$b="2";
		$c="3";
		$d="4";
		$stmt->execute();

		$a="1";
		$b="2";
		$c="3";
		$d="4";
		$stmt->execute();
	释放
		$stmt->close();


	处理结果集
		$stmt=$mysqli->prepare("select id,name,price from t1 where id > ?");

		$stmt->bind_param("i",$id);

		$stmt->bind_result($id,$name,$price);	//将每一列绑定到对应的变量

		$id=99;

		$stmt->execute();

		$stmt->store_result();	
			//一次性将结果取过来，没有这条语句则是逐条取，效率低，且下面的data_seek方法和num_rows都无法正确执行

		$result=$stmt->result_metadata();
			//此方法虽返回的是mysqli_resul结果集对象，但只能使用获取字段的方法

		while($field=$result->fetch_field){
			echo $field->name;
		}

		$stmt->data_seek(2);

		while($stmt->fetch()){		//循环取每一行

			echo $id.$name.$price;

		}

		echo $stmt->num_rows;

		$stmt->free_result();

		$stmt->close();


PHP中使用MySQL的视图

	什么是视图
		视图是存放数据的一个接口，是虚拟表
		数据来自一个或几个基表或视图，也可以是用户自己定义的数据
		视图并不存放数据，数据还是存放在基表中
		基表数据发送变化，视图数据也随之变化；视图数据变化，基表数据也变化

	视图的作用
		1.视图可以让查询变得很清楚，让复杂的sql语句变得简单
		2.保护数据库的重要数据，给不同的人看不同的数据，可从基表中选择特定的字段生成视图，然后为用户分配此视图的权限，但基表不分配权限，则限制了用户查看数据的权限。

	视图分类
		定制视图
		传参视图 

		merge
			会将引用视图的语句
		temptable
		undefined

		[with [cascaded|local] check option]

	创建视图
		create [or replace] [algorithm={merge|temptable|undefined}]
		view view_name [(column_list)]
		as select_statement
		[with [cascaded|local] check option]

		create view student_view as select * from student; 

		创建或替换视图（弱同名视图已存在）
		create or replace view student_view as select id,name,age from student;

		指定视图字段名，注意字段名中有空格时使用的是反引号
		create view student_view(`编 号`,名字,年龄) as select id,name,age from student;
			视图名不能和基表名相同
			只有一个frm的结构存储文件

	更改视图

		 alter view student_view as select * from student; 

	删除视图
	
		drop view student_view;
		
	查看视图

		show tables;
		desc student_view;
		show table status like 'student_view' \G;
		show create view student_view \G;
		select * from information_scheme \G;

	web开发中使用视图

		登录时的用户名、密码字段放一个视图中

