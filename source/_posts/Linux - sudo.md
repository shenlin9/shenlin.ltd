在使用Linux系统过程中，通常情况下，我们都会使用普通用户进行日常操作，
而root用户只有在权限分配及系统设置时才会使用，而root用户的密码也不可能公开。

普通用户执行到系统程序时，需要临时提升权限，sudo就是我们常用的命令，仅需要输入当前用户密码，便可以完成权限的临时提升。

在使用sudo命令的过程中，我们经常会遇到当前用户不在sudoers文件中的提示信息，
如果解决该问题呢？通过下面几个步骤，可以很简单的解决

[ssy@localhost]$ sudo ls
提示
ssy user not in sudoers file


1、切换到root用户权限
    $ su root
2、查看/etc/sudoers文件权限，如果只读权限，修改为可写权限
    # ls -l /etc/sudoers
    -r--r-----. 1 root root 4030 9月  25 00:57 /etc/sudoers
    # chmod u+w /etc/sudoers
    # ls -l /etc/sudoers
    -rw-r-----. 1 root root 4030 9月  25 00:57 /etc/sudoers
3、执行vi命令，编辑/etc/sudoers文件，添加要提升权限的用户；在文件中找到root  ALL=(ALL) ALL，在该行下添加提升权限的用户信息，如：
    root    ALL=(ALL)       ALL
    ssy     ALL=(ALL)       ALL
    说明：格式为（用户名    网络中的主机=（执行命令的目标用户）    执行的命令范围）
4、保存退出，并恢复/etc/sudoers的访问权限为440
    # chmod u-w /etc/sudoers
    # ls -l /etc/sudoers
    -r--r-----. 1 root root 4030 9月  25 00:57 /etc/sudoers
5、切换到普通用户，测试用户权限提升功能

！！不用修改sudoers文件的权限，切换到root用户后，可直接使用:w!强制写入更改
