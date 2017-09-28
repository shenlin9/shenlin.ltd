---
title: bash - 基础
categories: bash
tags: bash
---

http://www.tldp.org/guides.html

<!--more-->

## bash 名称

bash : GNU Bourne-Again SHell，双关语，发音同 born again shell，因为 Bourne shell是一个早期的重要shell，由Steven Bourne史蒂夫·伯恩在1978年前后编写，并同Version 7 Unix一起发布。bash 则在1987年由布莱恩·福克斯创造。

BASH的正确缩写：

    BASH = GNU Bourne-Again Shell，BASH 是 GNU 组织开发和推广的一个项目。

BASH的作者和产生：

    Bourne shell 的作者是 Steven Bourne，它是 UNIX 最初使用的 Shell 并且在每种 UNIX 上都可以使用。
    而BASH 与 Bourne shell 完全向后兼容，是Bourne shell的扩展。

双关语的经典之处：

    * 作者名叫Steven Bourne，“bourne”与“born”的发音相近，而born有“出生”和“忍受”的含义。
    * Bourne-Again字面意思代表了它是“Bourne shell”l的一种扩展，而暗示“borne-again”、“born-again”则有“重生”和“再次忍受”的意思。（我想作者应该更喜欢“重生”）
    * 它的简写“bash”的意思是“重击”，暗示它的出现是一次强有力的重击。

## sha-bang

用户编写的 bash 脚本应该加上 `.sh` 扩展名，但系统自带的一些儿脚本，如 /etc/rc.d 目录下的脚本则不遵守这种命名习惯

每个脚本文件的第一行应指定当前文件的解释器，使用 `#!` 后面跟解释器路径来指定，`#!` 名为 sha-bang，指定的路径必须正确，否则会提示 “Command Not Found”

如果脚本只使用了系统命令，没有使用外部脚本的命令，则文件头部的 `#!` 可以省略

```
#!/bin/sh
#!/bin/bash
#!/usr/bin/perl
#!/usr/bin/tcl
#!/bin/sed -f
#!/bin/awk -f
```
* 每行调用一个脚本解释器
* `#!/bin/sh` 调用了系统的默认 shell

## 例子

把一堆命令列在一个文件中就可以称为一个最简单的 shell 脚本，目的是简化输入，如：
```
# Cleanup
# 要以 root 身份运行
cd /var/log
cat /dev/null > messages
cat /dev/null > wtmp
echo "Log files cleaned up."
```

改进版
```
#!/bin/bash
# 上面是 bash 脚本的正确开头，#! 指明运行此脚本的解释器

# Cleanup, version 2
# 要以 root 身份运行
# 如果不是 root 身份则应在这里输出错误信息并退出脚本

# 变量比 hard-coded 好
LOG_DIR=/var/log

cd $LOG_DIR
cat /dev/null > messages
cat /dev/null > wtmp
echo "Logs cleaned up."

# 正确的退出脚本应该使用 exit
# exit 0 表示正常退出，非 0 为不正常退出
# 不带参数的 exit 返回的是最后一个命令的退出状态
exit
```

