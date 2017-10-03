---
title: bash - linux shell 脚本攻略 - chapter 1 - 基本功能
categories: bash
tags: bash
---

shell 基本功能

<!--more-->

## shebang

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

环境变量是未在当前进程中定义，而从父进程中继承而来的变量

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

export 命令用来设置环境变量
```bash
$ export PATH="$PATH:/home/user/bin"

#或者
$ PATH="$PATH:/home/user/bin"
$ export PATH
```

LIBRARY_PATH 环境变量用于在**程序编译期间**查找动态链接库时指定查找共享库的路径，例如，指定gcc编译需要用到的动态链接库的目录
```bash
export LIBRARY_PATH=LIBDIR1:LIBDIR2:$LIBRARY_PATH
```

LD_LIBRARY_PATH 环境变量用于在**程序加载运行期间**查找动态链接库时指定除了系统默认路径之外的其他路径，注意，LD_LIBRARY_PATH中指定的路径会在系统默认路径之前进行查找
```bash
export LD_LIBRARY_PATH=LIBDIR1:LIBDIR2:$LD_LIBRARY_PATH
```

通过函数的方式设置路径环境变量
```bash
$ prepend PATH /opt/myapp/bin

$ prepend LD_LIBRARY_PATH /opt/myapp/lib

$ cat prepend.sh
prepend() { 
    [ -d "$2" ] && eval $1=\"$2\$\{$1:+':'\$$1\}\" && export $1 ; 
}
# 先检查第二个参数指定的目录是否存在
# 存在则把第二个参数指定的目录路径添加到第一个参数的前面
# 添加第二个参数时会先检查第一个参数是否为空，第一个参数为空则不添加冒号，否则结果末尾会多个冒号
```
* shell 参数扩展形式 `${parameter:+expression}` ，表示 parameter 不为空时则使用 expression 的值

其他环境变量
```bash
-> echo $HOME
/home/ssy

-> echo $PWD
/home/ssy

-> echo $USER
ssy

-> echo $UID
500                 # 普通用户，root 用户ID是 0

-> echo $SHELL
/bin/bash
```

获取字符串长度
```bash
-> var=abcdef

-> echo ${#var}
6
```

## 数学运算

### 普通赋值

使用普通的变量赋值方法定义数值，虽然依然被存储为字符串，但可以通过一些儿方法进行数学运算
```bash
var1=123;var2=234;
```
* 什么方法???

### let

let 命令可以执行基本的数学运算，使用 let 时，变量名前不需要加 `$`
```bash
-> var1=12;var2=34;let result=var1+var2;echo $result;
46

-> let result--;echo $result
45

-> let result++;echo $result
46

-> let result+=4;echo $result
50

-> let result-=4;echo $result
46
```

### `[]` 操作符

使用`[]`操作符，方法和 let 相同，变量前可加 `$` 也可不加
```bash
-> var1=12;var2=34;result=$[var2-var1];echo $result
22

-> var1=12;var2=34;result=$[$var2-$var1];echo $result
22
```

### `(())` 操作符

使用`(())`操作符，变量前要加 `$`
```bash
-> var1=12;var2=34;result=$((var2-var1));echo $result
22

-> var1=12;var2=34;result=$(($var2-$var1));echo $result
22
```
* 经测试可加可不加

### bc

bc 是一个数学运算高级工具，可以用来进行浮点数运算和调用一些儿高级函数，例如平方和平方根
```bash
-> echo "10^3"|bc
1000

-> echo "sqrt(100)"|bc
10

-> echo "4 * 0.56"
4 * 0.56

-> echo "4 * 0.56"|bc
2.24

-> result=`echo "4 * 0.56"|bc`
-> echo $result
2.24
```

因为给 bc 的参数是通过 stdin 传递的，所以要把参数 echo，多个参数之间使用分号，例如通过 scale 设定小数位数
```bash
-> echo "scale=2;3/8"|bc
.37
```

