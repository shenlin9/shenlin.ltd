---
title: 细说 PHP - PHP 中使用 memcached
categories:
  - PHP
tags:
  - PHP
---

memcached的安装及管理

	概述
		是一个高性能的分布式的内存对象缓存系统，通过在内存里维护一个巨大的hash表
		hash表是一个键值对表
		是一个c/s架构软件？？？？
		150k的开源软件，使用c语言实现
		默认端口是11211

	工作原理
		Memcache	软件
		Memcached	服务进程

		Memcached是以守护进程方式运行于一个或多个服务器中，随时接收客户端的连接和操作
		客户端可使用各种语言编写，如PHP、java等
		
	linux下安装
		memcache使用到libevent库，需要先安装此库
			./configure-with-libevent=/usr
		安装memcache
			./config-with-libevent=/usr
			make && make install
			启动
				memcached -d -m 128 -l 192.168.1.11 -p 11211 -u root
			停止
				kill cat /tmp/memcached.pid
				或
				killall memcached

	windows下安装

		memcached.exe -d install

		memcached.exe -d uninstall

		memcached.exe -d start
		
		memcached.exe -d -m 50 -l 127.0.0.1 -p 11211 start
	操作
		telnet localhost 80	---apache
		telnet localhost 21	---ftpd
		telnet localhost 22	---ssh
		telnet localhost 11211	---memcached

	命令格式
		[add | set]	键  标志位  有效时间 长度 \n值
		[get | delete]  键
		flush_all	//清空所有

	遍历
		stats items
		stats cachedump 1 1
		stats cachedump 1 0


php中使用memcached

	支持过程和对象两种使用方式

	安装memcached扩展
		windows系统
			将php扩展文件php_memcache.dll复制到php扩展目录ext
			在php配置文件中设置memcache扩展：extension=php_memcache.dll
			重启apache服务器
		linux系统
			？？？？

	使用
		$mem=new Memcache;
		$mem->connect("localhost",11211);
		$mem->add("mystr","this is a test",MEMCACHE_COMPRESSED,3600);
		$mem->set("mystr","this is a test",MEMCACHE_COMPRESSED,3600);
		$mem->replace("mystr","this is a test",MEMCACHE_COMPRESSED,3600);
		echo $mem->get("mystr");
	
		$mem->add("myarr",array("aaa","bbb","ccc"));
		print_r($mem->get("myarr"));

		$mem->add("myobj",new PDOException);
		var_dump($mem->get("myobj"));

		echo $mem->getVersion();

		$mem->delete("mystr");
		$mem->flush();

		echo "<pre>";
		print_r($mem->getStats());
		echo "</pre>";

		$mem->addServer("www.ssl.com",11221);
		$mem->addServer("192.168.1.11",11211);
		
		$mem->close();

	在php什么地方使用memcache
		1、缓存数据库select出的数据
		2、在会话控制Session中使用

	key值注意：
		1、同一个项目安装两次，key要有不同的前缀
		2、同一个sql语句可能在不同页面重复使用，可使用sql的md5值做为key，或截取部分md5值作为key

	安全注意：
		1、内网访问
		2、设置防火墙
			iptables -A INPUT -p tcp -s 192.168.1.111 -dport 11211 -j ACCEPT
			iptables -A INPUT -p udp -s 192.168.1.111 -dport 11211 -j ACCEPT


