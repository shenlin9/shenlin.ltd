---
title: bash - linux shell 脚本攻略 - chapter 3 - 文件
categories: 
  - Linux
  - Bash
tags: 
  - 
---

shell 文件操作，Unix 将操作系统的一切都视为文件。

<!--more-->

## 生成任意大小文件

有时为了测试程序效率等，可能需要生成一个包含随机数据的大文件。

dd 命令可以创建特定大小的文件，
```bash
-> dd if=/dev/zero of=junk.data bs=1M count=1
记录了1+0 的读入
记录了1+0 的写出
1048576字节(1.0 MB)已复制，0.0910078 秒，11.5 MB/秒
```
* if 表示 input file，输入文件，不指定 if 参数，则默认从 stdin 读入
* of 表示 output file，输出文件，不指定 of 参数，则默认从 stdout 输出
* bs 表示 block size，块大小
* count 表示复制的块数
* /dev/zero 是一个字符设备，它会不断返回0值字节（ \0 ）
* dd 命令运行在底层，参数错误可能会把磁盘清空或损坏数据。
* 所以 dd 的语法一定要检查清楚，尤其是 of 参数
* 可以用 dd 命令传输大量数据并观察命令输出来测量内存的操作速度，如上例中的 11.5 MB/秒

块大小的计量单位
* 字节（1B）  c
* 字（2B）  w
* 块（512B）  b
* 千字节（1024B）  k
* 兆字节（1024KB）  M
* 吉字节（1024MB）  G

## 文本文件的交集、差集

```bash
-> cat test1.txt
aaa
bbb
ccc

-> cat test2.txt
ccc
ddd
eee
```

比较两个文件差异
```bash
-> comm test1.txt test2.txt
aaa
bbb
		ccc
	ddd
	eee
```
* 必须是排序过的文本行
* comm 输出共3列，列之间用制表符作为定界符
    * 第1列是只在第一个文件里的行
    * 第2列是只在第二个文件里的行
    * 第3列是两个文件共有的行

删除第1列、第2列显示两个文件相同的行，即交集
```bash
-> comm test1.txt test2.txt -1 -2
ccc
```

删除第1列、第3列显示第2个文件的差集（第2个文件有而其他文件没有的行）
```bash
-> comm test1.txt test2.txt -1 -3
ddd
eee
```

删除第2列、第3列显示第1个文件的差集（第1个文件有而其他文件没有的行）
```bash
-> comm test1.txt test2.txt -2 -3
aaa
bbb
```

删除第3列显示两个文件的不同行，即求差
```bash
-> comm test1.txt test2.txt -3
aaa
bbb
	ddd
	eee
```

删除空白保持整齐
```bash
-> comm test1.txt test2.txt -3 | sed 's/^\t//'
aaa
bbb
ddd
eee
```

## 查找、删除重复文件

校验和是根据文件内容生成的，因此可以根据校验和删除重复文件。

??? 看懂下面的 shell

```bash
ls -lS --time-style=long-iso | awk 'BEGIN {
    getline; getline;
    name1=$8; size=$5
}

{
    name2=$8;
    if (size==$5)
    {
        "md5sum "name1 | getline; csum1=$1;
        "md5sum "name2 | getline; csum2=$1;
        if ( csum1==csum2 )
        {
            print name1; print name2
        }
    };
    size=$5; name1=name2;
}' | sort -u > duplicate_files

cat duplicate_files | xargs -I {} md5sum {} | sort | uniq -w 32 | awk '{ print "^"$2"$" }' | sort -u > duplicate_sample

echo Removing..

comm duplicate_files duplicate_sample -2 -3 | tee /dev/stderr | xargs rm

echo Removed duplicates files successfully.
```

## 文件权限、所有权和粘滞位

```bash
-rw-rw-r--.  1 ssy ssy       16 10月  3 05:56 out.txt
drwxr-xr-x.  9 ssy ssy    12288 5月  22 10:05 pcre-8.40
```
文件类型：
* - 普通文件
* d 目录
* c 字符设备
* b 块设备
* l 符号链接
* s 套接字
* p 管道

用户类型：
* user      文件的所有者
* group     多个用户的集合
* others    除了上面两种人之外的其他人