通过 obase, ibase 进行进制转换
```bash
-> echo "obase=2;ibase=10;123"|bc
1111011

-> echo "obase=10;ibase=2;10101110"|bc
174
```

## 文件描述符及重定向

文件描述符是与文件输入、输出相关联的整数

### 系统预留的文件描述符

0 标准输入 stdin
1 标准输出 stdout
2 标准错误 stderr

命令无错误退出返回 0，返回 1~255 则表示错误码，可使用 `$?` 来查询上个命令的退出状态

把错误输出到文件
```bash
-> ls + > out.txt                   # 错误命令，标准错误默认输出到屏幕，所以并没有输出到文件 out.txt 里
ls: 无法访问+: 没有那个文件或目录

-> cat out.txt

-> ls + 2> out.txt                  # 2> 表示把标准错误输出到文件 out.txt 里

-> cat out.txt
ls: 无法访问+: 没有那个文件或目录
```

可以分别指定标准输出和错误输出到不同的文件
```bash
-> ls >out.txt 2>err.txt
```

把 stderr 转换为 stdout，使得 stderr, stdout 都定向到同一个文件
```bash
$ cmd 2>&1 out.txt

$ cmd &>1 out.txt
```

`/dev/null` 就是个黑洞，任何输入到它里面的内容都会被丢弃
```bash
$ cmd 2> /dev/null
```

### tee

输出被重定向到文件后，就无法通过管道命令把输出再传递给后面的命令使用，要二者兼得，使用 tee 命令

tee 命令既可以重定向输出，又可以保留输出的一份副本作为后续命令的 stdin

tee 命令只能从 stdin 读取，不能从 stderr 读取

```bash
-> ls -l | tee out.txt | cat -n
     1	总用量 69532
     2	drwxr-xr-x. 28 ssy ssy     4096 5月  21 16:50 apr-1.5.2
     3	-rw-rw-r--.  1 ssy ssy  5662720 4月  29 2015 apr-1.5.2.tar
     4	drwxr-xr-x. 20 ssy ssy     4096 5月  21 17:51 apr-util-1.5.4
     5	-rw-rw-r--.  1 ssy ssy   874044 9月  20 2014 apr-util-1.5.4.tar.gz
     6	-rw-rw-r--.  1 ssy ssy        0 9月  29 14:03 err.txt

-> cat out.txt
总用量 69532
drwxr-xr-x. 28 ssy ssy     4096 5月  21 16:50 apr-1.5.2
-rw-rw-r--.  1 ssy ssy  5662720 4月  29 2015 apr-1.5.2.tar
drwxr-xr-x. 20 ssy ssy     4096 5月  21 17:51 apr-util-1.5.4
-rw-rw-r--.  1 ssy ssy   874044 9月  20 2014 apr-util-1.5.4.tar.gz
-rw-rw-r--.  1 ssy ssy        0 9月  29 14:03 err.txt
```
* 把 `ls -l` 的执行结果写入 out.txt，然后再把执行结果作为 stdin 传给 cat 命令，cat 命令给每行加了行号
* tee 命令默认是覆盖文件内容，使用 `-a` 选项可以追加文件内容

### 特殊输入输出设备文件

/dev/stdin 对应 stdin
/dev/stdout 对应 stdout
/dev/stderr 对应 stderr

可以使用 stdin 作为命令参数，用 `-` 来表示 stdin
```bash
-> echo what the fuck | tee -
what the fuck
what the fuck
```

### 从文件读取数据

把 out.txt 文件中的行穿给 cat 命令
```bash
-> cat -n < out.txt
     1	aaa
     2	bbb
```

### 多行文本重定向

`<<eof>` 和 `eof` 之间的内容会被当做 stdin 数据
```bash
-> cat <<eof> out.txt
> abc
> def
> ghi
> eof

-> cat out.txt
abc
def
ghi
```
* 注意 `<<eof>` 的右尖括号只有一个
* eof 不区分大小写

### 自定义文件描述符

