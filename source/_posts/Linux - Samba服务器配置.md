NetBios 和 SMB不可路由，即不能用在互联网上

nmbd进程：浏览共享即查看linux服务器共享的文件
	  计算机名称解析即通过linux服务器的名称而不是IP来访问

#注释的是说明信息
;注释的是选项

security=指定安全模式
	1、share  	无权限验证
	2、user		缺省的，推荐使用，由linux的Samba服务器做验证
	3、server	第三方主机验证，可以是windows或linux，很少使用
	4、domain	第三方主机验证，第三方必须是windows域控制器，很少使用

hosts allow=允许的主机
hosts denny=限制的主机
一般服务都是禁止优先，但Samba允许优先

网段、IP地址、域名

注意网段192.168.1.0，samba中写为192.168.1.

log file = 日志文件路径

	%m指的是进程名，即smbd和nmbd

服务基本限定就两种：
1、主机限定
2、用户限定


homes段

browseable = no 无权限访问的目录不可见，即只能看到自己的宿主目录
writable = no 只读 yes 可读可写
	也可写为writeable

Linux两种防火墙；
1、Netfilter/Iptables	#iptables -F
2、SELinux		#setsebool -P use_samba_home_dirs on
				      samba_enable_home_dirs 

			 gesebool -a | grep samba

			或关闭SELinux
			 /etc/selinxu/config SELINUX = disabled

iptables -L

用户必须是系统用户
	apache中可添加虚拟用户，即为服务添加的用户，系统中不存在

设置samba验证密码
	smbpasswd -a 用户名
		-a表示增加密码，没有-a表示修改密码

启动samba

确定smaba进程是否存在

samba查看访问的客户端信息
smbstatus

查看日志信息
cat /var/log/samba


windows建立连接后会记录会话，
net use

删除所有连接
net use * /delete /y

SSH Secure Shell Client
SSH Secure File Transfer
SecureCRT

-------------------------------------------------------

1、chcon -t samba_share_t /software
2、vi /etc/samba/smb.conf
	写在配置文件末尾

	[共享名]
	path = 共享目录，只能是一个共享目录，不能写多个，也不能写文件名
	valid users = 指定访问共享目录的用户，可指定多个，用空格分隔，不写表示所有用户
	writable = 权限
3、重启服务
	/etc/rc.d/init.d/smb restart

用户是否有写权限，需要：
1、samba是否授予写权限
2、用户在linux系统中是否对共享目录有写权限

samba对于写错的选项将忽略，认为没有设置此选项，可以使用下面的命令检测语法错误
testparm
