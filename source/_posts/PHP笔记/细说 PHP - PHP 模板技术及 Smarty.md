---
title: 细说 PHP - PHP模板技术介绍及自定义模板引擎
categories:
  - PHP
tags:
  - PHP
---

PHP模板技术介绍及自定义模板引擎

	Smarty模板引擎只是使用php开发的一个项目

	使用模板引擎的php程序的流程：用户访问php程序---》php程序里加载模板引擎---》php程序传递变量给模板引擎---》模板引擎加载模板---》模板引擎替换模板中的标识---》模板引擎创建编译好的文件---》模板引擎输出编译好的文件


Smarty介绍安装及配置

	使用smarty程序员需要做：	
		1、php程序和原来一样
		2、增加加载Smarty引擎，并创建对象
		3、为引擎分配php程序变量
		4、为引擎传递模板参数并显示


	使用smarty美工需要做：	
		1、制作html页面
		2、使用Smarty表现逻辑、放变量、遍历和判断数据

	安装
		下载Smarty压缩文件包，解压后将lib目录解压到网站目录下，包含lib目录下Smarty.class.php

		include "./lib/Smarty.class.php";

		$title="this is my title";
		$content="this is my title";

		$tpl=new Smarty();
		$tpl->assign("title",$title);
		$tpl->assign("content","$content");
		$tpl->display("mytemplate.tpl);

	初始化

		$tpl->template_dir="./tpl";	//模板目录
		$tpl->compile_dir="./com";	//编译目录
		$tpl->left_delimiter="<{";	//定界符，默认定界符{}和css、javascript的函数等冲突 $tpl->right_delimiter="}>";

	注意事项
		1、访问的是php文件，而模板是php文件包含的，所以模板中的图片、css文件、js文件路径都要以php文件为准
		2、php程序向模板引擎传递模板路径和名称时，以及模板包含其他模板时，等等所有的模板路径都要以Smarty中设定的模板目录template_dir为基目录
		3、如果想让各个目录下的php程序都可以加载Smarty和使用Smarty指定的模板和编译目录，唯一办法就是使用绝对路径。（注意：不同于asp，/代表网站根目录只是相对于客户端来说，对php来说/代表系统根目录）

		define("Root","C:/AppServ/www/");    //define("Root",$_SERVER["SCRIPT_FILENAME"]);
		include Root."libs/Smarty.class.php";
		$tpl=new Smarty();
		$tpl->template_dir=Root."tpl/";
		$tpl->compile_dir=Root."com/";

在Smarty中使用变量

	注释:<{* *}>，像php注释一样，不会输出到客户端

	Smarty是以变量为主的，所有的访问方式都是基于变量的，有三种变量使用方式：

	一、php程序中为模板引擎分配变量

		php从数据库或文件以及通过算法生成的动态数据

		任何类型的数据都可从php分配过来：

			标量：string int double boolean
			
			复合：array object NULL

			注意：  索引数组在模板中和php中使用方式相同，都是 数组名[索引下标]

				但关联数组在模板中不是使用 数组名["下标名"]的方式使用，而是使用 数组名.下标名 的方式

			模板中的变量可以进行运算：+ - * /

	二、在模板配置文件中给模板变量

		和模板的界面设计相关的变量

		配置文件：为键值对的形式
			border=1
			bgcolor=yellow	//公用变量，包含此配置文件后即可使用变量<{#bgcolor#}>

			[one]
			aa=111		

			[two]		//[] 设置了区域变量
			bb=222

			[three]
			cc=333		//包含配置文件时，使用section指定区域<{config_load file="view.conf" section="one"}>然后才可以使用此区域中变量<{#aa#}>

		配置文件的公用变量一般存放各页面都要用到的配置信息，而区域变量分别包含一级、二级、三级页面的不同配置信息

		使用步骤：

		1.修改Smarty属性，指定模板配置文件的目录：
			$tpl->config_dir=Root."config";

		2.模板中导入配置文件：
			<{config_load file="view.conf"}>

		3.模板中使用配置文件变量:
			<{#配置文件变量名#}>  <{  #配置文件变量名#  }> #号是和变量名紧挨着
			或
			<{$smarty.config.配置文件变量名}>


	三、模板保留变量


		1、模板提供了对超全局数组的直接访问，免去php程序再传值的麻烦：

			<{$smarty.get.page}> 		等价于php程序 $_GET["page"]
			<{$smarty.post.page}> 		等价于php程序 $_POST["page"]
			<{$smarty.request.page}> 	等价于php程序 $_REQUEST["page"]

			<{$smarty.cookie.page}> 	等价于php程序 $_COOKIE["page"]
			<{$smarty.session.username}> 	等价于php程序 $_SESSION["username"]

			<{$smarty.server.SERVER_NAME}> 	等价于php程序 $_SERVER["SERVER_NAME"]
			<{$smarty.env.PATH}> 		等价于php程序 $_ENV["PATH"]

			$_FILES		无对应
			$GLOBALS	无对应

		2、常量的使用方式

			php中用户自定义常量：
				define("ROOT","c:/myapp/www");
			
			模板中可直接使用常量，无需php程序传递：
				<{$smarty.const.ROOT}>

			模板中也可直接使用系统常量：
				<{$smarty.const.__FILE__}>

		3、时间
			<{$smarty.now}>

		4、
		5、
		6、


在Smarty模板中使用自定义函数

	html标签可分为单标签和块标签，单标签如：<input type="text" name="user">，块标签如：<font color="red" size="7"></font>，块标签有结束标记。

	Smarty模板中的自定义函数也可实现单标签和块标签，分别对应于注册函数和注册块。

	一、函数使用方式1：注册函数

		因Smarty是以变量为主的，在Smarty中调用php函数，其实是以自定义标记的形式来使用，类似于html标记

		1、php中编写函数，要求函数的参数只有一个，且参数类型为关联数组

			function demo(array $arr){
				echo $arr["id"];
				echo $arr["name"];
				echo $arr["price"];
			}

		2、php中模板引擎注册此函数，并给出模板中的调用名字

			$tpl->register_function("demo","hello");

		3、模板中按模板标签的形式使用此函数，关联数组的下标为属性名，即：

			<{函数名 关联数组下标1=下标所对应的值  关联数组下标2=下标所对应的值  关联数组下标3=下标所对应的值}>

			<{hello id="1" name="zhangsan" price="11"}>

		其实，Smarty模板中的include文件和load_config就是注册函数的使用方式：

			<{include file="header.tpl"}>

			<{load_config file="view.conf" section="homepage"}>

	一、函数使用方式2：注册块
		
		1、php中编写函数，函数的参数为4个
			参数1：关联数组
			参数2：块中的内容
			参数3：引用传值，&$smarty
			参数4：引用传值，$smarty会调用

			function test($arr,$content,&$smarty,&$smartycall){
				echo $arr["id"];
				echo $arr["name"];
				echo $arr["price"];
				echo $arr["content"];
			}

		2、php中模板引擎注册块，并给出模板中的调用名字

			$tpl->register_block("world","test");

		3、模板中按模板标签的形式使用此函数，关联数组的下标为属性名，即：

			<{函数名 关联数组下标1=下标所对应的值  关联数组下标2=下标所对应的值  关联数组下标3=下标所对应的值}>

			<{world id="1" name="zhangsan" price="11"}>
				这里的内容会传给第二个参数	
				这里也可以使用php变量，如<{$var}>,当然这个变量需要php传递过来：$tpl->assign("var",$myvar);
			<{/world}> 

		其实，Smarty模板中的if就是注册块的使用方式：

			<{if $isTrue}>
				welcome,haha
			<{else}>
				no,sorry
			<{/if}>

	上述两种方式，即注册函数和注册块
		1、都可以在注册函数标签字符串或注册块标签字符串的属性中使用php变量，当然需要先传递值
		2、变量做属性，不能加引号
		3、但双引号中也可以使用php变量，但此变量只能保护字母，数字，下划线和中括号，若包含其他字母则要使用``括起来，如关联数组在引号中使用时，需要使用``括起来，`$arr.one`否则会原形输出：
		4、属性中还可以使用配置文件读取过来的变量，用##括起来的，综合实例：
		
			<{hello id="1" name="zhang $haha san" price=$price color=#color#}>
		
			<{world id="1" name="zhang $haha san" price=$price color=#color#}>
				这里的内容会传给第二个参数	
				这里也可以使用php变量，如<{$var}>,当然这个变量需要php传递过来：$tpl->assign("var",$myvar);
			<{/world}> 


	二、Smarty插件功能

		smarty的plugins是插件目录，可以定义函数和块和变量调节器

		plugins目录下文件的命名规范：

			function开头的为函数
			block开头的为块函数	+.【函数名】+.php，如function.hello.php
			modifier开头的为变量调节器

		函数文件规范：

			函数以smarty_function_开头
			块函数以smary_block_开头	+【函数名】，如function smarty_function_math($params,&$smarty)
			变量调节器以smarty_modifier开头

		上述两个的【函数名】和模板中调用的函数名<{【函数名】 id="" name=""}>是一样的，则函数会被自动调用，不再用注册	
	

	Smarty的自定义函数：

		Smarty的plugins目录下的函数均为自定义函数，可自由修改，Smarty还有内建函数，是写在Smarty内部的，不可修改


## 使用Smarty中的变量调解器

	如果没有变量调节器，则同一个字符串，使用原形，小写字符串，部分字符串时，则需要多次在php中处理好后再多次传递

	在Smarty模板文件中，在变量后面使用 | 加函数名，这个函数第一个参数就是这个变量，以后每个参数直接使用冒号分隔：

		<{$var|myfunction:param1:param2}>

		<{$var|myfunction:"strtoupper"}>

		变量调节器也可以进去组合，称为组合变量调节器，如字符串先转换为大写，再截取：

			<{$var|strtoupper:param1:param2|substring:param1:param2}>

	变量调节器在plugins目录下文件的命名规范：

		modifier.方法名.php

	函数规范：

		变量调节器函数以smarty_modifier开头，后面是方法名，第一个参数为调用此函数的字符串

		function smarty_modifier_myfunction($str,$ope){

		}

		系统提供的变量调节器：

			truncate 截取中文会导致乱码，utf-8和gb2312有不同的截取方式


## 使用Smarty中提供的内建函数

	内建函数和自定义函数的用法相同，都是使用标记

	自定义函数可以通过注册和插件方式，加入自己的函数到smarty中，相当于扩展

	系统提供的自定义函数可以改，可以删

	Smarty内部的函数只能按手册提供的方式使用，不可以改，也不可以删除

	流程控制if，数组的遍历，文件包含，配置文件导入都要使用内建函数

	include和assign：
		通过assign为模板引擎传递变量时，被include的子模板也可以使用此变量，子模板还可再包含子模板，也可使用变量。

	capture:将capture块直接的输出存到变量里，不输出到页面上

		<{capture name="output"}>
			<hr><span>asdf</span>
			<{$var}>	
		<{/capture}>

		<{$smarty.capture.output}>

	if语句：

		<{if $var==1}>
		<{elseif}>
		<{elseif}>
		<{else}>
		<{/if}>
		集合和php的if语句一样，但条件判断的符号很多

	foreach,foreachelse
		和php的foreach用法相同，如果遍历的数组为空，则执行foreachelse

		<{foreach from=$arr item="val" key="k" name="one"}> //name是为循环起的名字
			<{$smarty.foreach.one.literation}>---<{$k}>---<{$val}>
		<{foreachelse}>
			数组为空或没有分配过来
		<{/foreach}>

		这个循环共循环了<{$smarty.foreach.one.total}>次

	section,sectionelse

		和foreach一样，也是可以遍历数组的，但效率比foreach高，功能比foreach多，建议使用section处理数组

		foreach被编译成了php的foreach，而section被编译为了php的for循环，不用像foreach一样为每个变量赋值

		缺点是不能遍历关联数组，只能是索引数组，且是下标连续的

		<{section loop=$arr name="ls"}>
			<{$arr[ls]}>
		<{sectionelse}>
			数组为空
		<{/section}>

## 使用Smarty中的强大缓存技术

	0、判断是否被缓存

		isCached("test.tpl",$_SERVER["REQUEST_URI"]);

	1、开启缓存
		$tpl->caching=0; //开发阶段不要开启，运行阶段再开启	

	2、指定缓存更新时间
		$tpl->cache_lifetime=60*60*24*7

	3、指定缓存文件保存位置
		$tpl->cache_dir=ROOT."cache";

	4、指定缓存ID

		一个模板只能有一个缓存文件，如果一个模板有多个文章，则需要每个文章有一个缓存

		$tpl->display("test.tpl",cacheid);

		cacheid每次变化一个值就会有一个缓存，最好使用url作为cacheid: $_SERVER["REQUEST_URI"]

	5、局部不缓存

		(1)、自己使用register_block的方式写一个<{nocahce}><{/nocache}>标签

			php程序：
				$tpl->register_block("nocache","notcache",false); //第三个参数即指明不缓存

				function notcache($param,$content){
					return $content;
				}

			Smarty模板：
				<{nocahce}>
					<font color=red><{$var}></font>
				<{/nocache}>
				
		(2)、使用插件块的方式

			文件名：block.nocache.php

			函数：
				function smarty_block_nocache($params,$content,&$smarty){
					return $content;
				}

			但默认所有的块都是缓存的，即使经过上面修改后，还不行，还需要修改smarty被编译后的类：

				打开libs目录下的Smarty_Compiler.class.php

				大概713行附近
					$this->_plugins['block'][$tag_command]=array($plugin_func,null,null,null,true);
				修改为：
					if($tag_command=="nocache")
						$this->_plugins['block'][$tag_command]=array($plugin_func,null,null,null,false);
					else
						$this->_plugins['block'][$tag_command]=array($plugin_func,null,null,null,true);

	6、清除缓存

		clearCache
		clearAllCache


## MVC设计模式及PHP框架介绍
	
	M:model，指业务模型
	V:view，指用户视图
	C:control，指控制器，用来处理用户动作，通过动作来完成视图的显示和业务的调度

	模块：不要和模型混淆，模块可以理解为一个独立应用

	操作：指模块可能有的动作，如用户模块的操作有：添加用户、修改用户、删除用户等

	框架：是一个半成品，提取了多个项目都需要用到的模块，可以完成一个项目50%左右的工作量

	框架应具备的功能：
		目录组织结构
		类加载
		基础类
		强制MVC
		URL处理
		输入处理
		错误异常处理
		扩展类

	流行php框架：
		YII		推荐
		ZendFramework	php官方框架，使用和学习都很麻烦，不建议使用

	自己开发框架原因：
		安全
		按需定制


BroPHP框架介绍及目录部署


BroPHP框架的控制器声明与应用

	url采用pathinfo模式，这种模式对搜索引擎兼容性比较好

	http://host/入口文件名/模块名/操作/参数名1/值1/参数名2/值2

	默认模块为index，默认操作为index

	模块对应control目录下同名的文件里的同名的类，一个操作对应类中一个方法

BroPHP框架中模型的声明与应用


BroPHP框架中数据库的统一操作接口


BroPHP视图的声明与应用


BroPHP框架中的自动验证


BroPHP框架内置扩展类库和自定义扩展类


BroPHP框架的综合应用实现用户注册和登录


BroPHP框架的综合应用实例一个完整模块的操作过程

