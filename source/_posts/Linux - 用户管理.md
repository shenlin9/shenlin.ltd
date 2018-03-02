---
title: Linux - 用户管理
categories:
  - Linux
tags:
  - Linux
---

Linux 用户管理

<!--more-->

## 用户管理配置文件

【配置文件】

    用户信息文件：/etc/passwd

    密码文件：/etc/shadow


    用户组文件：/etc/group

    用户组密码文件：/etc/gshadow


    新用户的默认配置：

        用户配置文件：/etc/login.defs

                        PASS_MIN_LEN    密码长度要求
                        
                        UID_MIN         最小UID
                        UID_MAX         最大UID

                        CREATE_HOME     是否创建宿主目录

                      /etc/default/useradd

                        GROUP=100
                        HOME=/home
                        INACTIVE=-1         是否激活
                        EXPIRE=             过期时间
                        SHELL=/bin/bash
                        SKEL=/etc/skel
                        CREATE_MAIL_SPOOL=yes

        模板配置文件：位于/etc/skel目录下，此目录下的都是隐藏文件，新添加的用户会从这里复制文件到新用户的宿主目录下

    登录信息：/etc/motd

                    message of the day

                    成功登录之后显示的信息

              /etc/issue

                    用户登录之前显示的提示信息，默认为系统的版本和硬件平台

【/etc/passwd文件格式】

    每一行表示一个用户，有多少行就表示有多少个用户：

        $cat /etc/passwd

        root:x:0:0:root:/root:/bin/bash
        bin:x:1:1:bin:/bin:/sbin/nologin
        daemon:x:2:2:daemon:/sbin:/sbin/nologin
        adm:x:3:4:adm:/var/adm:/sbin/nologin
        lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
        sync:x:5:0:sync:/sbin:/bin/sync
        shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
        halt:x:7:0:halt:/sbin:/sbin/halt
        mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
        uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin
        operator:x:11:0:operator:/root:/sbin/nologin
        games:x:12:100:games:/usr/games:/sbin/nologin
        gopher:x:13:30:gopher:/var/gopher:/sbin/nologin
        ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
        nobody:x:99:99:Nobody:/:/sbin/nologin
        vcsa:x:69:69:virtual console memory owner:/dev:/sbin/nologin
        saslauth:x:499:76:Saslauthd user:/var/empty/saslauth:/sbin/nologin
        postfix:x:89:89::/var/spool/postfix:/sbin/nologin
        sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
        ssy:x:500:500::/home/ssy:/bin/bash
        mysql:x:27:27:MySQL Server:/var/lib/mysql:/bin/false

    使用冒号分隔，7个部分，各部分依次是：

        用户名      用户登录系统时使用的用户名

        密码        密码位，注意不是密码

        UID         用户标识号

        GID         缺省组标识号，一个用户可以隶属于多个组，这里的是缺省的组

        描述信息    例如存放用户全名，所属部门，项目组等信息

        宿主目录    用户登录系统后的缺省目录

        Shell       用户使用的Shell，默认为bash，写错了用户将无法登录系统

    为什么有密码位？

        因/etc/passwd中保存了用户信息，很多命令需要读取这些信息，因此权限设置是-rw-r--r--，即所有人都有r权限，若将密码保存在这里，任何人都可以尝试破解密码，安全性不好。

        早期的UNIX就是在密码位保存密码的。

统计行数

    wc -l /etc/passwd

【DES和MD5】

    DES

        老版本的UNIX使用DES加密，DES只加密前8位

    MD5

        输入长度不固定，输出长度固定

        单向不可逆，没法通过输出得到输入

【用户类型】

    Linux用户分为三种：

        超级用户    （root,UID=0）

            不能说root就是超级用户，而是UID为0的用户就是超级用户，其权限和root一样。

        伪用户      （UID 1-499）

        普通用户    （UID 500-60000）

【伪用户】

    1、伪用户与系统和程序服务相关

        bin、daemon、shutdown、halt等，任何Linux系统默认都有这些伪用户

        mail、news、games、apache、ftp、mysql和sshd等，与Linux系统的进程相关
    
    2、伪用户通常不需要或无法登录系统

    3、可以没有宿主目录

【用户组】

    每个用户都至少属于一个用户组

    每个用户组可以包括多个用户

    同一用户组的用户享有该组共有的权限

    windows有嵌套组，linux没有