通常用到的 3 种文件打开模式
* 只读模式，对应于 `<` 操作符
* 截断写入模式，对应于 `>` 操作符
* 追加写入模式，对应于 `>>` 操作符

使用 exec 命令创建自定义文件描述符，创建时要使用上面的 3 种模式任意一种
```bash
# 创建截断写入描述符
-> exec 3>out.txt

# 使用截断写入描述符
-> echo aaa >& 3

-> cat out.txt
aaa

# 创建追加写入描述符
-> exec 4>>out.txt

# 使用追加写入描述符
-> echo bbb >& 4            # 注意是一个右尖括号

-> cat out.txt
aaa
bbb

# 创建读取描述符
-> exec 3<out.txt

# 使用读取描述符
-> cat <& 3
aaa
bbb
```

## 数组

bash 支持索引数组和关联数组，关联数组要 bash 4.0 及以后版本，关联数组是利用散列技术实现的

```bash
# 两种方式定义索引数组
idx_array=("apple" "orange")
idx_array[2]="pear"
idx_array[3]="banana"

# 声明关联数组
declare -A ass_array

# 两种方式定义关联数组
ass_array=(['red']='bus' [green]='car')
ass_array[yellow]='rail'
ass_array[blue]='plane'

# 获取数组元素
index=3
echo ${idx_array[1]}
echo ${idx_array[$index]}

ass=green
echo ${ass_array[red]}
echo ${ass_array['red']}
echo ${ass_array['green']}
echo ${ass_array[$ass]}

# 列出数组所有元素
echo ${idx_array[*]}
echo ${idx_array[@]}

echo ${ass_array[*]}
echo ${ass_array[@]}

# 列出数组所有索引
echo ${!idx_array[*]}
echo ${!idx_array[@]}

echo ${!ass_array[*]}
echo ${!ass_array[@]}

# 获取数组长度
echo ${#idx_array[*]}
echo ${#idx_array[@]}

echo ${#ass_array[*]}
echo ${#ass_array[@]}
```

## 别名

别名就是一种便捷方式，省去输入一长串命令的麻烦

```bash
# 格式，同名的别名将被覆盖
$ alias new_command='command sequence'

# 举例：通过别名让移除命令先备份到 backup 目录再移除
$ alias rm='cp $@ ~/backup && rm $@'


# 查看别名列表
-> alias
alias l.='ls -d .* --color=auto'
alias ls='ls --color=auto'
alias ll='ls -l --color=auto'

# 通过命令行设置的别名都是临时的，关闭当前终端后就失效，长久生效需写入 ~/.bashrc 
-> echo 'alias ll="ls -l --color=auto"' >> ~/.bashrc

# 取消别名
-> unalias ll

-> alias ll=

-> 从 ~/.bashrc 文件中删除别名命令行

# 对别名转义：在命令前加 \ ，则如果有同名的别名覆盖了原始的命令，则仍旧执行原始命令而忽略定义的别名
# 这样有时是为了安全考虑，防止攻击者把部分普通命令替换为了特权命令，借此盗取用户输入的重要信息
-> \ls
```

## 终端信息

tput 和 stty 是两款终端处理工具

```bash
# 获取终端的行数、列数
-> tput cols
156

-> tput lines
43

# 打印出当前终端名
-> tput longname
xterm terminal emulator (X Window System)

# 利用 stty 实现输入密码时无任何显示
-> cat test.sh
echo "Enter password: "
stty -echo              # 禁止把输出发送到终端
read password
stty echo               # 允许把输出发送到终端
echo Password read.

-> ./test.sh
Enter password: 
Password read.
```

## 日期时间

GMT : Greenwich Mean Time，格林尼治标准时间，指位于伦敦郊区的皇家格林尼治天文台的标准时间，因为本初子午线被定义在通过那里的经线。理论上格林尼治标准时间的正午是指当太阳横穿格林尼治子午线时的时间。

UTC : Coordinated Universal Time，协调世界时，又称为世界标准时间

