文件 /etc/sysconfig/network-scripts/ifcfg-eth0存放的是网络接口（网卡）的脚本文件（控制文件），ifcfg-eth0是默认的第一个网络接口，如果机器中有多网络接口，那么名字就将依此类推ifcfg-eth1,ifcfg-eth2,ifcfg-eth3......（这里面的文件是相当重要的，涉及到网络能否正常工作）。

====设定形式：设定值=值====

设定项目项目如下：

  *DEVICE        接口名（设备,网卡）

  *USERCTL      [yes|no]（非root用户是否可以控制该设备）

  *BOOTPROTO    IP的配置方法[none|static|bootp|dhcp]（引导时不使用协议|静态分配IP|BOOTP协议|DHCP协议）

  *HWADDR        MAC地址   

  *ONBOOT        系统启动的时候网络接口是否有效，是否启动这个网卡 （yes/no）   

  *TYPE        网络类型（通常是Ethernet）   

  *NETMASK        网络掩码   

  *IPADDR        IP地址   

  *IPV6INIT    IPV6是否有效（yes/no）   

  *GATEWAY        默认网关IP地址

  *BROADCAST     广播地址

  *NETWORK  网络地址

====可参照下面的例子====

  *DEVICE=eth0        

  *BOOTPROTO=static        

  *BROADCAST=192.168.1.255        

  *HWADDR=00:0C:2x:6x:0x:xx        

  *IPADDR=192.168.1.23        

  *NETMASK=255.255.255.0        

  *NETWORK=192.168.1.0        

  *ONBOOT=yes        

  *TYPE=Ethernet

