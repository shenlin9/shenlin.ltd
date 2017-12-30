---
title: 细说 PHP - MySQL
categories:
  - PHP
tags:
  - PHP
---

MySql架构

	c/s结构，端口3306 

	根目录下配置文件my.ini
	/bin目录：存放mysql的所有命令，windows下较少
	/data目录：mysql的数据库目录，一个子目录对应一个库，子目录下对应的文件为表

sql语句
	structured query language

	DDL,DML,DQL,DCL

	mysql -h localhost -u root -p  //-p后面输入密码时不要有空格，但建议这时不输入密码，因为这时密码时明文显示，直接回车，会提示输入密码，这时会显示为*号

	/s 查看mysql状态
	show variables;  查看系统配置变量
	show variables like 'ssh';
	show database;
	create database if not exists 库名;
		在/mysql/data目录下直接建立文件夹haha，则相当于建立了一个数据库
	drop database if exists 库名;
		在/mysql/data目录下删除文件夹haha，则相当于删除了一个数据库
	use 库名;	//选择一个库作为默认库
	show tables;
	desc 表名;	查看表结构
	create table if not exists users(id int, name char(30), age int,sex char(3));
		表的3个文件：结构、数据、索引
	drop table if exists 表名;
	insert into users values(1,'zhangsan',10,'boy');
		所有的字段按字符串处理，mysql会自动转换
	insert into users values('1','zhangsan','10','boy');
	insert into users(id,name,age) values('2','lisi','11');
	select * from users;
	update users set name='wangwu',age='21' where id='2';
	delete from users where id='2';

	? contents 查看帮助包括哪些儿内容
	? data types 上面命令中列出的均可在前面加?号进行查看
	? int

	? show
	? create table

	\c 退出多行命令