权限类型对目录、文件的含义：
* r
    * 文件：
    * 目录：
* w
    * 文件：
    * 目录：
* x
    * 文件：
    * 目录：

权限类型和八进制数对应关系：
* r--   4
* -w-   2
* --x   1

权限类型和用户类型对应关系：
* rwx------，rwS------
    * S setuid，允许用户以文件拥有者的权限来执行
* ---rwx---，---rwS---
    * S setgid，允许用户以用户组的权限来执行
* ------rwx，------rwt，------rwT
    * t sticky bit，粘滞位，只针对目录，只有创建了此目录的用户才能删除目录中的文件
    * T 没有执行权限只有粘滞位使用小写 T，有执行权限和粘滞位使用大写 t
        * 目录设置了粘滞位的典型例子 /tmp
        ```bash
        -> ll -d /tmp
        drwxrwxrwt. 5 root root 12288 10月  5 09:31 /tmp
        ```

设置权限
```bash
-> chmod u=rwx,g=rx,o= testproject/

-> chmod u+x,g-w,o+r testproject/

-> chmod a-x testproject/           # a 表示 all，即用户、用户组和其他人

-> chmod o+t testproject/           # 设置粘滞位，只能用小写 t，不能用大写 T

-> chmod a+t testproject/           # 可以使用 u+t, g+t, o+t, a+t ，但只有后两种起作用，因为粘滞位是在其他人的权限上

-> ll -d testproject/
drwxr----T. 4 ssy ssy 4096 10月  4 18:47 testproject/

-> chmod o+x testproject/           # 可见只有粘滞位是大写 T，既有执行权限又有粘滞位是小写 t

-> ll -d testproject/
drwxr----t. 4 ssy ssy 4096 10月  4 18:47 testproject/

-> chmod 742 testproject/

-> chmod -R 777 testproject/        # -R 递归设置权限，包括了子目录和文件的权限     ??? 出错
chmod: 更改"testproject/subdir" 的权限: 不允许的操作
chmod: 更改"testproject/subdir/3.txt" 的权限: 不允许的操作
```

设置 setuid
```bash
# 将文件的所有权替换为需要执行该文件的用户
$ chown root.root executable_file

# 然后以该用户的身份登录
$ su root

# 增加 setuid 权限
$ chmod +s executable_file

# 切换会原用户
$ su ssy

# 再执行就是以文件所有者的身份执行了
$ ./executable_file
```
* setuid 权限需要文件所有者才能设置???
* setuid 的使用不是无限制的。为了确保安全，它只能应用在 Linux ELF 格式二进制文件上，而不能用于脚本文件。 


更改所有者和用户组???
```bash
-> chown ssl testmod
chown: 正在更改"testmod" 的所有者: 不允许的操作

-> chgrp ssl testmod
chgrp: 正在更改"testmod" 的所属组: 不允许的操作

-> chown ssl:ssl testmod
chown: 正在更改"testmod" 的所有者: 不允许的操作
```

只给目录一种权限：
* r
    * 只可以 ll 目录下的文件名和子目录名，但仅仅显示名字，不显示类型，日期，权限等各种信息
    * 无权限直接对目录下的具体文件 ll
    * 无权限 cat 目录下文件内容
    * 无权限 cd 进入目录
    * 无权限创建子目录
    * 无权限创建文件
    * 无权限删除子目录
    * 无权限删除文件
* w
    * 无权限 cd 进入目录
    * 无权限 ll 目录
    * 无权限直接对具体的文件 ll
    * 无权限 cat 文件内容
    * 无权限创建子目录
    * 无权限创建文件
    * 无权限删除子目录
    * 无权限删除文件
* x
    * 可以 cd 进入目录
    * 无权限 ll 目录
    * 可以直接对目录下的具体文件 ll
    * 可以 cat 目录下的文件内容
    * 无权限创建子目录
    * 可以创建文件
    * 无权限删除子目录
    * 无权限删除文件

## 创建不可修改文件

chattr 为文件添加写保护后就无法删除