【/etc/shadow文件格式】

    root:$6$F7yJ4iBQ$oEGgf0eA2zkGoyx3Ac0ChoJ7HuDiRq5td9rXsGGGybHMNN15uBh8OETOPVRlNDqnkRBWwlcqQT7n5wor0d5b21:17306:0:99999:7:::
    bin:*:17246:0:99999:7:::
    daemon:*:17246:0:99999:7:::
    adm:*:17246:0:99999:7:::
    lp:*:17246:0:99999:7:::
    sync:*:17246:0:99999:7:::
    shutdown:*:17246:0:99999:7:::
    halt:*:17246:0:99999:7:::
    mail:*:17246:0:99999:7:::
    uucp:*:17246:0:99999:7:::
    operator:*:17246:0:99999:7:::
    games:*:17246:0:99999:7:::
    gopher:*:17246:0:99999:7:::
    ftp:*:17246:0:99999:7:::
    nobody:*:17246:0:99999:7:::
    vcsa:!!:17306::::::
    saslauth:!!:17306::::::
    postfix:!!:17306::::::
    sshd:!!:17306::::::
    ssy:$6$DYQuZnbf$MqHan9/bBE0mCkhfxWivu1JeEWYv.NgUaWXQXL/ccBIO7Mkjb2flsv6uCUOAbrlQtfbAISo4gl.EQA0Gohl5m0:17306:0:99999:7:::
    mysql:!!:17308::::::

    使用冒号分隔，9个部分，各部分依次是：

        用户名                  用户登录系统时使用的用户名

        密码                    加密密码

        最后一次修改时间        用户最后一次修改密码的天数，以linux的诞生日1970.1.1为基准

        最小时间间隔            两次修改密码之间的最小天数，默认0表示不限定

        最大时间间隔            密码保持有效的最大天数，默认为99999天

        警告时间                从系统开始警告到密码失效的天数，默认7天

        账号闲置时间            账号闲置时间，可以利用提取用户多久未登录

        失效时间                密码失效的绝对天数

        标志                    一般不使用

    /etc/shadow文件中的密码被删除，则用户不需要输入密码即可登录系统

    /etc/shadow文件权限：-r--------，即只有root有r权限，比较安全

【/etc/passwd和/etc/shadow中的密码转换】

    linux中设置密码，都是先将密码写入/etc/passwd中的密码位，然后使用pwconv命令将密码再转入到/etc/shadow文件，系统自动完成此过程

    使用pwunconv命令，可将命令转回来，即从/etc/shadow文件转回/etc/passwd文件

    pwconv  即  password convert

【/etc/group文件格式】

    root:x:0:
    bin:x:1:bin,daemon
    daemon:x:2:bin,daemon
    sys:x:3:bin,adm
    adm:x:4:adm,daemon
    tty:x:5:
    disk:x:6:
    lp:x:7:daemon
    mem:x:8:
    kmem:x:9:
    wheel:x:10:
    mail:x:12:mail,postfix
    uucp:x:14:
    man:x:15:
    games:x:20:
    gopher:x:30:
    video:x:39:
    dip:x:40:
    ftp:x:50:
    lock:x:54:
    audio:x:63:
    nobody:x:99:
    users:x:100:
    floppy:x:19:
    vcsa:x:69:
    utmp:x:22:
    utempter:x:35:
    cdrom:x:11:
    tape:x:33:
    dialout:x:18:
    saslauth:x:76:
    postdrop:x:90:
    postfix:x:89:
    fuse:x:499:
    sshd:x:74:
    ssy:x:500:
    mysql:x:27:

    使用冒号分隔，4个部分，各部分依次是：

        组名                    用户登录时所在的组

        组密码                  一般不使用

        GID                     组标识号

        组内用户列表            属于该组的所有用户列表

【/etc/gshadow文件格式】

    root:::
    bin:::bin,daemon
    daemon:::bin,daemon
    sys:::bin,adm
    adm:::adm,daemon
    tty:::
    disk:::
    lp:::daemon
    mem:::
    kmem:::
    wheel:::
    mail:::mail,postfix
    uucp:::
    man:::
    games:::
    gopher:::
    video:::
    dip:::
    ftp:::
    lock:::
    audio:::
    nobody:::
    users:::
    floppy:!::
    vcsa:!::
    utmp:!::
    utempter:!::
    cdrom:!::
    tape:!::
    dialout:!::
    saslauth:!::
    postdrop:!::
    postfix:!::
    fuse:!::
    sshd:!::
    ssy:!::
    mysql:!::

    使用冒号分隔，4个部分，各部分依次是：

        组名                    用户登录时所在的组


【手工添加用户】

    1、分别在/etc/passwd、/etc/group和/etc/shadow文件中添加一笔记录

    2、创建用户宿主目录，并更改目录的所有者为对应用户

    3、在用户宿主目录中设置默认的配置文件：从/etc/skel目录下复制配置文件

    4、设置用户初始密码


## 用户管理命令


【SetUID】


【】



【】


## 用户组管理命令


【】


【】



【】


## 批量添加用户


【】


【】



【】


## 用户授权


【】


【】



【】