创建数据库表

	create table [if not exists] 表名(
		字段名1 列类型 [属性] [索引]
		字段名2 列类型 [属性] [索引]
		......
		字段名n 列类型 [属性] [索引]
	) [表类型] [表字符集];

	SQL语句不区分大小写，但是一个表对应x个文件名，windows不区分大小写，linux区分大小写，自己定义的名称最好都小写，SQL语句都大写。	

	列类型：

		细分都是按空间大小来区分的

		数据库的数据量大，空间分配不合理会造出很大的浪费，所以分配空间原则是能存下就可以，也因为如此数据库有很细的类型划分。

		1、数值型

			整型：
									有符号范围		无符号范围
				=================================================================================
				非常小的整型	TINYINT		1字节	-128---127		0----255
				较小的整型	    SMALLINT	2字节	-32768---32767		0----65535
				中等大小的整型	MEDIUMINT	3字节	0----16777215
				标准的整型	    INT		    4字节	-2147483648---2147483647
				大整数型	    BIGINT		8字节

			浮点型：
				float(M,D)		4字节		//M代表总位数,D代表小数位数
				double(M,D)		8字节
				decimal(M,D)		M+2字节		//定点数，以字符串形式存放

				浮点数会四舍五入
				浮点数存在误差，是近似数，不能使用==比较
				对比较敏感数据，使用定点数处理
				定点数优点是精确，但效率低于浮点数

		2、字符串型

			char(m)		默认255个字符，固定长度字符串，长度是定义的字节数，select后回删除空格，查询速度快，浪费空间，适合数据变化不大
			varchar(m)	默认255个字符，可变长度字符串，长度是内容长度+1个字节，select后保留空格，查询速度慢，节省空间
			text	变长，最大长度2^16-1，保存大数据量文本数据
				MEDIUMTEXT
				LONGTEXT
			blob	变长，最大长度2^16-1，保存二进制数据，如照片，压缩包，电影
				MEDIUMBLOB
				LONGBLOB
			ENUM	枚举，1或2字节，一次只能用一个值
			SET	集合，1,2,3,4,8字节，一次可以用多个集合的值，中间使用“,”分开即可

		3、日期型

			DATE		YYYY-MM-DD
			TIME		hh:mm:ss
			DATETIME	YYYY-MM-DD hh:mm:ss
			TIMESTAMP
			YEAR		YYYY

			创建表时最好不要使用这些时间格式，而用整型time()保存时间，比这些时间格式方便参与运算。（？？？？）

	数据字段属性
		1、unsigned	无符号，插入负值时会自动转为0
				只能用在数值型字段，即整型和浮点型
		2、zerofill	前导0，不足的位数自动用0补齐
				只能用在数值型字段，即整型和浮点型
				该字段自动应用UNSIGNED
		3、AUTO_INCREMENT	只能是整数
					数据每增加一条，字段值自动加1，删除数据后，仍从原最大值加1开始，而不是从最大值开始
					字段值不允许重复，因此定义时还需要将此列定义为key
					当插入数据为NULL 0 留空时，即自动插入值
					插入时指定了值，再插入值则从最大那个值开始加1
		4、NULL和NOT NULL
				建议：在创建表时，字段都不允许插入NULL值，因mysql的NULL转为php的值时，不一定等于''或0等

		5、default
				设置默认值


	索引
		1、主键索引
			primary key
			主要作用是确定数据库表中一条特定数据记录的位置，用来唯一标识一条记录
			最好为每张数据表定义一个主键。
			一个表只能指定一个主键
			主键的值不能为空

		2、唯一索引
			unique
			和主键索引一样，都可以防止创建重复的值
			不是为了提高数据操作速度
			每个表都可以有多个唯一索引

		3、常规索引
			最重要的技术，可以提升数据库的性能
			性能优化最先考虑的即常规索引
			可以提高查找的速度，会减慢数据列上插入、删除、修改的速度
			和表一样是独立的数据对象，可在创建表时创建，也可以单独使用

			create table table_carts(
				id int not null,
				id int not null,
				id int not null,
				id int not null,
				primary key(id),
				index cuid(uid),	//index 和 key是同义词，因此这行和下面的一行都是创建索引
				key csid(sid),
			);
			create index ind1 on users(name,age);

			drop index ind1 on users;

		4、全文索引
			fulltext类型索引，只能用在MyISAM表类型上使用
			只有在varchar、char、text文本字符串上使用
			也可以多个数据列使用
			create table books(
				id int,
				bookname varchar(30),
				price double,
				detail text not null,
				fulltext(detail,bookname),
				index ind(price),
				primary key(id)
			);

			通常sql：select * from books where bookname like '%php%';

			全文检索：select bookname,price from books where MATCH(detail) AGAINST('php');
				  select match(detail) against('php') from books;


	表类型和存储位置

		mysql和大多数据库不同，mysql有一个存储引擎的概念

		mysql可以针对不同的需求选择最优的存储引擎

		通常也把支持的存储引擎叫数据表类型

		使用show engines查看mysql支持的数据引擎，12种，缺省为MyISAM

		也可使用show variables like 'table_type';查看表类型，即引擎类型

		重要的表类型有：MyISAM 和 InnoDB

		create table() type InnoDB;
		或
		create table() engine InnoDB;

		在一个mysql库中可以有不同的表类型

		MyISAM表类型特点：
			成熟、稳定、易于管理
			OPTIMIZE TABLE 表名;  优化碎片
			强调快速读取操作

			缺点是有些儿功能不支持

		InnoDB表类型特点：

			支持一些儿MyISAM所不支持的功能

			缺点是空间占用较多，读写速度比MyISAM慢

		功能			MyISAM				InnoDB
		==================================================================
		事物处理		不支持				支持
		数据化锁定		不支持				支持
		外键约束		不支持				支持
		表空间占用		相对小				相对大，最大2倍
		全文索引		支持				不支持


		存储位置：
			MyISAM文件：
			  	.frm	存储表结构
			  	.MYD	存储数据
			  	.MYI	存储索引
			InnoDB文件
				只有.frm文件，既存表结构，又存数据和索引（？？？）

	MySQL默认字符集

		GBK	定长的字符集，是否汉字都占2字节
		UTF-8	变长的字符集，1到4个字节，每个汉字3个字节

		MySQL服务器、数据库、表、字段都可以指定字符集，36个

		显示字符集show character set;

		注意数据库中的utf-8没有-

		MySQL的字符集包括

			字符集：用来定义MySQL存储字符串的方式

			校对规则：是对规则定义了比较字符串的方式
				以_ci结尾的校对规则表示区分大小写
				以_cis结尾的校对规则表示不区分大小写
				以_bin结尾的校对规则表示进行二进制比较

			一对多的关系，一个字符集可以对应多个校对规则

			查看对应关系：show collation like '%gbk%';

		create database demo default character set gbk collate gbk_chinese_ci;
		create table t1(id int) type=InnoDB default character set gbk collate gbk_chinese_ci;
		alter database demo character set utf8;

		 客户端与服务器交互时：
			character_set_client
			character_set_connection
			character_set_results

		mysqldump -u root -p --default-character-set=gbk -d demo1 > c:\demo.sql
		mysql -u root -p demo1 < c:\demo.sql

			set names 字符集 同时修改以上三个的值