```bash
-> chattr +i 1.txt
chattr: Operation not permitted wh_ile setting flags on 1.txt

-> sudo chattr +i 1.txt
[sudo] password for ssy: 

-> ll 1.txt
-rwxrwxrwx. 1 ssy ssy 86 Oct  4 10:30 1.txt

-> rm 1.txt
rm: remove write-protected regular file `1.txt'? y
rm: cannot remove `1.txt': Operation not permitted

-> sudo chattr -i 1.txt

-> rm 1.txt
```

## 批量创建文件

```bash
-> cat test.sh
for name in test{1..5}.c
do
    echo $name
    touch $name
done

test1.c
test2.c
test3.c
test4.c
test5.c
```

当创建的文件已存在时，touch 命令会把文件的所有时间戳都更改为当前时间
```bash
-> ll s1.txt
-rwxrwxrwx. 1 ssy ssy 86 Oct  4 10:38 s1.txt

-> touch s1.txt

-> ll s1.txt
-rwxrwxrwx. 1 ssy ssy 86 Oct  5 13:51 s1.txt
```

只更改访问时间或文件内容修改时间
```bash
# 只更改文件访问时间
-> touch -a s1.txt

# 只更改文件内容修改时间
-> touch -m s1.txt
```

指定文件的时间戳
```bash
-> touch -d "2016-03-10 18:00:00" s1.txt

-> ll s1.txt
-rwxrwxrwx. 1 ssy ssy 86 Mar 10  2016 s1.txt
```

## 符号链接

符号链接相当于 windows 的快捷方式，删除符号链接不影响原文件。

创建符号链接
```bash
-> ln -s s1.txt link.txt

-> ll s1.txt link.txt
-rwxrwxrwx. 1 ssy ssy 86 Mar 10  2016 s1.txt
lrwxrwxrwx. 1 ssy ssy  6 Oct  5 14:04 link.txt -> s1.txt
```

输出符号链接指向的原文件
```bash
-> readlink link.txt
s1.txt
```

列表目录下的符号链接
```bash
-> ls -l /bin | grep "^l"
lrwxrwxrwx. 1 root root      4 May 20 08:55 awk -> gawk
lrwxrwxrwx. 1 root root      8 May 20 08:56 dnsdomainname -> hostname
lrwxrwxrwx. 1 root root      8 May 20 08:56 domainname -> hostname
lrwxrwxrwx. 1 root root      4 May 20 08:55 egrep -> grep

-> find /bin -type l -print0 | xargs -0 -I {} ls -l {}
lrwxrwxrwx. 1 root root 4 May 20 08:55 /bin/awk -> gawk
lrwxrwxrwx. 1 root root 8 May 20 08:56 /bin/dnsdomainname -> hostname
lrwxrwxrwx. 1 root root 8 May 20 08:56 /bin/domainname -> hostname
lrwxrwxrwx. 1 root root 4 May 20 08:55 /bin/egrep -> grep
```

## 统计文件类型

不同于 Windows 系统，Unix/Linux 系统的文件类型并不是由文件的扩展名确定的

可使用 file 命令查看文件类型
```bash
-> file s1.txt
s1.txt: ASCII text

-> file link.txt
link.txt: symbolic link to 's1.txt'

-> file test.sh
test.sh: Bourne-Again shell script text executable

-> file subdir
subdir: directory

-> file 1.txt.gpg
1.txt.gpg: data

-> file junk.data
junk.data: very short file (no magic)

-> file /bin/bash
/bin/bash: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.18, stripped
```
* 文件类型信息包含较多的细节，由逗号分隔

输出时不带文件名，直接输出类型
```bash
-> file -b test.sh
Bourne-Again shell script text executable
```

统计目录下个文件类型数量的脚本
```bash
-> cat filestat.sh
if [ $# -ne 1 ];
then
    echo "Usage is $0 basepath";
    exit
fi

path=$1
declare -A statarray;
while read line;
do
    ftype=`file -b "$line" | cut -d, -f1`
    let statarray["$ftype"]++;
done < <(find $path -type f -print)

echo ============ File types and counts =============
for ftype in "${!statarray[@]}";
    do
    echo $ftype : ${statarray["$ftype"]}
done

-> ./filestat.sh ../testproject/
============ File types and counts =============
very short file (no magic) : 1
Bourne-Again shell script text executable : 1
VAX COFF executable not stripped : 3
POSIX shell script text executable : 10
data : 2
VAX COFF executable : 1
VAX COFF executable - version 26967 : 1
ASCII text : 14
empty : 1
ASCII English text : 2
```

