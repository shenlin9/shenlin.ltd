---
title: bash - linux shell 脚本攻略 - chapter 1 - 基本功能
categories: bash
tags: bash
---

<!--more-->

## 

`#!/bin/bash` 读作 shebang 或 sha-bang
unix 中 `#` 读作 sharp 或 hash，`!` 读作 bang

## 两种运行脚本的方式

1、作为 `bash` 命令的参数
```bash
$ bash script.sh
```
* 此时脚本文件中就不需要 shebang 了

2、给予脚本文件执行权限，将其变为可执行文件
```bash
$ chmod a+x script.sh

$ ./script.sh
```
* 内核读取脚本文件时，会注意到首行的 shebang，然后使用 `$ /bin/bash script.sh` 这样的方式来执行脚本
* 脚本要独立运行，必须授权脚本文件执行权限，并且在脚本文件中设置 shebang，这样就可以使用 shebang 后的解释权路径来运行了

## 家目录下的脚本文件

`~/.bash_logout`        
`~/.bash_history`       历史记录文件
`~/.bash_profile`       登录 shell 要执行的脚本
`~/.bashrc`             定义 shell 启动时的提示文本、颜色等

## 输出

echo 和 printf 都可以输出，有选项时则选项必须在输出字符串的前面，否则选项会被认为是另一个输出字符串
```bash
# 正确输出绿色背景
-> echo -e "\e[1;42m Green Background \e[0m"
 Green Background 

# -e 被作为字符串输出了
-> echo "\e[1;42m Green Background \e[0m" -e
\e[1;42m Green Background \e[0m -e
```

### echo

echo 可以直接输出，也可以加单引号或双引号，双引号的变量会被解析
```bash
$ echo

$ echo this is a string

$ echo 'this is a string'

$ echo "this is a string"
```

双引号里的叹号可能引发错误
```bash
$ echo "this is a string!"
bash: !: event not found error
```
* 经测试叹号后面没有空格即出错

因为分号是命令分隔符，所以分号必须使用引号才能输出，否则会被认为是使用分号分隔的两条命令
```bash
$ echo this is a; string
```

echo 默认每次输出后会添加一个换行，可以使用 `-n` 选项忽略结尾换行
```bash
-> echo hello;echo world
hello
world

-> echo -n hello;echo world
helloworld
```

`-e` 选项可以输出转义字符和打印彩色输出
```bash
-> echo "a\tb\tc"
a\tb\tc

-> echo -e "a\tb\tc"
a	b	c

-> echo -e "\e[1;31m This is red text \e[0m"
 This is red text       # 字体颜色为红色

-> echo -e "\e[1;42m Green Background \e[0m"
 Green Background       # 背景色为绿色
```
* 文字颜色码：重置=0，黑色=30，红色=31，绿色=32，黄色=33，蓝色=34，洋红=35，青色=36，白色=37
* 背景颜色码：重置=0，黑色=40，红色=41，绿色=42，黄色=43，蓝色=44，洋红=45，青色=46，白色=47

### printf

printf 可以进行格式化输出
```bash
$ printf "hello, world"
hello, world

$ cat printf.sh
#!/bin/bash
printf "%-5s %-10s %-4s\n" No Name Mark
printf "%-5s %-10s %-4.2f\n" 1 Sarath 80.3456
printf "%-5s %-10s %-4.2f\n" 2 James 90.9989
printf "%-5s %-10s %-4.2f\n" 3 Jeff 77.564

$ ./printf.sh
No   Name      Mark
1    Sarath    80.35
2    James     91.00
3    Jeff      77.56
```
* "%-5s %-10s %-4.2f\n" 是格式化字符串
* %s, %c, %d, %f 是格式替换符
* - 表示左对齐，模式为右对齐
* 数字 5, 10 代表的都是字符串宽度为5 和 10，长的被截断，短的用空格补充
* 浮点数的.2表示保留两位小数

## 变量

bash 脚本里，每个变量的值都是字符串，并且不需要在使用前声明，直接赋值即可

一些儿特殊变量用来存储 shell 环境和 OS 环境的特殊值，这类变量称为环境变量

查看与终端有关的所有环境变量
```bash
$ env
```

查看与某进程有关的环境变量
```bash
-> sudo cat /proc/1/environ         # 1 是 PID，即进程ID。
HOME=/TERM=linuxPATH=/sbin:/bin:/usr/sbin:/usr/bin
```

pgrep 可以查看进程ID
```bash
# 查看 ssh 进程的环境变量
-> pgrep sshd
1385
1958
1962

-> sudo cat /proc/1385/environ
TERM=linuxSSH_USE_STRONG_RNG=0PATH=/sbin:/usr/sbin:/bin:/usr/binRUNLEVEL=3runlevel=3PWD=/LANGSH_SOURCED=1LANG=en_US.UTF-8PREVLEVEL=Nprevious=NCONSOLETYPE=vtSHLVL=2UPSTART_INSTANCE=UPSTART_EVENTS=runlevelUPSTART_JOB=rc_=/usr/sbin/sshd
```
* 上面返回的环境变量是 "name=value" 的形式，每个变量之间使用 null 字符分隔，即'\0'

使用 tr 命令把 null 字符替换为换行符
```bash
# 查看 ssh 进程的环境变量，每行一个
-> sudo cat /proc/1385/environ|tr '\0' '\n'
TERM=linux
SSH_USE_STRONG_RNG=0
PATH=/sbin:/usr/sbin:/bin:/usr/bin
RUNLEVEL=3
runlevel=3
PWD=/
LANGSH_SOURCED=1
LANG=en_US.UTF-8
PREVLEVEL=N
previous=N
CONSOLETYPE=vt
SHLVL=2
UPSTART_INSTANCE=
UPSTART_EVENTS=runlevel
UPSTART_JOB=rc
_=/usr/sbin/sshd
```
