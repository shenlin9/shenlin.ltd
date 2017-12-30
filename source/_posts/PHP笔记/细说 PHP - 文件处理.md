---
title: 细说 PHP - 文件处理
categories:
  - PHP
tags:
  - PHP
---

文件处理概述
	1、文件处理都是使用系统函数完成的
	2、这些函数都是基于linux/unix系统为模型

文件处理
	1、获取文件类型
		windows中只能获取file、dir、unknown三种类型
		linux中的所有类型均可获取：block、char、dir、fifo、file、link、unknown
			block：块设备文件，如磁盘分区，软驱，光驱
			char：字符设备，I/O中以字符为单位的设备，如键盘、打印机
			dir：目录也是文件的一种
			fifo：命令管道，信息从一个进程传入另一个进程
			file：普通文件
			link：链接文件

		filetype(目录或文件名);
		is_dir();
		is_file();
		is_link();
		is_writable(); 别名 is_writeable();
		is_readable();
		is_executable();
		is_uploaded_file(); //判断文件是否通过HTTP POST上传
	2、获取文件属性
		file_exists();
		filesize();
		filectime(); //inode修改时间，并不是创建时间，很多unix文件系统没有创建时间
		filemtime(); //文件内容修改时间
		fileatime(); //最后访问时间
		stat();	

	3、和文件路径相关的函数

		路径分隔符
			linux/unix  	/
			windows		\
				因\同时也是转义字符，因此在双引号中，使用\做分隔符时，需要其避免和后面的字符组成转义字符，需要使用两个\
				"c:\\windows\\ntext"

			分隔符常量：DIRECTORY_SEPARATOR 在不同系统上，会自动转换

			不管什么系统，PHP的目录分隔符都支持/

			PHP和Apache的配置文件中，也都使用/做为分隔符

		相对路径
			.	当前目录
			..	上一级目录

			./login.php 和 login.php 效果相同

		绝对路径
			/	根路径，存在两种情况
				客户端访问服务器端文件时，是通过apache访问，apache只能管理网站目录，所以根指的是网站根目录
					echo "<img src='/logo.gif'>";	
				在服务器中执行路径，根指的是操作系统根目录
					file_put_contents("/aaa.txt","abc");

		basename();
		dirname();
			可以通过嵌套取得上级的上级目录
			dirname(dirname("./aaa/bbb/ccc.php"));
		pathinfo();

	4、文件操作

		创建文件
			touch();
		删除文件
			unlink();
		移动文件/为文件重命名
			rename("当前文件路径","目标文件路径");
		复制文件
			copy("当前文件路径","目标文件路径");

		以上操作需要PHP执行对应文件的权限，即apache对应的用户有此权限

		和权限设置相关的函数：
			chgrp
				改变文件所属组
			chown
				改变文件所有者
			chmod
				改变文件模式

			filegroup
			fileowner

	5、读写文件内容

		file_get_contents(); //需php5支持
			读取文件中全部内容后并返回
		file_put_contents(); //需php5支持
			文件不存在，则自动创建后再写入内容
			文件存在，则覆盖文件内容
			不能追加内容，也不能加锁

		file();
			读取文件全部内容并返回数组，每行为一个元素
		readfile();
			读取文件中全部内容后并全部输出

		fopen();
			返回值：成功返回资源类型，失败返回false
			参数1：文件路径
			参数2： r	以只读的模式打开
				r+	除了读，还可以写
				w	以只写的方式打开，文件不存在，则创建文件并写入，文件存在则覆盖原内容
				w+	除了写，还可以读
				x
				x+
				a	以只写的方式打开，文件不存在，则创建文件并写入，文件存在则在原文件内容后追加
				a+
				b	以二进制模式打开文件，如图片、电影，但需要和r、w联合起来使用，如rb、wb
				t	以文本模式打开文件
		fclose();
			资源类型需要关闭，再次读取时也需要先关闭再打开

		fwrite(); //别名fputs();
			参数1：fopen返回的文件资源
			参数2：写的内容

		fread();
			参数1：fopen返回的文件资源
			参数2：读取指定长度字符


			读取本地文件中的全部内容，使用filesize
			$file=fopen("demo.php","r");
			echo fread($file,filesize($file));
			fclose($file);

			读取远程文件中的全部内容，使用feof
			$str="";
			$file=fopen("http://www.163.com/","r");
			while(!feof($file)){
				$str.=fread($file,1024);
			}
			fclose($file);
			echo $str;

			feof();
				参数：文件资源
				如果文件打开错误或到文件末尾则出错

		fgetc();
			一次从文件中读取一个字符
		fgets();
			一次从文件中读取一行字符

		以上函数支持本地和远程文件，如远程 echo file_get_contents("http://www.163.com/");

	6、文件内部指针移动
		ftell();
			返回当前文件指针位置
		fseek();
			移动文件指针
		rewind();
			移动指针到文件头部

	7、文件锁定的机制处理
		flock();
			为文件加锁，释放文件锁