Unix 认为 `UTC 1970.1.1 0:00:00` 是纪元时间，POSIX 标准推出后，此时间也被称为 POSIX 时间

```bash
-> date
2017年 09月 30日 星期六 22:18:42 CST

# Unix 时间戳
-> date +%s
1506841653

# 把时间转换为 Unix 时间戳
-> date --date "Thu Nov 18 08:07:21 IST 2010" +%s
1290047841

# 根据时间算出星期几
-> date --date "2017-10-01" +%A
星期日

# 自定义日期输出格式，字符串里的 + 号是必须的
-> date "+%Y-%m-%d"
2017-10-01

# 设置日期和时间
date -s "21 June 2009 11:01:22"

# 计算代码执行时间
start=$(date +%s)
sleep 2
end=$(date +%s)
time=$(( end - start ))
echo $time
exit

# 使用 time 获取代码执行时间
-> time ./test.sh
3

real	0m2.188s
user	0m0.033s
sys	    0m0.141s
```

|日期内容|格 式|
|星期|%a（例如：Sat） %A（例如：Saturday）|
|月|%b（例如：Nov） %B（例如：November）|
|日|%d（例如：31） |
|固定格式日期（mm/dd/yy）| %D（例如：10/18/10）|
|年|%y（例如：10） %Y（例如：2010）|
|小时|  %I或%H（例如：08）|
|分钟|  %M（例如：33）|
|秒|  %S（例如：10）|
|纳秒|  %N（例如：695208515）|
|Unix纪元时（以秒为单位）|  %s（例如：1290049486）|

## 调试脚本（?未测试?未完整）

### 修改 shebang

`#!/bin/bash` 改成  `#!/bin/bash -xv`

### `bash -x`

```bash
$ bash -x script.sh
```

### `sex -x set +x`

set -x ：在执行时显示参数和命令。
set +x ：禁止调试。
set -v ：当命令进行读取时显示输入。
set +v ：禁止打印输入

### debug

`$ _DEBUG=on ./script.sh`

## 函数和参数

### 定义函数

```bash
function func_name()
{

}

func_name()
{

}
```

### 调用函数

```bash
func_name       # 无需括号
```

### 传递参数

```bash
func_name arg1 arg2     # 参数之间是空格
```

通过命令行传递给脚本文件的可选参数，按顺序分别是 : `$0, $1, $2......${10},${11}`
* `$0` 是脚本文件名，可以通过符号链接对同一个脚本文件建立多个不同的文件名，然后脚本中通过文件名参数来执行不同的操作
* `$1` 是第一个参数，`$2` 是第二个参数……依次类推，9 以后的参数要使用大括号括起来
* `$*` 包含了除 `$0` 外的所有参数，被扩展成"$1c$2c$3c$4c"，其中 c 是 IFS 第一个字符
* `$@` 包含了除 `$0` 外的所有参数，被扩展成"$1" "$2" "$3" "$4" ...
* `$#` 是参数的数量
* `$?` 是上一个命令的返回值
* `$!` 是上一个后台命令的PID
* `$$` 当前脚本进程的PID
`$@` 比 `$*` 要用的多，因为 `$*` 把所有参数拼成了一个字符串，因此使用较少

### 递归函数

bash 支持函数递归

著名的利用递归实现的 fork 炸弹
```bash
:(){ :|:& };:

# 上面的代码展开
:()
{
    :|:& 
}
:
```
* `:` 是函数名
* 函数名后面加 & 号表示把子进程放到后台
* 函数主体的意思：递归调用自身，把冒号函数 `:` 的输出通过管道命令传递给另一个冒号函数作为输入，并且放入后台运行
* 解决函数是限制一个用户可以创建的最多进程数量
    * 具体函数是在 `/etc/security/limits.conf` 文件里添加 `ulimit -u 30`

### 导出函数

函数也可以像环境变量一样导出，导出后函数的作用域扩展到子进程中了

```bash
$ export -f fname
```

### 获取返回值

返回值也称为退出状态，0 表示成功退出，非 0 表示不成功

