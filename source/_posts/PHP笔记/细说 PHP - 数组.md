---
title: 细说 PHP - 数组
categories:
  - PHP
tags:
  - PHP
---

数组概述
	1、数组的本质是声明和操作一批变量，成批处理
	2、数组是php支持的8种数据类型中的复合类型
	3、数组可以存储任意长度的数据，也可以存储任意类型的数据
	4、php的数组可以完成其他语言中很多数据结构的功能，如链表、队列、栈、集合类等。

数组的分类：
	1、一维数组，二维数组，三维数组，多维数组
	2、php数组又可分为索引数组和关联数组
		索引数组：下标使用整数做为索引
		关联数组：下标使用字符串作为索引

数组多种声明方式：
	1、直接为数组元素赋值
		$user[0]=1;
		$user[1]="zhangsan";
		$user[2]=10;
		echo "<pre>";
		echo print_r($user);
		echo "</pre>";

		上面的索引下标也可以不写，即：
		$user[]=1;
		$user[]="zhangsan";
		$user[]=10;
		也会自动从0开始索引，结果和上面的效果相同
		如果给出下标，下一个的索引是最大索引值加1，如
		$user[]=1;		--->这里索引是0
		$user[]="zhangsan";	--->这里索引是1
		$user[5]=10;
		$user[]="boy";	--->这里索引是6

		使用字符串下标：
		$user["num"]=1;
		$user["name"]="zhangsan";
		$user["age"]=10;
		$user["sex"]="boy";

		但将索引数组和关联数组混合声明时，关联数组的声明不影响索引下标的声明
		$user["num"]=1;
		$user["name"]="zhangsan";
		$user[1]=10;
		$user["sex"]="boy";
		
	2、使用array()函数
		1、默认是索引数组
			$user=array(1,"zhangsan",10,"boy");
		2、声明关联数组需使用=>指定键值对
			$user=array("id"=>1,"name"=>"zhangsan",12=>10,"boy");
			其中前两项声明为了关联数组，后两项声明为了索引数组，且指定了下标从12开始

		3、声明二维、三维数组
			$user=array(array(1,"zhangsan",10,"boy"),array(1,"lisi",10,"boy"),array(1,"wangwu",10,"boy"));

			$info=array(
					"user"=>array(
						array(1,"zhangsan",10,"boy"),
						array(2,"lisi",10,"boy"),
						array(3,"wangwu",10,"boy"),
					),
					"score"=>array(
						array(1,"zhangsan",10,"boy"),
						array(2,"lisi",10,"boy"),
						array(3,"wangwu",10,"boy"),
					),
					"class"=>array(
						array(1,"zhangsan",10,"boy"),
						array(2,"lisi",10,"boy"),
						array(3,"wangwu",10,"boy"),
					)
				);
	3、使用其他的函数声明
		file();


数组的多种遍历方式：

	1、使用for语句
		数组必须是索引数组，且下标是连续的，但php下标可不连续，且可能是关联数组，故这种方式不是php首先方式

		$user=array(1,"zhangsan",10,"boy");
		for($i=0;$i<count($user);$i++){
			echo "\$user[{$i}]=".$user[i]."<br>";
		};
		
	2、使用foreach语句

		foreach(数组变量 as 变量值){
			//循环体
		}

		循环次数由数组的元素个数决定
		每一次循环都会将数组中元素分别赋值给后面变量

		foreach($user as $var){
			echo $var."<br>";
		}

		foreach循环时也可获得下标

		foreach(数组变量 as 下标变量=>变量值){
			//循环体
		}

		foreach($user as $key=>$var){
			echo $key."-------".$var."<br>";
		}

	3、使用while() list() each()组合

		each()函数
			1、需要一个数组做参数
			2、返回值也是一个数组
			3、返回来的数组有0,1,key,value四个下标
				0和key下标是当前数组元素的键
				1和value下标是当前数组元素的值
			4、默认当前元素是第一个元素，执行一次就向后移动一个元素，到最后取不到元素时返回false

			$user=array("id"=1,"name"=>"zhangsan","age"=>10,"sex"=>"boy");
			while($arr=each($user)){
				echo $arr[0]."=>".$arr[1]."<br>";
				echo $arr["key"]."=>".$arr["value"]."<br>";
			}

		list()函数
			1、需要将数组赋值给这个函数
			2、数组中的元素个数，要和list()函数中的参数个数相同
			3、数组中的每个元素值会按索引顺序赋值给list函数中的每个参数，list将每个参数转为变量
			4、只能接受索引数组

			list($name,,$sex)=array("zhangsan",10,"boy");
			echo $name."----".$sex;

		list()和each()组合使用
			
			$user=array("id"=>1,"name"=>"zhangsan","age"=>10,"sex"=>"boy");

			while(list($key,$value)=each($user)){
				echo $key."=>".$value."<br>";
			}

		数组内部指针控制函数
			
			next(数组);	//移到下一个元素
			prev(数组);
			reset(数组);	//移到第一个元素
			end(数组);
			
			current(数组);	//获取当前元素value
			key(数组);	//获取当前元素key