目录处理

	不同于文件的各种操作都有系统函数，目录的各种操作都需要自己编写函数，创建目录除外。

	1、遍历目录
		opendir();
		readdir();
		closedir();
		rewinddir();
		is_dir();
	2、创建目录
		mkdir();
	3、删除目录
		rmdir();
			删除空目录
			自己编写删除非空目录
	4、重命名/移动目录
		prename();
	4、复制目录
		自己编写
	5、统计目录大小
		自己编写

文件上传

	PHP配置文件中和上传文件有关的选项

		file_uploads = on
			允许使用php上传文件

		upload_max_filesize = 200M	
			最大不要超过系统内存

		post_max_size = 250M
			此项值除了包含上传文件本身外，还包括表单的其他数据，如商品名称等，所以要大于上面的值

		upload_tmp_dir = c:/upload

	上传表单需要注意的事项
	
		指定表单编码的数据方式enctype="multipart/form-data"（只有上传文件才使用这个值），并带有一些常规的信息

		提交方法必须是HTTP POST

		使用<input type="file">来选取上传文件

		建议加上<input type="hidden" name="MAX_FILE_SIZE" value="1024000">，单位是字节

	PHP处理上传的数据

		$_POST
			处理非上传数据

		$_FILES
			处理上传文件的数据

			Array
			(
			 [name] => hello.jpg
			 [type] => images/pjpeg
			 [tmp_name] => c:\windows\temp\php68.tmp //脚本结束时此临时文件即被删除，将此临时目录的文件用脚本移动到指定目录即完成上传
			 [error] => 0
			 [size] => 24485
			)

			上传文件还需注意：
			1、使用$_FILES["mypic"]["error"]检查错误
			2、使用$_FILES["mypic"]["size"]限制大小，单位字节 2M=pow(2,20);
			3、使用$_FILES["mypic"]["type"]或文件的扩展名限制类型
				Apache的mime.types可以查看所有MIME类型

				list($bigtype,$smalltype)=explode("/",$_FILES["mypic"]["type"]);
				if($bigtype!="image"){

				}else{

				}
			4、将上传后的文件名改名，防止用户意外访问

				先使用is_uploaded_file()判断是否为上传文件

				再使用move_uploaded_file()移动上传文件，不使用copy() rename()等函数

文件下载

	http请求:
		1、协议和版本 HTTP 1.1
		2、头信息
		3、表单信息

	http响应：
		1、状态 200 ok 404 页面没找到
		2、响应头信息
		3、数据发送

	按HTTP协议的报文顺序，下述的header前不能有任何输出：
	<?php
		header("Content-Type:text/html;charset=utf-8");
		header("Location:two.html");
	?>

	告诉浏览器以附件形式处理：
		
		header('Content-Disposition:attachment;filename="mytwo.html"');
		readfile("two.html");

		header("Content-Type:image/gif");	//即使不加此行，浏览器也可能自动检测
		header('Content-Disposition:attachment;filename="logo.gif"');
		header("Content-Length".filesize("logo.gif"));	//即使不加此行，浏览器也可能自动检测
		readfile("logo.gif");
		readfile("two.html");

文件上传类：
		function __construct($options=array()){
			foreach($options as $key=>$val){
				if(in_array($key,get_class_vars(get_class($this)))){
					this=>$key=$val;
				}
			}
		}