这段代码
```bash
done< <(find $path –type f –print);
```
它的执行逻辑如下：
```bash
while read line;
    do something
done < filename
```
这里不用 filename ，而是用 find 命令的子进程输出来代替文件名。
即 `<(find $path -type f -print)` 等同于文件名。
注意，第一个`<`用于输入重定向，第二个`<`用于将子进程的输出装换成文件名。在两个`<`之间有一个空格，避免shell将其解释为`<<`操作符。

`<<<` 也可以让我们将字符串作为输入文件，因此上面代码也可改为
```bash
done <<< "`find $path –type f –print`"        # 注意括号替换为了分号和反引号
```

## 环回文件

通常在物理设备上创建文件系统，这些儿物理设备以设备文件的形式来使用，将设备文件挂载到一些儿称为挂载点（mount point）的目录即可使用物理设备上的文件系统。

而环回文件系统是指那些在文件中而非物理设备中创建的文件系统。

环回文件自身包含了文件系统，可以像物理设备一样使用 mount 挂载，使得我们可以在物理磁盘的文件中创建逻辑磁盘。

## ISO 文件

## 比较文件差异

比较文件
```bash
-> diff s1.txt s2.txt
1d0
< key say hometown
2a2
> 787 wa ha ha
4d3
< 444 where are you from
5a5
> key say hometown

-> diff -u s1.txt s2.txt
--- s1.txt	2017-10-05 17:32:16.595863026 +0800
+++ s2.txt	2017-10-05 17:32:41.113115076 +0800
@@ -1,5 +1,5 @@
-key say hometown
 111 hi  beijing
+787 wa ha ha
 333 who are you tianjin
-444 where are you from
 aaa i am shenlin    shanghai
+key say hometown
```
* `<` 表示此行在第一个文件里，`>` 表示此行在第二个文件里
* `-u` 是一体化输出，`-` 是第二个文件相对于第一个文件少的行，`+` 是第二个文件相对于第一个文件多的行

生成补丁文件
```bash
-> diff -u s1.txt s2.txt > s.patch
```

应用补丁文件
```bash
-> patch -p1 s1.txt < s.patch
patching file s1.txt

-> diff s1.txt s2.txt
# 输出为空，两个文件一样了

# 再次应用补丁文件
# 或者直接使用 -R reverse 反转参数，则不会提示 `Assume -R? [n] y`
# -> patch -p1 -R s1.txt < s.patch
# patching file s1.txt
-> patch -p1 s1.txt < s.patch
patching file s1.txt
Reversed (or previously applied) patch detected!  Assume -R? [n] y

# 文件被还原
-> diff -u s1.txt s2.txt
--- s1.txt	2017-10-05 18:53:42.069380116 +0800
+++ s2.txt	2017-10-05 17:32:41.113115076 +0800
@@ -1,5 +1,5 @@
-key say hometown
 111 hi  beijing
+787 wa ha ha
 333 who are you tianjin
-444 where are you from
 aaa i am shenlin    shanghai
+key say hometown
```

生成目录差异，以递归的形式对目录中的所有内容生成差异
```bash
$ diff -Naur directory1 directory2
```
* -N ：将所有缺失的文件视为空文件。
* -a ：将所有文件视为文本文件。
* -u ：生成一体化输出。
* -r ：遍历目录下的所有文件

## 输出文件的前几行后几行

