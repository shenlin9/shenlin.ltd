---
title: 细说 PHP - 错误和异常
categories:
  - PHP
tags:
  - PHP
---


错误类型
	1、语法错误
	2、运行时错误
	3、逻辑错误

错误报告
	php配置文件中和错误相关的搜"Error handling and logging"
	或
	使用下列PHP函数设置错误报告：

		error_reporting(E_ALL & ~E_NOTICE & E_WARNING);

		ini_set("error_reporting",E_ALL);


		echo ini_get("error_reporting");

	错误 E_ERROR
	警告 E_WARNING
	注意 E_NOTICE


	getType($var); //注意，程序仍继续执行
	getType(); //警告，程序仍继续执行
	getType() //错误，程序终止执行

	上面的语句加@可临时屏蔽掉所有的注意、警告和错误，即：

	@getType($var); 
	@getType(); 
	@getType() 

错误日志

	1、指定错误日志
		ini_set("error_reporting",E_ALL);
	2、关闭错误输出
		ini_set("display_errors","off"); 
		设置此值发生错误将不输出到浏览器
		如果不指定错误日志位置，将默认写入web服务器的错误日志中，如apache错误日志
		若此值设置为true，则输出到浏览器，但不会再写入apache错误日志
	3、开启错误日志功能
		ini_set("log_errors,"on");
	4、指定错误日志文件
		ini_set("error_log","c:/error.log");

		设置为syslog则将错误写入了操作系统的错误日志中：
		ini_set("error_log","syslog");
	5、使用函数产生错误日志	
		error_log("this is a error message");


	建议开发时输出所有的错误报告，有利于进行程序调试；运行阶段不要输出任何错误报告。

异常处理
	意外，程序运行过程中发生的意料之外的事
	是php5的一个新特性

	try{

		throw new Exception("xxx失败");

		throw new MyException1("xxx失败");

		throw new MyException2("xxx失败");

	}catch(MyException1 $异常参数){

	}
	}catch(MyException2 $异常参数){

	}
	}catch(Exception $异常参数){	//将系统的Exception处理放最后，这样如果Exception的子类没有对应的catch，则会交给Exception类

	}

	1、如果try中代码没有问题，则不执行catch代码
	2、如果try中有异常发生，则在try中抛出异常对象，且抛给了catch中的异常参数，抛出异常后try中的代码中断执行，跳转到catch中去执行
	3、我们主要做的不是提示发生了什么异常，而是要在catch中解决这个异常，解决不了，则输出给用户。

内置异常类和自定义异常类

	1、自定义异常类必须是内置异常类的子类
	2、内置异常类只要构造方法和toString可以重载，其他都是final
	3、自定义异常类应该是解决当发生此异常时的处理，即在这里换种方法实现发生异常代码要完成的功能，而不是通知