`$?` 返回上个命令的退出状态

```bash
CMD=""
$CMD
if [ $? -eq 0 ];        # 根据上个命令的退出状态进行不同流程
then
    echo "$CMD executed successfully"
else
    echo "$CMD terminated unsuccessfully"
fi
```

### 传递参数

可以用多种不同的格式给命令传递参数，

例如命令接收 4 个参数：`-p`,`-v` 是可选选项，`-k` 是数字参数，`file` 是文件名参数

下面传递参数的方式都是正确的

```bash
$ cmd -p -v -k 1 file

$ cmd -pv -k 1 file

$ cmd -vpk 1 file

$ cmd file -pvk 1
```

## 将命令序列的输出读入变量

### 子 shell

可以使用 `()` 操作符来定义一个子 shell

子 shell 本身是独立的进程，因此在子 shell 中执行的命令不会对当前 shell 有任何影响

例如在子 shell 中改变当前目录时，不会影响主 shell 的当前目录
```bash
-> cat test.sh
pwd;
(cd /var; ls;)
pwd;

-> ./test.sh
/home/ssy
cache  cvs  db	empty  games  lib  local  lock	log  mail  nis	opt  preserve  run  spool  tmp	yp
/home/ssy
```

可以通过子 shell 读取由管道相连的命令序列的输出 `var=$(cmd1 | cmd2)`
```bash
-> var=$(ls)

-> echo $var
abasdf apr-1.5.2 apr-1.5.2.tar apr-util-1.5.4 apr-util-1.5.4.tar.gz err.txt httpd-2.4.25 httpd-2.4.25.tar httpd-2.4.25.tar.bz2 mysql nginx-1.13.0 nginx-1.13.0.tar.gz out.txt pcre-8.40 pcre-8.40.zip php-7.1.5 php-7.1.5.tar.bz2 sudo testdir testproject test.sh

-> var=$(ls | cat -n)

-> echo $var
1 abasdf 2 apr-1.5.2 3 apr-1.5.2.tar 4 apr-util-1.5.4 5 apr-util-1.5.4.tar.gz 6 err.txt 7 httpd-2.4.25 8 httpd-2.4.25.tar 9 httpd-2.4.25.tar.bz2 10 mysql 11 nginx-1.13.0 12 nginx-1.13.0.tar.gz 13 out.txt 14 pcre-8.40 15 pcre-8.40.zip 16 php-7.1.5 17 php-7.1.5.tar.bz2 18 sudo 19 testdir 20 testproject 21 test.sh

-> echo "$var"                      # 放入双引号中的变量输出将会保留空格和换行
     1	abasdf
     2	apr-1.5.2
     3	apr-1.5.2.tar
     4	apr-util-1.5.4
     5	apr-util-1.5.4.tar.gz
     6	err.txt
     7	httpd-2.4.25
     8	httpd-2.4.25.tar
     9	httpd-2.4.25.tar.bz2
    10	mysql
    11	nginx-1.13.0
    12	nginx-1.13.0.tar.gz
    13	out.txt
    14	pcre-8.40
    15	pcre-8.40.zip
    16	php-7.1.5
    17	php-7.1.5.tar.bz2
    18	sudo
    19	testdir
    20	testproject
    21	test.sh
```

组合多个命令时，命令被称为过滤器 filter，使用管道 pipe 链接过滤器，如 `cmd1 | cmd2 | cmd3`

### 反引用

通过反引号 `var=`cmd``