通用增强版
```
#!/bin/bash
# Cleanup, version 3

# Warning:
# -------
# This script uses quite a number of features that will be explained later on.
# By the time you've finished the first half of the book, there should be nothing mysterious about it.

LOG_DIR=/var/log
ROOT_UID=0                                              # Only users with $UID 0 have root privileges.
LINES=50                                                # Default number of lines saved.
E_XCD=86                                                # Can't change directory?
E_NOTROOT=87                                            # Non-root exit error.

# Run as root, of course.
if [ "$UID" -ne "$ROOT_UID" ]
then
    echo "Must be root to run this script."
    exit $E_NOTROOT
fi

if [ -n "$1" ]
then
    # Test whether command-line argument is present (non-empty).
    lines=$1
else
    lines=$LINES # Default, if not specified on command-line.
fi

# Stephane Chazelas suggests the following, as a better way of checking command-line arguments,
# but this is still a bit advanced for this stage of the tutorial.
#
# E_WRONGARGS=85 # Non-numerical argument (bad argument format).
#
# case "$1" in
# "" ) lines=50;;
# *[!0-9]*) echo "Usage: `basename $0` lines-to-cleanup";
# exit $E_WRONGARGS;;
# * ) lines=$1;;
# esac
#
#* Skip ahead to "Loops" chapter to decipher all this.

cd $LOG_DIR
if [ `pwd` != "$LOG_DIR" ] # or if [ "$PWD" != "$LOG_DIR" ]
# Not in /var/log?
then
    echo "Can't change to $LOG_DIR."
    exit $E_XCD
fi # Doublecheck if in right directory before messing with log file.

# Far more efficient is:
#
# cd /var/log || {
# echo "Cannot change to necessary directory." >&2
# exit $E_XCD;
# }

tail -n $lines messages > mesg.temp                                 # Save last section of message log file.
mv mesg.temp messages                                               # Rename it as system log file.
                                                                    # cat /dev/null > messages  No longer needed, as the above method is safer.
cat /dev/null > wtmp                                                # ': > wtmp' and '> wtmp' have the same effect.
echo "Log files cleaned up."
# Note that there are other log files in /var/log not affected by this script.

exit 0
# A zero return value from the script upon exit indicates success to the shell.
```

检测参数个数
```
E_WRONG_ARGS=85
script_parameters="-a -h -m -z"                                     # -a = all, -h = help, etc.

if [ $# -ne $Number_of_expected_args ]
then
    echo "Usage: `basename $0` $script_parameters"
    # `basename $0` is the script's filename.
    exit $E_WRONG_ARGS
fi
```

## 调用脚本

调用脚本前需要确保脚本文件可读可执行
```bash
# 每个人可读可执行
$ chmod 555 script_name

# 每个人可读可执行
$ chmod +rx script_name

# 脚本拥有者可读可执行
$ chmod u+rx script_name
```

调用脚本
```bash
$ sh script_name

$ bash script_name

$ ./script_name
```

脚本移动到 PATH 路径下，确保全局可执行
```bash
$ mv script_name /usr/local/bin

$ script_name
```

## 特殊字符

### 注释符

`#` 表示单行注释，后面的内部将被作为注释不予执行，但 `#!` 是个例外

`#` 可以嵌入到管道符中进行注释
```bash
initial=( `cat "$startfile" | sed -e '/#/d' | tr -d '\n' |\
# Delete lines containing '#' comment character.
sed -e 's/\./\. /g' -e 's/_/_ /g'` )
# Excerpted from life.sh script
```

被转义的 `#` 不是注释
```bash
echo "The # here does not begin a comment."
echo 'The # here does not begin a comment.'

echo The \# here does not begin a comment.
echo The # here begins a comment.

echo ${PATH#*:} # Parameter substitution, not a comment.

echo $(( 2#101011 )) # Base conversion, not a comment.
# Thanks, S.C.
```

### 命令分隔符

一行多个命令使用分号分隔，注意分号后要加一个空格
```bash
if [ -x "$filename" ]; then                                         # 注意分号后都有个空格
    echo "File $filename exists."; cp $filename $filename.bak
else
    echo "File $filename not found."; touch $filename
fi; echo "File test complete."
```

### case 语句终止符

使用两个分号 `;;` 或者 `;;&` 或者 `;&`
```bash
case "$variable" in
    abc) echo "\$variable = abc" ;;
    xyz) echo "\$variable = xyz" ;;
esac
```

### 加载脚本文件

使用`.`，等价于 source，两者的效果相同，都相当于 C 语言中的 `#include`
```bash
. data-file                                                         # Load a data file.  Same effect as "source data-file", but more portable.
```

### 部分引用

双引号

### 全部引用

单引号

### 逗号

链接一系列数学运算，每一个都被计算，但只有最后一项的值被返回
```bash
let "t2 = ((a = 9, 15 / 3))"
# Set "a = 9" and "t2 = 15 / 3"
```