php中预定义的超全局数组

	1、包含了来自web服务器端、客户端、运行环境和用户输入的数据
	2、在全局范围内可以直接使用
	3、用户不能自定义这些儿数组
	4、在函数内不用使用global关键词导入即可直接使用

	$_GET
		经由url请求提交至服务器脚本的变量，长度限制8092字节
		<a href="demo.php?username=ssl&age=10&sex=boy">go</a>
		可以直接使用同名的变量接收，即
		echo $username;
		echo $age;
		echo $sex;
		但一般为安全起见，这个选项在php配置文件中是关掉的，建议使用$_GET
		echo $_GET["username"];
		echo $_GET["age"];
		echo $_GET["sex"];
	$_POST
		经由HTTP POST方法至服务器脚本的变量
	$_COOKIE
		
	$_REQUEST 
		经由GET、POST和COOKIE机制提交到脚本的变量，当不确定用户提交数据的机制时使用此数组
	$_FILES
		经由HTTP POST上传文件提交到脚本的变量
	$_SESSION

	$_ENV
		执行环境提交到脚本的变量
	$_SERVER
		此数组中保存的变量由web服务器设定 或 直接和当前脚本的执行环境相关联
	$GLOBALS
		当前脚本所有有效地变量，包括所有超全局数组如$_GET,$_POST...和当前脚本文件中用户定义的全局变量如$a,$b，键名也分别为超全局数组变量名称和全局变量名，也因此在函数中，访问全局变量$a，除了可以使用global $a;包含后使用外，还可以直接使用$GLOBALS["a"]使用。


数组的相关处理函数：

	一、数组键值操作函数
		1、array_values()
		2、array_keys()
		3、in_array()
		4、array_key_exists()
		5、array_flip()
		6、array_reverse()

	二、统计数组元素的个数和唯一性的函数
		1、count(); sizeof();
		2、array_count_values();
		3、array_unique();

	三、使用回调函数处理数组的函数
		1、array_filter();
		2、array_walk();
		3、array_map();

	四、数组的排序函数
		1、根据元素值对数组排序（忽略键值）
			sort();rsort();
		2、根据键名对数组排序
			ksort(); krsort();
		3、根据元素值对数组排序（保留键值）
			asort(); arsort();
		4、根据自然排序法对数组排序，字母按a到z，数字按1到9
			natsort(); natcasesort();
		5、根据用户自定义规则对数组排序
			usort(); uasort(); uksort();
		6、多维数组排序
			array_multisort();

	五、数组的拆分、合并、分解、结合的数组函数
		1、array_slice();
		2、array_splice();
		3、array_combine();
		4、array_merge();
		5、array_intersect();
		6、array_diff();
		x、+ 比较下面的输出
			$a1=array(1,2,3,4);
			$a2=array(5,6,7,8);
			$a3=$a1+$a2;
			print_r($a3);

			$a1=array(1,2,3,4);
			$a2=array(10=>5,6,7,8);
			$a3=$a1+$a2;
			print_r($a3);
	
	六、数组与数据结构的函数
		1、使用数组实现堆栈
			入栈：$arr[]="a"; 和这句效果相同，但比这句效率高array_push($arr,"a");
			出栈：$value=array_pop($arr); 注意：$arr[0]只是读取不是出栈
		2、使用数组实现队列
			array_unshift($arr,"c","d");
			$value=array_shift($arr);
		3，使用数组实现链表
			unset($arr[5]);	

	七、其他与数据操作有关的函数
		1、array_rand();
		2、shuffle();
		3、array_sum();
		4、range();	//除了是数字还可以是字母，如range("a","k");