```bash
# head 默认查看头 10 行，两种方法
-> head multiline.txt 
-> cat multiline.txt | head
The 1 line
The 2 line
The 3 line
The 4 line
The 5 line
The 6 line
The 7 line
The 8 line
The 9 line
The 10 line

# 输出前 3 行
-> head -n 3 multiline.txt 
The 1 line
The 2 line
The 3 line

# 从反方向指定：输出除了最后 98 行之外的所有行
-> head -n -98 multiline.txt 
The 1 line
The 2 line

# 输出末尾 10 行
-> tail multiline.txt 
-> cat multiline.txt | tail
The 91 line
The 92 line
The 93 line
The 94 line
The 95 line
The 96 line
The 97 line
The 98 line
The 99 line
The 100 line

# 输出末尾 3 行
-> tail -n 3 multiline.txt 
The 98 line
The 99 line
The 100 line

# 输出末尾 3 行
-> tail -n -3 multiline.txt 
The 98 line
The 99 line
The 100 line

# 输出第 98 行开始的行
-> tail -n +98 multiline.txt 
The 98 line
The 99 line
The 100 line
```

tail 的特殊选项 `-f` 或 `--follow`，可以不间断的监视文件中新添加的内容
适用于监视日志文件等
```bash
-> tail -f multiline.txt 
The 91 line
The 92 line
The 93 line
The 94 line
The 95 line
The 96 line
The 97 line
The 98 line
The 99 line
The 100 line
The 101 line                        # <-- 新追加的行会自动显示

-> sudo tail -f /var/log/messages      # 监控日志消息
[sudo] password for ssy: 
Oct  6 07:20:26 e42 kernel: 00:00:00.043406 main     OS Product: Linux
Oct  6 07:20:26 e42 kernel: 00:00:00.050377 main     OS Release: 2.6.32-696.3.2.el6.i686
Oct  6 07:20:26 e42 kernel: 00:00:00.053060 main     OS Version: #1 SMP Tue Jun 20 00:48:23 UTC 2017
Oct  6 07:20:26 e42 kernel: 00:00:00.053500 main     Executable: /opt/VBoxGuestAdditions-5.1.22/sbin/VBoxService
Oct  6 07:20:26 e42 kernel: 00:00:00.053509 main     Process ID: 1296
Oct  6 07:20:26 e42 kernel: 00:00:00.053516 main     Package type: LINUX_32BITS_GENERIC
Oct  6 07:20:26 e42 kernel: 00:00:00.088351 main     5.1.22 r115126 started. Verbose level = 0
Oct  6 07:20:26 e42 vboxadd-service.sh: VirtualBox Guest Addition service started.
Oct  6 07:20:30 e42 xinetd[1417]: xinetd Version 2.3.14 started with libwrap loadavg labeled-networking options compiled in.
Oct  6 07:20:30 e42 xinetd[1417]: Started working: 0 available services

-> dmesg | tail -f                  # 查看内核的环形缓冲区消息，用来调试USB设备，或是查看 SCSI 磁盘 sda,sdb...
All bugs added by David S. Miller <davem@redhat.com>
VBoxService 5.1.22 r115126 (verbosity: 0) linux.x86 (Apr 28 2017 17:27:07) release log
00:00:00.028406 main     Log opened 2017-10-05T23:20:26.170497000Z
00:00:00.043406 main     OS Product: Linux
00:00:00.050377 main     OS Release: 2.6.32-696.3.2.el6.i686
00:00:00.053060 main     OS Version: #1 SMP Tue Jun 20 00:48:23 UTC 2017
00:00:00.053500 main     Executable: /opt/VBoxGuestAdditions-5.1.22/sbin/VBoxService
00:00:00.053509 main     Process ID: 1296
00:00:00.053516 main     Package type: LINUX_32BITS_GENERIC
00:00:00.088351 main     5.1.22 r115126 started. Verbose level = 0
```

`-s` 设定睡眠间隔，相当于设置了被监控文件的更新间隔
```bash
-> sudo tail -s 3 -f /var/log/messages  
```

`--pid` 设定当进程ID为几的进程结束后，tail 也会自动结束
例如 vim 进程一直在向文件 file 中追加数据，则 tail 可以设置根据 vim 进程是否退出来决定是否退出监视 file 文件
```bash
-> PID=$(pidof vim)
-> tail -f file --pid $PID
```
??? 没测试成功，会根据 vim 的进程自动退出，但没有更新监控内容，确认 vim 已经写入文件了
??? 经测试和 vim 有关系，使用 echo >> file 增加的行会自动显示，但使用 vim 打开再关闭后就不会自动显示了