链接字符串
```bash
for file in /{,usr/}bin/*calc                                       # Find all executable files ending in "calc" in /bin and /usr/bin directories.
do
    if [ -x "$file" ]
    then
        echo $file
    fi
done

# /bin/ipcalc
# /usr/bin/kcalc
# /usr/bin/oidcalc
# /usr/bin/oocalc
# Thank you, Rory Winston, for pointing this out.
```

### 小写转换

'' '

### 转义字符

\

### 路径分隔符

`/`

### 命令执行

反引号 

### 冒号

空命令，等价于 true，返回值也是 true，等价于 NOP，do Nothing OPeration

返回值为0，表示正常返回
```bash
:
echo $?                                                     # 0
```

作为循环条件
```bash
while :                                                     # 等同于 while true
do
    operation-1
    operation-2
    ...
    operation-n
done
```

作为if then中的占位符
```bash
if condition
then :                                                      # Do nothing and branch ahead
else
    take-some-action
fi
```

作为二进制操作占位符
```bash
: ${username=`whoami`}                                      # 没有前面的冒号则出错，除非 username 是个命令或内置函数

: ${1?"Usage: $0 ARGUMENT"}                                 # From "usage-message.sh example script.
```

here Document 中的命令占位符
```bash
#!/bin/bash

: <<TESTVARIABLES
${HOSTNAME?}${USER?}${MAIL?}                                # Print error message if one of the variables not set.
TESTVARIABLES

exit $?
```

检测字符串变量
```bash
: ${HOSTNAME?} ${USER?} ${MAIL?}                            # Prints error message if one or more of essential environmental variables not set.
```

和重定向符 `>` 一起使用将清空文件而无需更改文件权限，文件不存在时则创建
```bash
: > data.xxx                                                # File "data.xxx" now empty.  Same effect as cat /dev/null >data.xxx
                                                            # However, this does not fork a new process, since ":" is a builtin.
```

和追加重定向符 `>>` 一起使用时，对于已存在的文件无作用，不存在的文件将创建
```bash
: >> target_file
```

可以作为函数名，目的是为了混淆代码
```bash
:()
{
    echo "The name of this function is "$FUNCNAME" "
}

: # The name of this function is :
```
* 可能最近的发行版都不支持，建议使用 _ 替代

作为非空函数的占位符
```bash
not_empty ()
{
    :                                                       # Contains a : (null command), and so is not empty.
}
```

## 控制字符

控制字符可以改变终端行为或文本显示，也可以用八进制或十六进制来表示

控制字符通常不在脚本文件中使用

Ctrl + a            移动光标到命令行首
Ctrl + e            移动光标到命令行尾
Ctrl + b            向左移动光标，相当于左方向键
Ctrl + f            向右移动光标，相当于右方向键

Ctrl + s            挂起
Ctrl + q            还原挂起
Ctrl + c            终止前端任务
Ctrl + z            暂停一个前台任务
Ctrl + d            注销当前shell

Ctrl + d            删除光标右侧字符，相当于 del 键，如果光标所在行没有字符时，则从当前 shell 注销，相当于 exit，或者在图形环境关闭当前窗口
Ctrl + h            删除光标左侧字符，相当于 backspace 键
Ctrl + w            向后按词删除
Ctrl + u            从光标当前位置删除到行首
Ctrl + k            从光标当前位置删除到行尾

Ctrl + g            在早先老的电传打字机会响铃
Ctrl + i            水平 tab
Ctrl + j            新行，脚本中也可以用八进制 `\012` 或十六进制 `\x0a`

Ctrl + l            清屏，和 `cls` 效果一样
Ctrl + n            从历史缓冲区中删除一行命令文本
Ctrl + p            从历史缓冲区中调用最后一个命令
Ctrl + r            在历史缓冲区中向后查找

Ctrl + o            新行
Ctrl + m            回车
Ctrl + t            交换光标处两个字符的位置

Ctrl + v            在编辑器中输入控制字符时使用，如要输入 Ctrl + h，应先按 Ctrl + v 键，再按 Ctrl + h 键

Ctrl + x            剪切高亮文本
Ctrl + y            粘贴先前删除的文本

## 变量和参数

### 变量替换

在 bash 中，从变量名获取变量值称为变量替换 variable substitution