修改表

	alter table t1 add name varchar(30) not null;
	alter table t1 add age int unsigned not null default '0';
	alter table t1 add sex varchar(10) not null after name;
	alter table t1 add height double first;

	alter table t1 modify sex char(3); //modify更改类型，change改名字
	alter table t1 change name1 name2 varchar(30);
	alter table t1 rename as users;

	alter table t1 drop age;
	drop table if exists t1;


insert into 表名([字段列表]) values(值列表1),values(值列表2),values(值列表3);

truncate 表名;	

select [all | distinct]

	distinct不是针对一列数据的，是针对整个查询列表取消重复的一列，在多个字段时表示字段的组合不相同

	select distinct a,b from t1

	select version(),1.2*10;
		上述sql中的括号，*等在嵌入到php中时，可能会产生问题，要使用别名

	逻辑运算符
		&& and
		|| or
		!  not

	比较运算符
		=	--sql中的等于是一个等号
		<=>	和=作用相同，但可以用于null值判断，而=不可以，需要使用is null 判断
		!= <>
		<
		<=
		>
		>=
		is null
		is not null
		between and	包括两个边界
		not between and
		like	两个通配符：下划线_ 表示任意一个字符；%表示0个或多个字符
		not like
		in
		regexp rlike
from

where

group by 

having
	分组后的条件判断
	select cid,count(*) from t group by cid having cid > 200;

order by 

limit
	单独从句，用于限制条数
	
	select * from t order by a desc limit 0,5;

窄表 宽表 疑惑
笛卡尔连接

Mysql中的内置系统函数

	字符串函数

		concat(s1,s2.....sn) 把传入的参数连接为字符串
			concat "abc","xyz"
			select concat(name," age is",age);

		insert(str,x,y,str1) 将字符串str的x（下标从1开始）位置开始，y个长度的字符串替换为str1

		replace(str,a,b) 将字符串str的a替换为b

		lower()  upper()

		left(str,x)  right(str,x)

		lpad(str,n,pad)  rpad(str,n,pad) 用字符串pad对str最左边或最右边进行填充，直到字符串长度为n

		trim() ltrim() rtrim()

		strcmp(s1,s2) 按ascii码比较字符串，s1>s2 返回1 s1=s2 返回0 s1<s2 返回-1

		substring(str,x,y) 返回字符串中的第x位置起y个长度的字符串

	数值函数
		abs(x)
		ceil(x) 返回大于x的最小整数
		floor(x) 返回小于x的最大整数
		mod(x,y) 返回x除以y的余数
		rand() 返回0-1之间的小数
		round(x,y) 返回参数x的四舍五入值，小数长度为y位
		truncate(x,y) 返回数字x截断为y位小数的结果，相对于上面的round，不会四舍五入

	日期函数
		curdate 返回当前日期
		curtime() 返回当前时间
		now()	返回当期日期和时间
		unix_timestamp(now())	返回当前的unix时间戳
		from_unixtime(129344485) 返回时间戳对应的日期
		week(now())
		year(now())
		hour(now())
		minute(now())
		monthname(now()) 月份名字，为英文，如December
		date_format(now(),"%Y-%m-%d %H:%i:%s");

	流程控制函数

		if(value,t,f)   --value为真，返回t，否则返回f
		ifnull(value1,value2)	--不是null返回value1，是null返回value2
		case 
			when [value1] then [result1] 
			when [value2] then [result2] 
			when [value3] then [result3] 
			... 
			else [value] 
		end

	其他函数
		database	返回当前数据库名
		version		返回当前mysql版本
		user		返回当前的登录用户
		inet_aton(ip)	返回IP地址对应的网络字节
		inet_ntoa	返回网络字节对应的IP地址
		password	返回41位长的加密字符串，用于mysql系统用户的密码加密
		md5

