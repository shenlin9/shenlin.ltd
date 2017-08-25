【Linux引导流程】

    固件Firmware(CMOS/BIOS)->执行POST(Power On Self Test)加电自检->读取MBR->自举程序BootLoader(GRUB)->载入内核->内核Kernel->驱动硬件

                                                                                                                          ->启动进程init->读取并执行配置文件/etc/inittab


    init的工作：

        init启动后读取inittab文件，执行缺省运行级别，从而继续引导过程。

        在UNIX系统中，init是第一个可以存在的进程，它的PID恒为1，但它也必须向一个更高级的功能负责：PID为0的内核调度器（Kernel Scheduler），从而获得CPU时间 

    内核调度器：负责分配CPU时间和进程间切换。

    父子进程：linux中，父进程终止，子进程也必须被终止；若父进程被终止，而子进程因特殊原因没被终止，则子进程被称为孤儿进程；linux检测到孤儿进程后，会将孤儿进程的父进程指向init进程；

              若子进程被终止后，父进程没了解到此信息，还尝试和子进程取得联系，则此子进程称为僵尸进程，linux中用Z（Zombie）表示。

    MBR：Mater Boot Record 主引导记录，位于0柱面0磁头1扇区

         MBR共512个字节

             Boot Loader （446bytes） 自举程序

             Partition Table （64bytes） 分区表

                   partition1 
                   partition2 
                   partition3 
                   partition4 

             Magic Number（2bytes） 结束标志字

    固件介于软件和硬件之间

    固件的常用设置：

        安全设置，如设置BIOS密码

        可引导的介质列表

        可引导介质的搜索顺序

        电源管理

        启动细节显示

        …………

    BIOS时钟

        硬件时钟，linux中称为hardware clock，使用命令hwclock查看

        更改硬件时钟时间：hwclock --set --date="9/22/96 16:45:05"

    软件时钟

        linux系统的时钟，使用date命令查看

        更改软件时钟时间：date MMDDhhmm[[CC]YY][.ss]，格式是 月月日日时时分分年年年年.秒秒

                          date 030821192014.30

    软件时钟和硬件时钟的同步

        两个时钟同步很重要：一些儿应用程序在调用时间值时，时钟不同步则无法正常运行，会提示Time Error

                            一些儿应用程序对时间有很高的要求，如网络环境中实现同步的网络备份，这时需要网络中所有主机的时间是一致的

        同步命令：hwclock --hctosys         hardware clock to sys，将硬件时钟同步到系统

                  hwclock --systohc         sys clock to haredware，将系统时钟同步到硬件

        NTP（Network Time Protocal）服务，定期和实际服务器同步???

【Linux运行级别】

    在/etc/inittab配置文件中设置linux的运行级别，init进程将读取inittab配置文件，并将系统运行在设定的级别上。

    共7个级别：0 - halt
                    挂起（关机???用处???），不要将默认的运行级别设为此值

               1 - Single user mode
                    单用户模式，类似于windows的安全模式，只有root可以登录，用大写S表示

               2 - Multiuser,without NFS
                    多用户模式，没有NFS，如果不使用网络就和3一样

                    NFS：Network File System，网络文件系统，sun公司开发的服务，用于Unix和Linux之间的文件共享，简单但安全性差，使用较少

               3 - Full multiuser mode
                    完整的多用户模式

               4 - unused
                    没有使用，可以自己定义的运行级别，可以定义启动哪些儿服务

               5 - X11
                    X11是X Window的版本号，图形化的多用户环境，

               6 - reboot
                    重启，不要将默认的运行级别设为此值

               ???系统的默认级别

    查看当前的运行级别：runlevel

                显示N 3，N表示没有切换过运行级别???

                显示S 3，S表示切换过运行级别???

    切换运行级别：init [0123456Ss]

                  telinit [0123456Ss]

                  telinit是init的软链接

    在inittab中，所有条目采取以下格式：

        id:runlevels:action:process

            id：条目的标识符，1到4位字符

            run-levels：指定运行级别，可以指定多个，如果为空，不是表示不执行，而是表示从0到6每个运行级别都要执行

            action：指定运行状态，action常用取值：

                        initdefault：指定系统缺省启动的运行级别

                        sysinit：系统启动执行process中指定的命令

                        wait：执行process中指定的命令，并等其结束再运行其他命令

                        once：执行process中指定的命令，不等待其结束

                        ctrlaltdel：按下Ctrl+Alt+Del时执行process指定的命令

                        powerfail：当出现电源错误时执行process指定的命令，不等待其结束

                        powerokwait；当电源恢复时执行process指定的命令

                        respawn：一旦process指定的命令中止，便重新运行该命令

            process：指定要运行的程序的完整路径


        比较特殊的一行，定义了系统启动时的默认运行级别：

            id:5:initdefault:


【Linux启动服务管理】


    启动脚本 /etc/rc.d/rc.sysinit

        /etc/inittab中为si::sysinit:/etc/rc.d/rc.sysinit        

        可见运行状态为空，说明无论哪种运行级别，都会执行rc.sysinit，如自己有需要每次系统启动时都执行的脚本，可以附加在此文件内容后

        完成系统服务程序启动，如系统环境变量设置，设置系统时钟，加载字体，检查加载文件系统，生成系统启动信息日志文件等

    启动脚本 /etc/rc.d/rc

        判断系统的运行级别，启动对应运行级别目录中的服务程序，完成相应运行级别的初始化设置

    启动脚本 /etc/rc.d/rc[0123456].d        /etc/rc[0123456].d是其软链接，是为了和Unix的良好兼容

        分别存放对应于运行级别的服务程序脚本的符号链接，链接到init.d目录中的相应脚本，ls -l /etc/rc.d/rc3.d执行结果举例：

            K35smb -> ../init.d/smb
            S55sshd - > ../init.d/sshd

        文件格式分为3个部分：

            开头字母S或K：S开头的表示在此运行级别要Start启动，K开头的表示Kill关闭，注意都是大写。如S改为小写，则不启动，可利用此特点关闭自己不需要的服务，则有此规律，看到小写s开头的就可以了解到某服务缺省是随系统启动的，自己随后调整为了不启动

            中间的数字35,55表示启动/关闭的顺序，数字越小越优先启动

            最后是脚本的名称


    启动总结：

    firmware-->BootLoader(位于MBR中)-->启动init进程-->读取/etc/inittab配置文件-->判断系统的缺省运行级别initDefault-->执行脚本/etc/rc.d/rc.sysinit（始终执行，和运行级别无关）-->执行脚本/etc/rc.d/rc，判断initDefault-->根据运行级别启动对应目录/etc/rc.d/rc[0123456].d下的以S开头的脚本-->输入usrename,password


    tty

        ctrl+alt+[F1--F6]可启动对应的6个终端

        ctrl+alt+F7 回到X Window

    /etc/rc.d/init.d        /etc/init.d是此目录的软链接目录

        该目录下包含各个运行级别的服务程序脚本

        linux安装的服务的启动脚本都位于此目录

        直接运行此目录下的脚本，会弹出使用方法的提示信息，可以从这里手工启动和关闭服务：

            /etc/rc.d/init.d/sshd

            Usage：/etc/rc.d/init.d/sshd {start|stop|restart|reload|condrestart|status}

                reload  表示重新读取服务的配置文件，不需要重启服务

                condrestart 首先检测服务是否在运行，在运行则重启服务，没运行则不做任何操作

    设置自启动程序

        ln -s

            做一个启动服务脚本文件的软链接，放到对应的启动级别的目录下，如

            ln -s /etc/rc.d/init.d/msg.script /etc/rc.d/rc3.d/S100msg.script

        chkconfig

            chkconfig --list

                显示系统中已安装的服务和缺省的状态

                chkconfig --list httpd  只显示httpd服务的信息

            chkconfig --levels servicename on/off
            chkconfig --level servicename on/off

                将名称为servicename的服务在运行级别levels上设为on/off，一个运行级别使用选项--level，多个使用--levels

                chkconfig --levels 2345 httpd on

        ntsysv

            ntsysv --level 3

            设定运行级别3的启动服务，图形界面

        dmesg

            检查引导期间的错误

            dmesg | grep hda

            dmesg | grep eth0

                如果没有找到任何信息，说明内核没能驱动此网卡

        系统日志 /var/log/messages

            grep syslogd /var/log/messages

                检查系统日志，查找可能被dmesg忽略的应用程序错误

            /var/log

                此目录存放的是日志文件