使用`$`进行变量替换，变量在声明和分配内存空间时，不使用 `$` 符

双引号不影响变量替换，称为部分引用或弱引用；单引号会影响变量替换，会按字面形式输出，称为全部引用或强引用。

放在引号中的变量空格会保留
```
var="A B  C   D"
echo $var       #A B C D
echo "$var"     #A B  C   D
```

`$variable` 是 `$(variable)` 的简化形式，如果某种情况下前面的形式出错，可以使用后面的形式试下。

### 变量赋值

根据上下文，`=` 既可以是赋值运算符，也可以是测试运算符

作为测试运算符，测试字符串是否相等，如
```bash
test str1=str2
```

作为赋值运算符，`=` 前后都不能有空格
```bash
variable=value

variable =value

variable= value
```
* 第二种写法会被 bash 认为在调用一个名为 `variable` 的命令，其参数是 `=value`
* 第三种写法会被 bash 认为在调用一个名为 `value` 的命令，且设置了一个环境变量 `variable` 其值为空

未初始化变量的值是 null，不是 0
```bash
if [ -z "$unassigned" ]
then
    echo "\$unassigned is NULL."
fi # $unassigned is NULL.
```

未初始化变量参与数学运算时作为 0
```bash
echo "$uninitialized"                       # 未初始化，输出空行
let "uninitialized += 5"                    # 加 5
echo "$uninitialized"                       # 输出 5
```

几种初始化情况
```bash
#!/bin/bash

# 直接赋值
a=879
echo "The value of \"a\" is $a."

# 使用 let 赋值
let a=16+5
echo "The value of \"a\" is now $a."

# 在 loop 中隐含的赋值
for a in 7 8 9 11
do
    echo -n "$a "
done

# 通过 read 赋值
read a
echo "The value of \"a\" is now $a."

# 通过反引号将命令的执行结果赋值给变量 a
a=`ls -l`
echo $a             # 将去除所有空白
echo "$a"           # 将保留所有空白，所以把变量放入双引号中可以更好的保留输出格式
echo '$a'           # 单引号内变量不作解析

# 通过 $(...) 机制将命令的执行结果赋值给变量 a
a=$(uname -a)
echo "$a"

exit 0
```

## 数据类型

bash 的变量没有区分数据类型，这种特性既可以让你更容易的编写代码，也会忽略一些儿细微的错误和造成粗心的编程习惯

bash 的变量实际上都是字符串类型，但允许进行比较和数学运算，只有纯数字的字符串才可以进行数学运算

