---
title: 细说 PHP - 会话控制 Cookie 和 Session
categories:
  - PHP
tags:
  - PHP
---

## 基于Cookie的会话控制

	借助客户端电脑的一个文件来保存用户信息，再访问同一个网站的各个页面斗会带这些信息过去，服务器通过这些信息就能区分用户。

	cookie通过http头信息传递，因此设置cookie前不能有任何输出

	写入cookie
		$time=time()+3600;
		setCookie("one","111",$time);

	cookie中也可保存数组
		setCookie("arr[0]","111",$time);
		setCookie("arr[1]","222",$time);
		setCookie("arr['x']","333",$time);
		setCookie("arr['y']","444",$time);

	删除cookie
		$time=time()-1;
		setCookie("one");
		setCookie("two","",$time);

	读取cookie
		print_r($_COOKIE);
		print_($_COOKIE["one"]);


## 使用Session进行会话控制

	两种session：
		使用cookie保存sessionid 
		url中带有sessionid

	开启session：session_start();
		分配sessionid给客户端，基于cookie的session则写入了cookie中
		证明：echo $_COOKIE["PHPSESSIONID"];
		因此基于cookie的session，在此语句前不能有任何输出
		php配置文件中开启session：session.auto_start=1，则所有页面不用写session_start();但这种方式限制是不能将对象放入session中，因此不建议使用


	保存数据到session
		$_SESSION["aa"]="111";

	读取session
		echo $_SESSION["aa"];
		echo $_SESSION[session_name()];

	获取sessionid
		echo session_id();

	注销session
		也先要开启session：
			session_start();

		删除session数组中某个值：
			unset($_SESSION["aa"]);
				不要unset($_SESSION);

		清空session：
			$_SESSION=array();

		清除cookie中的sessionid：
			isset($_COOKIE[session_name()]){
				setCookie(session_name(),'',time()-1,'/'); //路径参数不可少
			}

		彻底销毁session
			session_destroy();

	URL中传sessionid

		方案1：指定url中传sessionid的变量

			if(isset($_GET["sid"])){
				session_id($_GET["sid"]);
			}
			session_start();

			echo "<a href='/index.php?sid={session_id()}'>首页</a>";

		方案2：url中使用session_name()传sessionid

			echo "<a href='/index.php?{session_name()}={session_id()}'>首页</a>";


		方案3：使用常量SID
			浏览器支持cookie则值为空
			不支持cookie则代表了session_name和session_id的值

				echo "<a href='/index.php?{SID}'>首页</a>";

		方案4：修改配置文件，使得链接自动增加sessionid，表单、链接、header跳转前都会自动增加，但？？？echo输出的链接、客户端脚本中的跳转系统检测不到？？？，需要自己添加

			session.user_trans_sid = 1

SESSION的高级用法将信息写入到自定义文件

	一、session信息写入到自定义位置

		1、解决跨机保存session
			文件系统：linux下可以使用nfs或是samba，windows下使用共享文件夹

			使用数据库

			使用memcache

			上述都需要使用函数session_set_save_handler()来实现

		2、解决在线用户信息

	二、php配置文件中的常用选择

		session.name
		session.use_trans_sid
			启用SID的支持，默认值为0
		session.save_path
			session文件保存位置，session文件以sess_开头，后面是sessionid

		session.use_cookies
		session.cookie_path
		session.cookie_domain
		session.cookie_lifetime
			保存到客户端的cookie的最长时间
			默认值为0，表示关闭浏览器就删除
			设置此值后，即使用户未关闭浏览器，到时也会删除老cookie，产生新cookie

		session.save_handler
			默认值为files
			自定义位置改为：user
			保存到memcache改为：memcahe

		session.gc_maxlifetime
			gc是garbage collection垃圾回收的缩写
			指定经过多少秒后数据被视为垃圾并被清除
			用户有动作时，session_start会重新设定此时间？？？
			默认是1440秒
		session.gc_probability
			默认值1
		session.gc_divisor
			默认值100
			上面两个函数合起来使用，用于gc进程启动概率：session初始化时，即调用session_start()时，会启动垃圾回收进程来回收垃圾session，但并非超时的session这时一定被清除，因考虑到性能问题，是否清除session有个概率，即上面两值相除，如按默认值为1%。

	三、session_set_save_handler()函数的使用

		修改php配置文件：session.save_handler = user

		//在运行session_start()时调用
		//启动会话
		function open($save_path,$session_name){
			global $sess_save_path;
			$sess_save_path = $save_path;
			return true;
		}

		//在调用session_write_close和session_destroy时调用
		function close(){
			return true;
		}
	
		//在运行session_start()和读取数据$_SESSION时
		//读取session数据到$_SESSION中
		function read($session_id){
			global $sess_save_path;
			$sess_file=$sess_save_path."mysessfile".$session_id;

			return (string)file_get_contents($sess_file);
		}

		//结束时和session_write_close()强制提交session数据时
		function write($session_id,$sess_data){
			global $sess_save_path;
			$sess_file=$sess_save_path."mysessfile".$session_id;

			if($fp=fopen($sess_file,"w")){
				$return=fwrite($fp,$sess_data);
				fclose($fp);
				return $return;
			}else{
				return false;
			}

		}

		//调用session_destroy();时
		//
		function destroy($session_id){
			global $sess_save_path;
			$sess_file=$sess_save_path."mysessfile".$session_id;

			return unlink($sess_file);
		}

		//在session.gc.probability和session.gc_divisor值决定的，在open()、read()、session_start()时
		function gc($gc_maxlifetime){
			global $sess_save_path;
			
			foreach(glob($sess_save_path."/mysessfile_*") as $filename){
				if(filemtime($filename)+gc_maxlifetime<time()){
					unlink($filename);
				}
			}
		}

		session_set_save_handler("open","close","read","write","destroy","gc",); //参数均为回调函数

		session_start();


SESSION的高级应用将信息写入到数据库中（*******自己实现一遍******）

	给回调函数传值，当函数没有类中时，直接传函数名就可以
	当函数在类中时，需要使用数组，第一个元素为类名，第二个元素为方法名

	__class__常量代表类名


SESSION最优的应用将信息写入到Memcache中管理（*******自己实现一遍******）

	memcache中set方法设置的时间可以完成session的gc完成的而工作，因此gc直接返回true

	即使不自己实现session_set_save_handler的各回调方法，仅修改php配置文件也可实现：
		session.save_handler = memcache
		session.save_path="tcp://192.168.1.11:11211","tcp://192.168.1.12:11211"