## 列出目录本身

列出当前目录下的所有目录

```bash
-> ls -l | grep "^d"
drwxr-xr-x. 28 ssy ssy     4096 5月  21 16:50 apr-1.5.2
drwxr-xr-x. 20 ssy ssy     4096 5月  21 17:51 apr-util-1.5.4
drwxr-xr-x. 12 ssy ssy     4096 5月  30 15:55 httpd-2.4.25
```
* ls -l 的每行的第一个字符表示文件类型
* 目录的字符是 d

```bash
-> ll -d */
drwxr-xr-x. 28 ssy ssy  4096 5月  21 16:50 apr-1.5.2/
drwxr-xr-x. 20 ssy ssy  4096 5月  21 17:51 apr-util-1.5.4/
drwxr-xr-x. 12 ssy ssy  4096 5月  30 15:55 httpd-2.4.25/
```
* -d 表示遇到目录时列出目录本身而非目录的文件
* */ 表示以 / 结尾 ???

```bash
-> ll -F | grep "/$"
drwxr-xr-x. 28 ssy ssy     4096 5月  21 16:50 apr-1.5.2/
drwxr-xr-x. 20 ssy ssy     4096 5月  21 17:51 apr-util-1.5.4/
drwxr-xr-x. 12 ssy ssy     4096 5月  30 15:55 httpd-2.4.25/
```
* ls 的 -F 在每个输出项的后面都添加了一个表示文件类型的字符
* 目录的字符是 /，所以目录是 / 结尾的

```bash
-> find . -maxdepth 1 -type d
.
./.pki
./testmod
./apr-1.5.2
./apr-util-1.5.4
```

## 快速切换目录

### 两个目录

`cd -` 可以在最近使用的两个目录之间快速切换

### 两个以上目录

命令行下涉及到 2 个目录以上的切换时，如 `/var/logs`，`/usr/src`，`/home/ssy`，使用 pushd, popd 是一个快捷的方法

pushd 和 popd 把目录的路径存储在栈中，栈是 LIFO - Last In First Out

pushd 把目录入栈并自动切换到刚入栈的目录

popd 弹出栈顶目录，并自动切换到最新的栈顶目录

dirs 查看栈中目录列表

```bash
-> dirs                                 # 查看目录栈
~

ssy@e42.matrix ~
-> pushd /var/log/                      # 把目录入栈
/var/log ~

ssy@e42.matrix log                      # 则已经自动切换到了入栈的目录
-> pwd
/var/log

...

-> dirs                                 # 经过多次入栈后，栈中的目录，每个目录都有默认编号，从 0 开始
/etc/cron.d /usr/local/httpd-2.4.25 /var/log ~

ssy@e42.matrix cron.d
-> popd                                 # 把目录出栈
/usr/local/httpd-2.4.25 /var/log ~

ssy@e42.matrix httpd-2.4.25             # 出栈后自动切换到新的栈顶目录
-> pwd
/usr/local/httpd-2.4.25

ssy@e42.matrix httpd-2.4.25             # 根据栈中目录的编号切换到指定目录
-> pushd +2
~ /usr/local/httpd-2.4.25 /var/log

ssy@e42.matrix log                      # 可见指定编号的目录被提升到了栈顶
-> dirs
/var/log ~ /usr/local/httpd-2.4.25

ssy@e42.matrix log
-> popd +2                              # 把栈中指定编号的目录出栈，这时并不自动切换目录
/var/log ~

ssy@e42.matrix log
-> dirs
/var/log ~
```

## 统计文件行数、词数、字符数

`-l` 行数 `-w` 词数 `-c` 字符数
```bash
-> cat out.txt
aaa
bbb
ccc
ddd

-> wc out.txt           # 没有选项，则分别输出行数、词数、字符数
 4  4 16 out.txt

-> wc -l out.txt        # 行数
4 out.txt

-> wc -w out.txt        # 词数
4 out.txt

-> wc -c out.txt        # 字符数
16 out.txt

-> cat out.txt | wc -c  # 通过 stdin
16

-> wc -L out.txt        # 打印出文件最后一行的长度
3 out.txt
```