```bash
#!/bin/bash

a=2334                          # Integer.
let "a += 1"
echo "a = $a " # a = 2335
echo # Integer, still.

b=${a/23/BB} # Substitute "BB" for "23".
# This transforms $b into a string.
echo "b = $b" # b = BB35
declare -i b # Declaring it an integer doesn't help.
echo "b = $b" # b = BB35
let "b += 1" # BB35 + 1
echo "b = $b" # b = 1
echo # Bash sets the "integer value" of a string to 0.
c=BB34
echo "c = $c" # c = BB34
d=${c/BB/23} # Substitute "23" for "BB".
# This makes $d an integer.
echo "d = $d" # d = 2334
let "d += 1" # 2334 + 1
echo "d = $d" # d = 2335
echo
# What about null variables?
e='' # ... Or e="" ... Or e=
echo "e = $e" # e =
let "e += 1" # Arithmetic operations allowed on a null variable?
echo "e = $e" # e = 1
echo # Null variable transformed into an integer.
# What about undeclared variables?
echo "f = $f" # f =
let "f += 1" # Arithmetic operations allowed?
echo "f = $f" # f = 1
echo # Undeclared variable transformed into an integer.
#d=${c/BB/23} # Substitute "23" for "BB".
# This makes $d an integer.
echo "d = $d" # d = 2334
let "d += 1" # 2334 + 1
echo "d = $d" # d = 2335
echo
# What about null variables?
e='' # ... Or e="" ... Or e=
echo "e = $e" # e =
let "e += 1" # Arithmetic operations allowed on a null variable?
echo "e = $e" # e = 1
echo # Null variable transformed into an integer.
# What about undeclared variables?
echo "f = $f" # f =
let "f += 1" # Arithmetic operations allowed?
echo "f = $f" # f = 1
echo # Undeclared variable transformed into an integer.
#
# However ...
let "f /= $undecl_var" # Divide by zero?
# let: f /= : syntax error: operand expected (error token is " ")
# Syntax error! Variable $undecl_var is not set to zerd=${c/BB/23} # Substitute "23" for "BB".
# This makes $d an integer.
echo "d = $d" # d = 2334
let "d += 1" # 2334 + 1
echo "d = $d" # d = 2335
echo
# What about null variables?
e='' # ... Or e="" ... Or e=
echo "e = $e" # e =
let "e += 1" # Arithmetic operations allowed on a null variable?
echo "e = $e" # e = 1
echo # Null variable transformed into an integer.
# What about undeclared variables?
echo "f = $f" # f =
let "f += 1" # Arithmetic operations allowed?
echo "f = $f" # f = 1
echo # Undeclared variable transformed into an integer.
#
# However ...
let "f /= $undecl_var" # Divide by zero?
# let: f /= : syntax error: operand expected (error token is " ")
# Syntax error! Variable $undecl_var is not set to zero here!
#
# But still ...
let "f /= 0"
# let: f /= 0: division by 0 (error token is "0")
# Expected behavior.
# Bash (usually) sets the "integer value" of null to zero
#+ when performing an arithmetic operation.
# But, don't try this at home, folks!
# It's undocumented and probably non-portable behavior.
# Conclusion: Variables in Bash are untyped,
#+ with all attendant consequences.
exit $?o here!
#
# But still ...
let "f /= 0"
# let: f /= 0: division by 0 (error token is "0")
# Expected behavior.
# Bash (usually) sets the "integer value" of null to zero
#+ when performing an arithmetic operation.
# But, don't try this at home, folks!
# It's undocumented and probably non-portable behavior.
# Conclusion: Variables in Bash are untyped,
#+ with all attendant consequences.
exit $?
# However ...
let "f /= $undecl_var" # Divide by zero?
# let: f /= : syntax error: operand expected (error token is " ")
# Syntax error! Variable $undecl_var is not set to zero here!
#
# But still ...
let "f /= 0"
# let: f /= 0: division by 0 (error token is "0")
# Expected behavior.
# Bash (usually) sets the "integer value" of null to zero
#+ when performing an arithmetic operation.
# But, don't try this at home, folks!
# It's undocumented and probably non-portable behavior.
# Conclusion: Variables in Bash are untyped,
#+ with all attendant consequences.
exit $?
```

## 特殊变量类型

本地变量 : 代码块或方法中的变量

环境变量 : 影响 shell 或用户界面行为的变量，像其他进程一样，shell 启动时会建立自己的环境变量，所有的子进程会继承 shell 的环境变量，分配给环境变量的空间时有限制的，环境变量占用太多空间会导致问题

```bash
bash$ eval "`seq 10000 | sed -e 's/.*/export var&=ZZZZZZZZZZZZZZ/'`"
bash$ du

bash: /usr/bin/du: Argument list too long
Note: this "error" has been fixed, as of kernel version 2.6.23.
```

### 位置参数

通过命令行传递给脚本文件的可选参数，按顺序分别是 : `$0, $1, $2......${10},${11}`
* `$0` 是脚本文件名，可以通过符号链接对同一个脚本文件建立多个不同的文件名，然后脚本中通过文件名参数来执行不同的操作
* `$1` 是第一个参数，`$2` 是第二个参数……依次类推，9 以后的参数要使用大括号括起来
* `$*` 和 `$@` 包含了除 `$0` 外的所有参数
* `$#` 是参数的数量
* `$?` 是上一个命令的返回值
* `$!` 是上一个后台命令的PID
* `$$` 当前脚本进程的PID

