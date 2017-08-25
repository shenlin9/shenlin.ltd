查询是否安装ssh
rpm -qa | grep ssh

重启SSH服务
service sshd restart

查看是否启动22端口
netstat -antp | grep sshd

设置ssh服务开机启动
chkconfig sshd on 


chkconfig
查询或设置系统服务的运行级别信息
实际操作的是这些儿文件夹：/etc/rc[0-6].d


字体名字前有@的是横向文字