```bash
-> var=`ls`

-> echo $var
abasdf apr-1.5.2 apr-1.5.2.tar apr-util-1.5.4 apr-util-1.5.4.tar.gz err.txt httpd-2.4.25 httpd-2.4.25.tar httpd-2.4.25.tar.bz2 mysql nginx-1.13.0 nginx-1.13.0.tar.gz out.txt pcre-8.40 pcre-8.40.zip php-7.1.5 php-7.1.5.tar.bz2 sudo testdir testproject test.sh

-> var=`ls | cat -n`

-> echo $var
1 abasdf 2 apr-1.5.2 3 apr-1.5.2.tar 4 apr-util-1.5.4 5 apr-util-1.5.4.tar.gz 6 err.txt 7 httpd-2.4.25 8 httpd-2.4.25.tar 9 httpd-2.4.25.tar.bz2 10 mysql 11 nginx-1.13.0 12 nginx-1.13.0.tar.gz 13 out.txt 14 pcre-8.40 15 pcre-8.40.zip 16 php-7.1.5 17 php-7.1.5.tar.bz2 18 sudo 19 testdir 20 testproject 21 test.sh
```

## 使用 read 命令实现各种字符读取

read 命令用于从键盘或标准输入中读取文本，可以和用户交互，但大多数时候都是按下回车键才标志输入完毕

有些儿情形是没法按回车的，如游戏中使用方向键控制小球的运动等

`-n` 选项指定读取的字符个数，例如读取 3 个字符存入变量 var
```bash
$ read -n 3 var
```

`-s` 用无回显的方式读取密码存入变量 var
```bash
$ read -s var
```

`-p` 显示提示信息
```bash
$ read -p "input your pwd:" var
```

`-t` 限制用户在 10 秒内完成收入
```bash
$ read -t 10 var
```

`-d` 设置特定的定界符作为输入行的结束
```bash
$ read -d ":" var
```

## 比循环高效的重复执行命令方法

在多数的现代系统中，true 是通过 /bin 目录下的一个二进制文件来实现的，即
```bash
-> ll /bin/true
-rwxr-xr-x. 1 root root 20504 3月  23 2017 /bin/true
```
所以在类似 while true 一类的循环中，每次判断 shell 都要生成一个进程，效率较低
```bash
repeat()
{
    while true
    do
        $@ && return
    done
}
```
更高效的方法是使用 shell 內建的冒号命令，冒号命令的返回码总是 0
```bash
repeat()
{
    while :
    do
        $@ && return
    done
}
```
还可以加入延时
```bash
repeat() { while :; do $@ && return; sleep 30; done }
```
调用函数
```bash
repeat wget -c http://www.example.com/software-0.1.tar.gz
```

## 内部字段分隔符

### IFS

IFS，Internal Field Separator， 内部字段分隔符

IFS 是存储了定界符的环境变量，存储的是 shell 环境默认使用的定界字符串，默认字符为空白字符：空格、换行、制表符

定界符是用来把一个数据流划分成不同数据元素的符号

```bash
-> cat test.sh
data="name,sex,rollno,location"
oldIFS=$IFS
IFS=,
for item in $data;
do
    echo Item: $item
done
IFS=$oldIFS

-> ./test.sh
Item: name
Item: sex
Item: rollno
Item: location
```

## 流程控制

```bash
if condition;
then
    commands;
else if condition; then
    commands;
else
    commands;
fi

# 两种 for 循环
for var in $list;       # list 变量即可以是字符串如"x:root:500:/bin/bash"，也可以是一个序列如{a..z}
do
    echo $var;
done

for((i=0;i<10;i++))
{
    commands; #使用变量$i
}

# while 循环
while condition;
do
    commands;
done

# until 循环
until [ condition ];
do
    commands;
done
```

## 流程控制中的比较语句和测试语句

### test 和 `[]`

可以用 test 命令或 `[]` 来测试条件

`[]` 是 test 命令的简化写法

测试条件通常放在中括号 `[]` 中，注意测试条件和括号之间必须有空格，即`[ condition ]`

```bash
-> var=3;if test $var -eq 3;then echo yes;fi
yes

-> var=3;if [ $var -eq 3 ];then echo yes;fi
yes
```

### 文件系统测试

`[ -e $var ]`   文件或目录是否存在
`[ -d $var ]`   目录是否存在
`[ -f $var ]`   文件是否存在

`[ -r $var ]`   文件或目录是否可读
`[ -w $var ]`   文件或目录是否可写
`[ -x $var ]`   文件或目录是否可执行