同一个文件根据调用时不同的文件名来执行不同的操作
```bash
#!/bin/bash
# Does a 'whois domain-name' lookup on any of 3 alternate servers:
# 需要先建立符号链接
# ln -s /usr/local/bin/wh /usr/local/bin/wh-ripe
# ln -s /usr/local/bin/wh /usr/local/bin/wh-apnic
# ln -s /usr/local/bin/wh /usr/local/bin/wh-tucows

E_NOARGS=75

if [ -z "$1" ]
then
    echo "Usage: `basename $0` [domain-name]"
    exit $E_NOARGS
fi

# 根据脚本文件名调用不同的 server
case `basename $0` in                               # Or: case ${0##*/} in
    "wh" ) whois $1@whois.tucows.com;;
    "wh-ripe" ) whois $1@whois.ripe.net;;
    "wh-apnic" ) whois $1@whois.apnic.net;;
    "wh-cw" ) whois $1@whois.cw.net;;
    * ) echo "Usage: `basename $0` [domain-name]";;
esac

exit $?
```

直接引用变量
```bash
if [ -n "$1" ]                  # 被测试的变量如果没有被引号括起来，则判断是否为空的运行结果不正确
then
    echo $1
else
    echo Empty String
fi
```

间接引用变量，如获取最后一个参数的值
```bash
argcnt=$#                       # 参数个数
echo $argcnt                    # 参数个数为 2
echo ${!2}                      # 无法正确显示
echo ${!$#}                     # 错误，提示 bad substitution
echo ${!argcnt}                 # 正确显示
echo ${!#}                      # 正确显示
```

如果脚本文件需要参数，但是调用时没有传递参数，则接受参数的变量会被赋值 NULL，结果可能不正确，应对这种情况的 3 种方法
```bash
# 第1种，对每个参数作判断
if [ -z $1 ]
then
    exit $E_MISSING_POS_PARAM
fi

# 第2种，在赋值操作两边添加额外的字符，副作用是变量不能以下划线开头
arg_=$1_                        # 原来的是 variable1=$1，这里各添加了下划线，
arg=${arg_/_/}                  # 再把多余的字符剔除
echo $arg

# 第3种，使用参数替代，推荐使用，后面章节介绍
${1:-$DefaultVal}
```

### shift

shift 命令把位置参数从右向左移动，但保持 `$0` 不变，即 `$1 <--- $2, $2 <--- $3, $3 <--- $4 ...`
```bash
$ cat test.sh
echo "#1 = $1"
echo "#2 = $2"
echo "#3 = $3"

echo "---"
shift

echo "#1 = $1"
echo "#2 = $2"
echo "#3 = $3"

echo "---"
shift

echo "#1 = $1"
echo "#2 = $2"
echo "#3 = $3"

$ ./test.sh f d e
#1 = f
#2 = d
#3 = e
---
#1 = d
#2 = e
#3 =
---
#1 = e
#2 =
#3 =
```

可以指定一次 shift 移动几个参数
```bash
$ cat test.sh
echo "#1 = $1"
echo "#2 = $2"
echo "#3 = $3"

echo "---"
shift 2

echo "#1 = $1"
echo "#2 = $2"
echo "#3 = $3"

$ ./test.sh f d e
#1 = f
#2 = d
#3 = e
---
#1 = e
#2 =
#3 =
```

依次访问参数
```bash
until [ -z "$1" ]
do
    echo -n "$1 "
    shift
done
```

如果一次 shift 的个数超过参数总数，则会进入死循环，如
```bash
$ cat test.sh
until [ -z "$1" ]
do
    echo -n "$1 "
    shift 20
done

$ ./test.sh 12
12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 12 ...
```

上面的代码可以通过加入 break 来修正
```bash
$ cat test.sh
until [ -z "$1" ]
do
    echo -n "$1 "
    shift 20 || break           # break
done

$ ./test.sh 12
12
```

## 引用

日常写作中，使用引号引起来的短语表示有特殊含义，如强调等，但在 bash 中，使用引号引起来的短语是为了保持原样原含义
如在通配符和正则表达式里特殊的符号可以通过引号来仅表示字面含义
```bash
$ ls x1*
x1c_2013.jpg  x1c_2015.jpg  x1c_2016.jpg

$ ls 'x1*'
ls: cannot access 'x1*': No such file or directory

$ ls "x1*"
ls: cannot access 'x1*': No such file or directory
```