【GRUB配置与应用】

    
    GRUB：the GRand Unified Bootloader

    GRUB的配置文件默认为/boot/grub/grub.conf    /etc/grub.conf为此配置文件的软连接

    配置选项：

        default
    
            定义缺省的启动系统，0表示第1个，1表示第2个，启动系统的列表在title菜单项名称中可看见

        timeout

            定义缺省等待时间

        splashimage

            定义GRUB界面图片，最好640x480的分辨率，色深14，因此时显卡驱动未加载，高分辨率的图片会导致背景漆黑一片

            位于/boot/grub/splash.xpm.gz

        hiddenmenu

            隐藏菜单，删除此选项则显示菜单项

        title

            定义菜单项名称
        
        root

            设置GRUB的根设备即内核所在的分区

        kernel

            定义内核文件所在位置

        initrd

            命令加载镜像文件

    GRUB命令：

        e：编辑当前的启动菜单项

        c：进入GRUB的命令行模式

        b：启动当前的菜单项

        d：删除当前行

        esc：返回GRUB启动菜单界面，取消对当前菜单项所做的任何修改


【启动故障分析与解决】


    应用1：root忘记密码，进入单用户模式重置密码：

            开机进入GRUB界面，按e进入编辑行模式

                            ，选中kernel行，再次按e

                            ，在新的界面的最后一行的最后位置输入空格加运行级别的编号，即可进入对应的运行级别，如空格1（或空格s）表示要进入单用户模式

                            ，回车后按b键即启动进入设置好的运行级别

                            ，进入单用户模式是不需要任何秘密的，进入系统后passwd root，也不需要旧密码即可更改

    应用2：设置GRUB密码，避免任何人可进入单用户模式：

        1、使用GRUB自带的grub-md5-crypt 或者 在GRUB交互命令行界面中使用md5crypt得到MD5加密的密码字符串，如xxx

        2、进入GRUB配置文件/boot/grub/grub.conf，在title上面增加一行：password --md5 xxxxxxxxxx （xxx为上面使用命令得到的密码）

        3、重启进入GRUB时，不再是按e进行编辑，而是需要先按p键键入密码

    应用3：配置文件grub.conf设置错误，修复GRUB

        当开机后进入grub界面但没有菜单，只剩下一个grub>提示符，解决办法：

        1、查看grub里设置的参数，找出错误：

            grub>cat /grub/grub.conf

        2、确认错误后，将root,kernel,initrd 3个命令使用正确的方式执行一遍：

            grub>root(hd0,6)

            grub>kernel(hd0,6)/vmlinuz-2.4.18-14 ro root=LABEL=/

            grub>initrd(hd0,6)/initrd-2.4.18-14.img

        3、启动

            grub>boot

    应用4：inittab文件损坏或丢失，进入linux rescue模式

            1、BIOS中设为光盘启动，把Linux安装盘放到光驱，使用光盘引导。

            2、出现安装界面后，根据提示按F5键进入linux rescue模式

            3、在boot下输入linux rescue，并回车

            4、使用备份的inittab.bak修复

