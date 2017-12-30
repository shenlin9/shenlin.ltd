---
title: 细说 PHP - 常量和变量
categories:
  - PHP
tags:
  - PHP
---

变量的声明

变量的声明
    使用一个美元符号后跟变量名来表示: `$变量 = 值`
    以字母或下划线开头，后面跟数字、字母或下划线
        这里的字母除了小写a-z和大写A-Z外，还包括ASCII码从127到255（0x7f-0xff）的字符，是一些乱七八糟的字符使用chr输出后也看不到
        因此可声明为php变量的字符个数为：小写字母26+大写字母26+数字10+下划线1+ASCII从127到255的129个=182个，其中常用的为63个，开头的为52个
        鉴于上述描述，php变量的正则可描述为`/^[a-zA-Z_\x7f-\xff][a-zA-Z0-9_\x7f-\xff]*/`
        变量名中可以使用中文

变量的赋值
    分为传值赋值和引用赋值两种
    默认总是为传值赋值
    将&符号加到要赋值的原变量前，即为引用赋值
        $a=&$b;
    只有有名字的变量才可以引用赋值：
        $a=&(10*18);	//错误，引用了没有名字的表达式
        $a=&test();	//错误

变量的初始化

    php的变量不需要初始化，但未初始化的php变量会发出E_NOTICE错误，但是在向一个未初始化的数组附加单元时不会
    ```php
    $str.="aaa";
    $int+=10;	
    $float+=1.11;
    echo ($bool?'true':'false');	//以上均会发生E_NOTICE错误
    $arr[]=11;			//这个不会
    ```
    未初始化php变量的值是其类型的默认值：字符串为空，布尔值为false，整型和浮点型为0，数组为空数组
    依赖未初始化变量的默认值可能会产生问题

变量的生命周期和isset语言结构

    变量在使用之前，是不存在的，即还未创建到内存，使用isset判断则返回false
    变量在第一次使用时，则被自动创建到内存，同时获得了一个与应用环境对应的类型，若这时没有指定变量值，则返回类型的默认值（不同于其他语言如C，不指定变量值时，其默认值是指向的内存空间中的垃圾值），这时isset返回true
    销毁的变量的方法：设置变量值为null $a=null;
              使用unset方法 unset($a);
    无论使用哪种方法，销毁变量后isset返回false
    还有一种特殊情况，变量已被创建到内存，但其值为null，则isset也返回false

    isset语言结构
        判断一个变量是否存在 同时 变量存在时其值是否为null
        只能用于判断变量，不能传表达式、函数等
        参数的个数不限，当多个参数时，从左到右分别判断各参数的值(遇到false就停止)，进行&&运算，返回整个isset的值

    总结isset：
        变量已被创建到内存且其值不为null时返回true
    

empty语言结构

    未创建的变量使用empty判断不会发生notice错误，返回值为true
    变量已创建，当其值为类型的默认值时，返回值为true

    访问一个不存在的数组元素时，is_null等函数会发生错误，而语言结构isset和empty则不会，使用isset和empty对数组和元素检测

        ```php
        <?php
        $item = array(); 
            //Now isset($item) returns true. But isset($item['unicorn']) is false.
            //empty($item) is true, and so is empty($item['unicorn']

        $item['unicorn'] = '';
            //Now isset($item['unicorn']) is true. And empty($item) is false. 
            //But empty($item['unicorn']) is still true;

        $item['unicorn'] = 'Pink unicorn';
            //isset($item['unicorn']) is still true. And empty($item) is still false. 
            //But now empty($item['unicorn']) is false;
        ?>
        ```

    总结empty：
        变量已被创建到内存且其值不为类型默认值时返回true


php是弱类型语言，变量的类型由存储的值确定
    

    变量的声明和使用都需要$
    不能使用php的运算符号
    可以使用系统关键字作为变量名，如$if="aa"; $self="aa";  $parent="aa";但$this是一个特殊的变量，不可以被赋值
    变量和常量区分大小写，但其他不区分，如函数echo和Echo是一样的