`[ -c $var ]`   是否是字符设备
`[ -b $var ]`   是否是块设备
`[ -L $var ]`   是否是符号链接

```bash
-> cat test.sh
if [ -"$1" "$2" ]       # 第一个参数是判断的类型，第二个参数判断的文件或目录
then
    echo yes
else
    echo no
fi

#----e,f,d 目录文件//文件//目录是否存在----

# -e 判断文件或目录是否存在
-> ./test.sh f /home/ssy/out.txt
yes

-> ./test.sh f /home/ssy/asdf.txt
no

-> ./test.sh f /home/ssy
yes

# -f 判断文件是否存在
-> ./test.sh f /home/ssy/out.txt
yes

-> ./test.sh f /home/ssy/asdf.txt
no

-> ./test.sh f /home/ssy      # -f 只判断文件，所以即使存在的目录也返回 no
no

# -d 判断目录是否存在
-> ./test.sh d /home/ssy
yesif test $var -eq 0 ; then echo "True"; fi

-> ./test.sh d /home/ssy/out.txt    # -d 只判断目录，所以即使存在的文件也返回 no
no

#----r,w,x 可读可写可执行----

# -w 判断目录或文件是否可写
-> ./test.sh x /home/ssy
yes

-> ./test.sh x /home/ssy/out.txt
yes

-> ./test.sh x /home/ssy/asdf.txt
no

# -r 判断目录或文件是否可读
-> ./test.sh r /home/ssy
yes

-> ./test.sh r /home/ssy/out.txt
yes

-> ./test.sh r /home/ssy/asdf.txt
no

# -x 判断目录或文件是否可执行
-> ./test.sh x /home/ssy
yes

-> ./test.sh x /home/ssy/out.txt
no

-> ./test.sh x /home/ssy/test.sh
yes

#----c,b,L 字符设备、块设备、符号链接----

# -c 判断是否是字符设备
-> ./test.sh c /home/ssy
no

-> ./test.sh c /home/ssy/out.txt
no

-> ./test.sh c /dev/stdin
yes

# -b 判断是否是块设备
-> ./test.sh b /home/ssy/out.txt
no

-> ./test.sh b /home/ssy
no

-> ./test.sh b /dev/cdrom
yes

# -L 判断是否是符号链接
-> ./test.sh L /dev/cdrom
yes

-> ll /dev/cdrom
lrwxrwxrwx. 1 root root 3 9月  29 10:46 /dev/cdrom -> sr0
```

### 算术比较

`[ $var -eq 3 ]`    等于
`[ $var -ne 3 ]`    不等于
`[ $var -gt 3 ]`    大于
`[ $var -ge 3 ]`    大于等于
`[ $var -lt 3 ]`    小于
`[ $var -le 3 ]`    小于等于
`[ $var -eq 3 -a $var2 -ne 2]`    逻辑与
`[ $var -eq 3 -o $var2 -ne 2]`    逻辑或

### 字符串比较

字符串比较使用一个中括号容易出错，所以常使用双中括号 `[[ condition ]]`

字符串相等比较的 `=` 左右各有一个空格，如果没有空格则是赋值语句

`[[ $str1 = $str2 ]]`   相等
`[[ $str1 == $str2 ]]`  相等，另一种写法
`[[ $str1 != $str2 ]]`  不相等
`[[ $str1 > $str2 ]]`   字母序大于
`[[ $str1 < $str2 ]]`   字母序小于
`[[ -z $str1 ]]`        空字符串
`[[ -n $str1 ]]`        非空字符串
`[[ -n $str1 ]] && [[ -z $str2 ]]`  逻辑与运算符
`[[ -n $str1 ]] || [[ -z $str2 ]]`  逻辑或运算符

## others

逗号分隔型数值 Comma Separated Value，CSV

生成序列
```bash
-> echo {1..9}
1 2 3 4 5 6 7 8 9

-> echo {a..n}
a b c d e f g h i j k l m n
```