## 目录以树形显示

tree 命令可能发行版中没有包含，先安装
```bash
-> yum install -y tree
```

树形显示目录
```bash
-> tree testproject/
testproject/
├── 11.txt
├── 1.txt.gpg
├── base64.txt
├── data.txt
├── filestat.sh
├── junk.data
├── link.txt -> s1.txt
├── multiline.txt
├── s1.txt
├── s1.txt.orig
├── s2.txt
├── s.patch
├── subdir
│   └── 3.txt
├── test1.txt
├── test2.txt
└── test.sh
```

`-p` 显示权限位
```bash
-> tree testproject/ -p
testproject/
├── [-rwxrwxrwx]  11.txt
├── [-rwxrwxrwx]  1.txt.gpg
├── [-rwxrwxrwx]  base64.txt
├── [-rwxrwxrwx]  data.txt
├── [-rwxrw-r--]  filestat.sh
├── [-rwxrwxrwx]  junk.data
├── [lrwxrwxrwx]  link.txt -> s1.txt
├── [-rw-rw-r--]  multiline.txt
├── [-rwxrwxrwx]  s1.txt
├── [-rwxrwxrwx]  s1.txt.orig
├── [-rwxrwxrwx]  s2.txt
├── [-rw-rw-r--]  s.patch
├── [drwxr-xr-x]  subdir
│   └── [-rw-r--r--]  3.txt
├── [-rwxrwxrwx]  test1.txt
├── [-rwxrwxrwx]  test2.txt
└── [-rwxrw-r--]  test.sh
```

`-P` 模式匹配文件
```bash
-> tree testproject/ -P "*.sh"
testproject/
├── filestat.sh
├── subdir
└── test.sh

1 directory, 2 files
```

`-I` 排除模式匹配的文件
```bash
-> tree testproject/ -I "*.sh"
testproject/
├── 11.txt
├── 1.txt.gpg
├── base64.txt
├── data.txt
├── junk.data
├── link.txt -> s1.txt
├── multiline.txt
├── s1.txt
├── s1.txt.orig
├── s2.txt
├── s.patch
├── subdir
│   └── 3.txt
├── test1.txt
└── test2.txt

1 directory, 14 files
```

`-h` 显示目录和文件大小
```bash
-> tree -h testproject/
testproject/
├── [  86]  11.txt
├── [ 121]  1.txt.gpg
├── [ 149]  base64.txt
├── [  39]  data.txt
├── [ 368]  filestat.sh
├── [   1]  junk.data
├── [   6]  link.txt -> s1.txt
├── [1.2K]  multiline.txt
├── [ 109]  s1.txt
├── [  99]  s1.txt.orig
├── [  99]  s2.txt
├── [ 256]  s.patch
├── [4.0K]  subdir
│   └── [   0]  3.txt
├── [  12]  test1.txt
├── [  12]  test2.txt
└── [  62]  test.sh
```

`-H` 输出 HTML 格式
```bash
-> tree testproject/ -H http://localhost -o out.html

-> ll out.html 
-rw-rw-r--. 1 ssy ssy 3485 10月  6 11:53 out.html
```

## tmp


更改 linux 语言
```bash
-> locale
LANG=zh_CN.UTF-8
LC_CTYPE="zh_CN.UTF-8"
LC_NUMERIC="zh_CN.UTF-8"
LC_TIME="zh_CN.UTF-8"
LC_COLLATE="zh_CN.UTF-8"
LC_MONETARY="zh_CN.UTF-8"
LC_MESSAGES="zh_CN.UTF-8"
LC_PAPER="zh_CN.UTF-8"
LC_NAME="zh_CN.UTF-8"
LC_ADDRESS="zh_CN.UTF-8"
LC_TELEPHONE="zh_CN.UTF-8"
LC_MEASUREMENT="zh_CN.UTF-8"
LC_IDENTIFICATION="zh_CN.UTF-8"
LC_ALL=

-> LANG=en_US.UTF-8
```

递归列出当前目录下的所有子目录和文件
```bash
-> ls *
```

查找当前目录下的某个文件
```bash
-> ls * | grep "meta_ccld"
meta_ccld
```