检测变量是否初始化
    使用isset()语言结构

可变变量

	一个变量的变量名可以动态设置和使用，如

	<?php
		$one = "xxx";
		$two = "one";
		$three = "two";
		$four = "three";

		echo $four;
		echo $$four;
		echo $$$four;
		echo $$$$four;

变量的引用赋值

	使用&符号

	<?php
		$one = "10";
		$two = &$one;


变量的类型

	PHP虽是弱类型语言，但也有变量类型概念，共有8种类型，4种标量，2种特殊类型，2种复合类型

	4种标量：
		整型 int integer
		浮点型 float double real
		布尔型 bool boolean
		字符串 string
	2种复合类型
		数组 array
		对象 object
	2种特殊类型
		资源类型 resource
		NULL类型
			表示变量没有值
			只有一个值null，不区分大小写
			判断一个变量值是否为null时，$var===null比is_null($var)快
			下列情况下，一个变量被认为是null
				未被赋值
				被赋值为null
				被unset()
			空数组和null
				空数组和null使用==进行比较时，空数组会被转为null，结果返回true
				空数组和null使用===比较时，因类型不同，一个为数组类型一个为NULL类型，返回false
				空数组使用is_null判断时，is_null只判断是不是null值，不是返回false
			变量赋值为null和被unset()的区别：
				当一个变量$a被引用传值给变量$b时
					unset()其中任意一个变量，不影响另一个变量
						$a=123;
						$b=&$a;
						unset($a); 	//只影响$a
						var_dump($a);
						var-dump($b);
					将任意一个变量赋值为null，则同时影响另一个变量
						$a=123;
						$b=&$a;
						$a=null; 	//同时影响$a和$b
						var_dump($a);
						var_dump($b);
				被unset()后的变量会报Undefined Variable错误，但赋值为null后的变量则不会，如下列中的is_null和var_dump都是在判断不存在变量时会报错
					$a=123;
					$a=null;
					echo is_null($a);
					var_dump($a);
                unset()可理解为将变量名从内存中抹去，而赋值为null则是将变量名对应的真实变量内容置为NULL值
				
	关于处理未定义变量时是否报错：
		语言结构更容易忽略错误，因此语言结构处理不存在的变量并不报错，如isset,empty；但函数则报错，如is_null,var_dump

	is_null和isset
		isset为语言结构，is_null为函数，因语言结构更容易忽略错误，因此isset不存在的变量并不报错，但is_null则报错（empty为语言结构也不报错var_dump为函数也报错)
		is_null判断的是表达式是否为null类型的null值，参数可以为变量或函数或表达式
		isset判断的是变量是否定义或其值是否为null
		is_null始终恰恰好是isset的相反值（只是在遇到unset的变量时is_null会报一个Notice错误）
			<?php

			$quirks = array(null, true, false, 0, 1, '', "\0", "unset");

			foreach($quirks as $var) {
			    if ($var === "unset") unset($var);

			    echo is_null($var) ? 1 : 0;
			    echo isset($var) ? 1 : 0;
			    echo "<br>";
			}

    使用==松散比较时，可转换为null的值 ：false,0,Null,array(),"",没初始化的变量,unset后的变量，赋值为null后的变量,无属性的对象
    使用==松散比较时，可转换为empty的值：false,0,Null,array(),"",没初始化的变量,unset后的变量，赋值为null后的变量,无属性的对象,"0"

	NULL 字节（"\0"）并不等同于 PHP 的 NULL 常数

	is_null
		注意字符串"0"，在empty("0")中返回true，在if("0")的逻辑判断中返回false
		判断一个未定义变量时会报错，但判断值被设置为null的变量时不报错
			is_null($a); //报错
			$b=null;
			is_null($b); //不报错


	var_dump(变量或值);	既可以查看变量或数据的类型，又可以查看值


		<?php

			$var = 10;

			echo "<pre>";
			echo var_dump($var);
			echo "</pre>";

			echo "----------------<br>"