一些儿程序会把引号里的一些儿特殊字符展开和重新解析，所以引号的一个重要作用是保护 shell 命令行参数
```bash
$ grep [Ff]irst *.txt
1.txt:This is the first line of file1.txt
2.txt:This is the First line of file2.txt

$ grep '[Ff]irst' *.txt                             # 引号中内容被 grep 重新解析
1.txt:This is the first line of file1.txt
2.txt:This is the First line of file2.txt

$ grep "[Ff]irst" *.txt                             # 引号中内容被 grep 重新解析
1.txt:This is the first line of file1.txt
2.txt:This is the First line of file2.txt
```

引用还可以保持原格式
```bash
$ echo $(ls -l)
total 1488150 drwxr-xr-x 1 ssl 197121 0 十二 19 2012 $RECYCLE.BIN/ -rw-r--r-- 1 ssl 197121 4898232 六月 8 2015 [逆向法巧学英语第三版].钟道隆.扫描版.pdf -rw-r--r-- 1 ssl 197121 518491 六月 8 2015 [英语学习逆向法].钟道隆.文字版.pdf -rw-r--r-- 1 ssl 197121 37 九月 23 09:57 1.txt -rw-r--r-- 1 ssl 197121 37 九月 23 09:57 2.txt -rw-r--r-- 1 ssl 197121 10298 六月 24 10:19 7ed24cdf05471016_1.jpg

$ echo "$(ls -l)"
total 1488150
drwxr-xr-x 1 ssl 197121          0 十二 19  2012 $RECYCLE.BIN/
-rw-r--r-- 1 ssl 197121         37 九月 23 09:57 1.txt
-rw-r--r-- 1 ssl 197121         37 九月 23 09:57 2.txt
-rw-r--r-- 1 ssl 197121      10298 六月 24 10:19 7ed24cdf05471016_1.jpg
-rw-r--r-- 1 ssl 197121    6556323 七月 21 16:46 Aligning text with Tabular.vim.ogv
drwxr-xr-x 1 ssl 197121          0 九月 18 11:00 BaiduNetdiskDownload/
-rw-r--r-- 1 ssl 197121      26569 三月  8  2015 blowfish.JPG
drwxr-xr-x 1 ssl 197121          0 九月 16 12:10 Documents/
-rw-r--r-- 1 ssl 197121      98957 七月  5 11:02 E42_ludashi.jpg
```

引用一个变量时，建议用双引号引起来，这样除了美元符`$`, 反斜线 \, 反引号 之外的所有特殊字符都不被重新解析
```bash
$ cat test.sh
var="'(]\\{}\$\"\`"

echo $var           # '(]\{}$"`
echo "$var"         # '(]\{}$"`

IFS='\'             # Internal Field Separator，字段分隔符，默认为空白符，包括空格、制表符、换行符
echo $var           # '(] {}$"` \ converted to space. Why?
echo "$var"         # '(]\{}$"`
```

引号还阻止了拆分单词
```bash
$ cat test.sh
fruits="apple banana orange"
for f in $fruits                        # 唯一区别是这里是否使用双引号
do
    echo "$f"
done

$ ./test.sh
apple
banana
orange

#----------------------------

$ cat test.sh
fruits="apple banana orange"
for f in "$fruits"                      # 唯一区别是这里是否使用双引号
do
    echo "$f"
done

$ ./test.sh
apple banana orange
```

## 转义字符



## exit

exit 用来退出 bash 脚本，其返回码可以被父进程获取，每个 bash 命令都有一个返回码，正常退出的返回码为 0，非正常退出返回 1~255 常被解析为错误码

bash 脚本中的 exit 退出时若没有指定返回码则返回的是最后一个命令的返回码，即省略 exit 或者直接写 exit 都等价于 `exit $?`，这就是 bash 方法的返回值

```bash
$ cat test.sh
echo a
exit 9

$ ./test.sh
a

$ echo $?
9
```
