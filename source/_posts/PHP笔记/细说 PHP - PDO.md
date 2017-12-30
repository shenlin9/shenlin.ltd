---
title: 细说 PHP - PDO
categories:
  - PHP
tags:
  - PHP
---

PDO的介绍安装及对象初使化

	PDO:PHP Data Object

	php处理不同的数据库需要调用不同的数据库扩展，如mysql,mysqli,orace,mssql等，这些扩展的调用方法虽然类似但不尽相同，在需要将数据从一个类型数据库移植到另一个类型数据库时，首先要使用工具将数据移动过去，然后最重要的是要修改程序中的数据调用方法，这是最烦琐最大量的工作，也因此，跨数据库的pdo应运而生，类似的技术还有adodb和mdb2，但pdo相对来说更高效。

	pdo是基于驱动的(adodb不是)，并不是pdo扩展本身去访问不同的数据库，而是安装对应数据库的pdo驱动，如pdo_mysql对应mysql的驱动

	启用pdo：
		安装pdo扩展
		安装对应数据库的驱动
		php配置文件中打开对应的驱动

	pdo的三个类
		PDO
			和数据库连接有关的类
		PDOStatement
			处理准备语句和结果集
		PDOException
			处理异常

	PDO很多常量

	创建PDO对象
		$pdo=new PDO($dsn,$username,$pwd);
			dsn data source name 

			数据源，包括主机位置、库名、连接哪种数据库驱动三种信息
			各数据库dsn的写法不同，可参考帮助
			可将dsn写入文件中或写入配置文件中，用户切换数据库时方便：
				pdo.dsn.mysqlpdo=mysql:host=localhost;dbname=demo

		try{

			$pdo=new PDO("mysql:host=localhost;dbname=demo","root","123456");

		}catch(PDOException $e){

			echo $e->getMessage();
			exit;
		}

		echo $pdo->getAttribute(PDO::ATTR_CLIENT_VERSION)."<br>";

		$pdo->setAttribute(PDO::ATTR_AUTOCOMMIT,0);

		也可用第四个参数设置PDO的常量：

			$pdo=new PDO($dsn,$username,$pwd,array(PDO::ATTR_AUTOCOMMIT->0));


PDO对象中方法的使用详细介绍

	exec()
		执行有影响行数的sql
		返回影响的行数

		获取最后自增长ID：$pdo->lastInsertId();
	query()
		执行有结果集的sql
		返回的是预声明对象???，支持foreach遍历
	prepare()


	三种错误模式：？？？
		PDO::ERRMODE_SILENT
			默认模式下sql有错误不提示，需要使用错误函数获取错误信息
			errorcode
			errorinfo
		PDO::ERRMODE_WARNING
			警告模式下trycatch不会捕获到异常
		PDO::EXCEPTION
			异常模式下可以抓取到异常

	事务：
		$pdo->setAttribute(PDO::ATTR_AUTOCOMMIT,0);
		$pdo->beginTransaction();
		$pdo->commit();
		$pdo->rollback();
		$pdo->setAttribute(PDO::ATTR_AUTOCOMMIT,1);

使用PDO进行SQL语句预处理

	//准备好了一条sql语句，并放到服务器端编译过了
	$stmt=$pdo->prepare("insert into t1(id,name,age,sex) values(?,?,?,?)");	
	$stmt=$pdo->prepare("insert into t1(id,name,age,sex) values(:id,:name,:age,:sex)");	
		返回的就是预处理类PDOStatement对象
		和mysqli的占位符只支持?占位符不同，这里也支持名字占位符
		?占位符是按索引顺序使用，名字占位符则是按名称使用，和顺序无关

	//绑定名字参数
	$pdo->bindParam(":id",$id,PDO::PARAM_INT);	//后面的类型是可选的，系统会自动转换
	$pdo->bindParam(":name",$name);
	$pdo->bindParam(":age",$age);
	$pdo->bindParam(":sex",$sex);

	//绑定问号参数
	$pdo->bindParam(1,$id);
	$pdo->bindParam(2,$name);
	$pdo->bindParam(3,$age);
	$pdo->bindParam(4,$sex);

	//为参数连续赋值和执行
	$id=1;
	$name="lisi";
	$age=10;
	$sex="boy";

	if($stmt->execute()){
		//获取最后插入ID
		echo $pdo->lastInsertId();
	}

	$id=2;
	$name="wangwu";
	$age=12;
	$sex="girl";

	if($stmt->execute()){
		//获取最后插入ID
		echo $pdo->lastInsertId();
	}

	//使用另一种方式执行，通过传递数组
	$stmt=$pdo->prepare("insert into t1(id,name,age,sex) values(:id,:name,:age,:sex)");	
	$stmt->execute(array(":id"=>1,":name"=>"lisi",":age"=>10,":sex"=>"boy"));//和数组元素顺序无关
	$stmt->execute(array(":id"=>2,":name"=>"wangwu",":age"=>12,":sex"=>"girl"));

	$stmt=$pdo->prepare("insert into t1(id,name,age,sex) values(?,?,?,?)");	
	$stmt->execute(array(1,"lisi",10,"boy"));//按占位符顺序传值
	$stmt->execute(array(2,"wuli",12,"girl"));

使用PDO操作结果集

	$stmt=$pdo->prepare("select * from t1 where id > :id");
	$stmt->execute(array(":id"=>12));

	$stmt->setFetchMode(PDO::FETCH_NUM);

	//逐条获取
	while ($row = $stmt->fetch(PDO::FETCH_NUM)) {
		foreach($row as $item){
			echo $item;
		}
	}

	//一次性全部获取
	$data=$stmt->fetchAll(PDO::FETCH_NUM);
	echo print_r($data);

	//通过绑定列的方式获取
	$stmt->bindColumn("id",$id,PDO::PARAM_INT);	//按名字绑定
	$stmt->bindColumn("name",$name);
	$stmt->bindColumn(3,$age);	//按顺序绑定
	$stmt->bindColumn(4,$sex);

	while($stmt->fetch()){
		echo $id.$name.$age.$sex;
	}

	//获取总行数
	echo $stmt->rowCount();

	//获取总列数
	echo $stmt->columnCount();

	//获取列信息
	for($i=0;$i<$stmt->columnCount;$i++){
		$field=$stmt->getColumnMeta($i);
		echo $field["name"];
	}

	fetch()
		默认返回的数据包含了索引和关联数组
		通过制定常量参数可以设置返回的数组
			PDO::FETCH_NUM
			PDO::FETCH_ASSOC
			PDO::FETCH_BOTH
		也可以通过设置获取模式来设置
			$stmt->setFetchMode(PDO::FETCH_NUM);

	fetchColumn
		获取单列

