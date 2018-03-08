---
title: bash - linux shell 脚本攻略 - chapter 4.6 - awk
categories: 
 - Linux
 - Bash
tags: 
 - awk
---

shell awk

<!--more-->

awk 是一款用于数据流的工具，可以对行和列进行操作，并有內建的函数、数组等

## 工作原理

awk 脚本的结构如下：

`awk 'BEGIN{ print "start" } pattern { commands } END{ print "end" }' file`
`echo someinput | awk 'BEGIN{ print "start" } pattern { commands } END{ print "end" }'`
* awk 可从文件或 stdin 读入数据
* awk 的脚本包含在**单**引号中，经测试双引号不可以
* awk 的脚本由 BEGIN {}, pattern {}, END {} 三个语句块组成
* BEGIN {} 和 pattern 和 END {} 都是可选的，唯一必选的部分是 pattern 后的大括号括起来的语句块，即最简形式：awk '{ some commands ... }'
* BEGIN 和 END 必须大写，pattern 为正则表达式，如 /linux/ 匹配包含 linux 的字符串

执行过程：
1. 先执行 BEGIN {} 语句块
2. 然后从输入的数据流（文件或stdin）读取一行，执行 pattern {} 语句块……重复执行直到数据流末尾
3. 最后执行 END {} 语句块

BEGIN {} 语句块常用于变量初始化、输出表格的表头等；
END {} 语句块常用于输出表尾，输出所有行的汇总信息等；
pattern {} 语句块是一个隐含的 while 循环，依次读取每一行并执行 {} 中的命令，其中 pattern 是可选的，没有 pattern 时则认为每一行都是匹配的，有 pattern 时则对每一行先测试是否匹配，匹配的才执行 {} 里面的语句块的语句

运行过程举例
```bash
-> seq 5 | awk 'BEGIN{ sum=0; print "Summation:" } { print $1"+"; sum+=$1 } END { print "=="; print sum }'
Summation:
1+
2+
3+
4+
5+
==
15
```

## 特殊变量

`NR` ：Number of Records，表示记录数量，在执行过程中对应于当前行号。
`NF` ：Number of Fields，表示字段数量，在执行过程中对应于当前行的字段数。可以使用 `print $NF` 打印最后一个字段，`print ($NF-1)` 打印倒数第二个字段
`$0` ：这个变量包含执行过程中当前行的文本内容。
`$1` ：这个变量包含第一个字段的文本内容。
`$2` ：这个变量包含第二个字段的文本内容。

输出第 2 和第 3 个字段
```bash
-> awk '{print $2,$3}' s1.txt
sAy hometown
hi beijing
who Are
where Are
i Am
```

打印文件的行数
```bash
-> awk 'END {print NR}' s1.txt
5
```

综合举例
```bash
-> echo -e "line1 f2 f3\nline2 f4 f5\nline3 f6 f7" | awk '{print "Line no:"NR",No of fields:"NF, "$0="$0, "$1="$1,"$2="$2,"$3="$3}'
Line no:1,No of fields:3 $0=line1 f2 f3 $1=line1 $2=f2 $3=f3
Line no:2,No of fields:3 $0=line2 f4 f5 $1=line2 $2=f4 $3=f5
Line no:3,No of fields:3 $0=line3 f6 f7 $1=line3 $2=f6 $3=f7
```

## 使用外部变量的值

`-v` 选项使用外部变量，只能赋值一个变量
```bash
-> out=100; echo | awk -v var=$out '{ print "the out value is " var }'
the out value is 100
```

另一种不使用 `-v` 选项给多个变量赋值的方式
```bash
-> out=100;echo | awk '{ print var,val }' var=$out val=$out
100 100
```

从文件输入  ??? 么有测试成功，不知道要实现什么
```bash
-> awk '{ print v1,v2 }' v1=$var1 v2=$var2 1.txt
```

## getline 读取行

awk 默认读取文件的所有行，getline 可以读取某一行

getline var 把行的内容写入 var
```bash
-> seq 5 | awk 'BEGIN { getline var; print "Read ahead first line", var }'
Read ahead first line 1
```

不带参数的 getline 则可以使用 `$0 $1 $2` 访问文本行的内容
```bash
-> seq 5 | awk 'BEGIN { getline; print "Read ahead first line", $0 } { print $0 }'
Read ahead first line 1
2
3
4
5
```

## 过滤要处理的行

使用 pattern 来过滤

行数小于 5 的行
```bash
-> awk 'BEGIN { FS=":" } NR<5 {print $1}' /etc/passwd
root
bin
daemon
adm
```

行号在 1 到 3 之间的行
```bash
-> awk 'BEGIN { FS=":" } NR==1,NR==3 {print $1}' /etc/passwd
root
bin
daemon
```

包含 mysql 和不包含 mysql 的行
```bash
-> awk 'BEGIN {} /mysql/ {print}' /etc/passwd
mysql:x:27:27:MySQL Server:/var/lib/mysql:/bin/false

-> awk 'BEGIN {} !/mysql/ {print}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
.................................
```

## 设置字段定界符

awk 默认的字段定界符是空格，使用 `-F` 来自定义定界符
```bash
-> awk -F ":" '{ print $NF }' /etc/passwd
/bin/bash
/sbin/nologin
/sbin/nologin
/sbin/nologin
/sbin/nologin
............
```

或者在 BEGIN 语句块中使用 FS="delimiter" 来设定定界符
```bash
-> awk 'BEGIN { FS=":" } { print $NF }' /etc/passwd
/bin/bash
/sbin/nologin
/sbin/nologin
/sbin/nologin
/sbin/nologin
```

## 读取命令输出

把命令的输出结果读入变量 cmdout 的方法
```bash
"command" | getline cmdout;
```

举例
```bash
-> echo | awk '{ "grep root /etc/passwd" | getline cmdout; print cmdout }'
root:x:0:0:root:/root:/bin/bash

-> awk '{ "grep root /etc/passwd" | getline cmdout; print cmdout }'
```
* echo 输出一个空行，没有这个空行则 awk 缺少输入数据，结果就是不执行
* cmdout 包含了命令 "grep root /etc/passwd" 的输出结果，注意命令由双引号包括，换单引号也不行

## 使用循环

```bash
for (i=0; i<10; i++) { print $i; }

for (i in array) { print array[i]; }
```

## 內建的字符串函数

`length(string)` 返回字符串 string 长度

`index(string,search_string)` 返回 search_string 在字符串 string 中出现的位置

`split(string,array,delimiter)` 用定界符 delimiter 把字符串 string 生成数组存入数组 array

`substr(string,start_position,end_position)` 返回子字符串

`sub(regex, replece_str, string)` 把正则表达式 regex 在字符串 string 里的第一个匹配替换为 replace_str

`gsub(regex, replece_str, string)` 把正则表达式 regex 在字符串 string 里的所有匹配替换为 replace_str

`match(regex,string)` 测试正则表达式是否匹配字符串，两个特殊变量 RSTART, RLENGTH，分别表示正则表达式所匹配内容的起始位置和长度