各种类型变量的声明

	<?php
		$int = 10;	//十进制声明
		$int = 045;	//以0开头的表示8进制声明
		$int = 0xff;	//以0x或0X开头的表示16进制声明

		$float = 10;
		$float = 3.14E5
		$float = 3.14E+5
		$float = 3.14E-5	//E也可以小写

		//布尔值的声明，以下都是false的情况
		$bool = false;	//true为真
		$bool = 0;	//相反，非0的数为真
		$bool = 0.000;	//相反，有非0的数出现
		$bool = "";	//相反，非空非0字符串为真
		$bool = "0";//相反，非空非0字符串为真
		$bool = " ";//相反，非空非0字符串为真
		$bool = null;	//相反，非空代表真
		$bool = array();	//相反，非空数组为真
---------------------------------------------------------------------------------------------------------------------------2014-03-08
		//字符串有多种声明方法
		//1、单引号、双引号和界定符都可以声明字符串
		//2、声明的字符串没有长度限制
		//3、双引号字符串中，可以直接解析变量和使用转义字符
		//4、双引号中不能再使用双引号，单引号中不能再使用单引号
		//5、单引号字符串中，不可以解析变量，只能转义单引号本身和转义字符\本身
		//6、双引号中解析变量时，
			$var = 100;
			$str = "aa$varbbdddd";
		     解析失败，因变量$var后无明显的断点符号，如空格，加号，逗号等，例如：
			$str = "aa$var bbdddd";
		     则成功，标准方法应该是使用大括号将变量括起来，$符号可括可不括，例如：
			$str = "aa{$var}bbdddd${var}bbdddd";
		//7、虽然双引号比单引号功能强，但最好使用单引号，原因是双引号的变量解析和转义或消耗相对多的资源
		//8、大量的字符串，使用定界符声明，格式为
		$str=<<<hello
	这里是任意字符串
	asdfdfsdfsdfsadfsdf
hello;
		以<<<开头，其中hello为自定义字符，第一个hello后不能有任何字符，第二个hello必须顶格写，前面不能有任何字符。
		//9、定界符中可以任意使用双引号、单引号，同时可以解析变量和转义字符。


数据类型之间的转换
	分为强制转换和自动转换，最常用为自动转换

	强制转换：
		1、setType(变量，类型); 
			//类型有int或integer,float,double,real,bool或boolean,string,array,object
			//这个函数将原变量的类型改变

		2、在赋值前使用(类型)的形式，不会改变原变量的类型
		$str = "100.112ab";
		$var = (int)$str;	

		3、使用一些儿有针对性的转换函数
		$变量=intval(变量或值);
		$变量=floatval(变量或值);
		$变量=stringval(变量或值);

		注意：整型在内存中占4个字节，最大范围为2.147e9
			浮点型在内存中占8个字节

	自动转换
		$a = 10;
		$b = 100.11;
		$c = "23asd";
		$d = true;
		$sum = $a+$b+$c+d;
		echo var_dump($sum);
与变量和类型有关的一些儿常用函数

	isset();
	empty();
	unset();

	getType();
	setType();
	
	变量类型测试函数
	is_bool();
	is_int() is_integer() is_long()
	is_string();
	is_float() is_double() is_real()
	is_array()
	is_object()
	is_resource()
	is_null()

	is_scalar()
	is_numeric()
	is_callable()

常量的声明与使用

	define("常量名",值);

	常量是一个简单值的标示符
	常量不能改变其值，也不能使用unset()取消
	常量可以不用理会变量范围的规则而在任何地方都可以定义和访问
	常量在声明和使用时都不使用$
	常量名称习惯都使用大写
	常量的值只能用标量类型（int,float,bool,string）
	常量一定要在声明时就给值
	defined("常量"); 检测某个常量是否已定义

系统的预定义常量和魔术常量

	所谓魔术，即会根据环境变化的
	魔术常量以__开头，如__FILE__、__LINE__

