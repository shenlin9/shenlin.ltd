---
title: bash - linux shell 脚本攻略 - chapter 4 - 文本
categories: bash
tags: bash
---

shell 文本操作

<!--more-->

## 正则

|元字符|描 述|示 例|
|---|---|---|
|^|行起始标记|^tux 匹配以tux起始的行|
|$|行尾标记|tux$ 匹配以tux结尾的行|
|.|匹配任意一个字符|Hack. 匹配Hackl和Hacki，但是不能匹配Hackl2和Hackil，它只能匹配单个字符|
|[]|匹配包含在 [字符] 之中的任意一个字符|coo[kl] 匹配cook或cool|
|[^]|匹配除 [^字符] 之外的任意一个字符|9[^01] 匹配92、93，但是不匹配91或90|
|[-]|匹配 [] 中指定范围内的任意一个字符|[1-5] 匹配从1～5的任意一个数字|
|?|匹配之前的项1次或0次|colou?r 匹配color或colour，但是不能匹配colouur|
|+|匹配之前的项1次或多次|Rollno-9+ 匹配Rollno-99、Rollno-9，但是不能匹配Rollno-|
|*|匹配之前的项0次或多次|co*l  匹配cl、col、coool等|
|()|创建一个用于匹配的子串|ma(tri)?x 匹配max或maxtrix|
|{n}|匹配之前的项n次|[0-9]{3} 匹配任意一个三位数，[0-9]{3}可以扩展为 [0-9][0-9][0-9]|
|{n,}|之前的项至少需要匹配n次|[0-9]{2,} 匹配任意一个两位或更多位的数字|
|{n,m}|指定之前的项所必需匹配的最小次数和最大次数|[0-9]{2,5} 匹配从两位数到五位数之间的任意一个数字|
|&#124;|交替——匹配两边的任意一项|Oct (1st &#124; 2nd) 匹配Oct 1st或Oct 2nd|
|\|转义符可以将上面介绍的特殊字符进行|转义a\.b 匹配a.b，但不能匹配ajb。通过在 . 之间加上前缀 \ ， 从而忽略了 . 的特殊意义|

JavaScript 可视化正则工具：
https://jex.im/regulex/
https://regexper.com/


## cut 4.4

cut 是把文本按列进行切分的工具，其默认的列分隔符是制表符（注意 vim 的 expandtab 选项会把制表符替换为空格）

cut 可以按字段 `-f`，字节 `-b`，字符 `-c` 切分

### 按字段切分

示例文件
```bash
-> cat s2.txt
key	say	hometown
111	hi	beijing
787	"wa ha ha"	handan
333	"who are you"	tianjin
aaa	"i am shenlin"	shanghai
```

`-f` field 按列号选择需要的列，列号从 1 开始，列号之间用逗号分隔
```bash
-> cut -f 1,3 s2.txt
key	hometown
111	beijing
787	handan
333	tianjin
aaa	shanghai
```

接受从 stdin 输入
```bash
-> cat s2.txt | cut -f 1
key
111
787
333
aaa
```

`--complement` 输出除了第3列之外的所有列
```bash
-> cut -f 3 --complement s2.txt
key	say
111	hi
787	"wa ha ha"
333	"who are you"
aaa	"i am shenlin"
```

`-d` 设定列的分隔符
```bash
-> echo -e "a;b;c;d" | cut -d";" -f 2,3
b;c
```

### 按字符、字节提取

字符、字节切分

`-c` 按字符截取，`-b` 按字节截取，开始和结束之间用连字符分隔
```bash
-> cat s3.txt
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxyz
abcdefghijklmnopqrstuvwxyz
这是一个中文的字符串
这是一个中文的字符串
这是一个中文的字符串

-> cut -b 1-4 s3.txt
abcd
abcd
abcd
这
这
这

-> cut -c 1-4 s3.txt
abcd
abcd
abcd
这是一个
这是一个
这是一个
```

`-n` 前 n 个字符，`n-` 从第 n 个字符开始到结尾
```bash
-> cut -c -2 s3.txt
ab
ab
ab
这是
这是
这是

-> cut -c 2- s3.txt
bcdefghijklmnopqrstuvwxyz
bcdefghijklmnopqrstuvwxyz
bcdefghijklmnopqrstuvwxyz
是一个中文的字符串
是一个中文的字符串
是一个中文的字符串
```

用 `-b` 或 `-c` 提取多个字段时，必须使用 `--output-delimiter` 指定输出定界符，否则无法分清两个字段
```bash
-> cut -c 2-4,8-10 s3.txt
bcdhij
bcdhij
bcdhij
是一个字符串
是一个字符串
是一个字符串

-> cut -c 2-4,8-10 --output-delimiter=","  s3.txt
bcd,hij
bcd,hij
bcd,hij
是一个,字符串
是一个,字符串
是一个,字符串
```

## sed

sed , stream editor 流编辑器

### s/pattern/replaced/g 替换匹配项

sed 常用功能是进行文本替换，其替换语法和 vi 很像

示例文件
```bash
-> cat s1.txt
key say hometown
aaa hi  beijing
bbb who are you tianjin
ccc where are you from
ddd i am shenlin    shanghai
```

替换文件
```bash
-> sed "s/a/A/" s1.txt
key sAy hometown
Aaa hi  beijing
bbb who Are you tianjin
ccc where Are you from
ddd i Am shenlin    shanghai
```

替换 stdin
```bash
-> cat s1.txt | sed 's/w/W/'
key say hometoWn
aaa hi  beijing
bbb Who are you tianjin
ccc Where are you from
ddd i am shenlin    shanghai
```

默认 sed 只输出替换结果，`-i` 则可以替换并保存文件的更改
```bash
-> sed -i "s/a/A/" s1.txt

-> cat s1.txt
keY sAy hometown
Aaa hi  beijing
bbb who Are You tianjin
ccc where Are You from
ddd i Am shenlin    shanghai

# 相当于下面两条命令
-> sed 's/y/Y/' s1.txt >news1.txt
-> mv news1.txt s1.txt
```

更改源文件前保留一份备份 ??? 未测试成功
```bash
sed –i .bak 's/abc/def/' file

-> sed -ci "s/abc/def/" s1.txt      # 复制了一份文件
```

默认替换时只替换每行的第一个匹配，参数尾部加上 `g` 则替换所有内容， `Ng` 则表示从第 N 个匹配出开始替换
```bash
-> sed "s/a/A/g" s1.txt
keY sAy hometown
AAA hi  beijing
bbb who Are You tiAnjin
ccc where Are You from
ddd i Am shenlin    shAnghAi

-> echo "hi beijing" | sed "s/i/I/2g"
hi beIjIng
```

sed 的默认定界符是 `/`，但也可以使用任意的定界符，如
```bash
-> echo "hi beijing" | sed "s:i:I:2g"
hi beIjIng

-> echo "hi beijing" | sed "s|i|I|2g"
hi beIjIng

-> echo "hi beijing" | sed "s?i?I?2g"
hi beIjIng
```

定界符是要替换内容的一部分时，需要使用 \ 转义
```bash
-> echo "hi,you are good?" | sed "s?d\??D?"
hi,you are gooD
```

### /pattern/d 删除匹配

删除空白行，空白行是行首标记紧随行尾标记，即 `^$` 表示空白行
```bash
-> cat s1.txt
keY sAy hometown

Aaa hi  beijing
bbb who Are You tianjin


ccc where Are You from

ddd i Am shenlin    shanghai

-> sed '/^$/d' s1.txt
keY sAy hometown
Aaa hi  beijing
bbb who Are You tianjin
ccc where Are You from
ddd i Am shenlin    shanghai
```

### 已匹配字符串

sed 中用 `&` 标记匹配的字符串

```bash
-> cat s1.txt
keY sAy hometown
Aaa hi  beijing
bbb who Are You tianjin
ccc where Are You from
ddd i Am shenlin    shanghai

-> sed "s/\w\+/[&]/" s1.txt
[keY] sAy hometown
[Aaa] hi  beijing
[bbb] who Are You tianjin
[ccc] where Are You from
[ddd] i Am shenlin    shanghai
```

### 匹配的子字符串

也称为向后引用（back reference），`\1` 匹配第一个子字符串 ，`\2` 匹配第二个 ，以此类推

```bash
-> echo This is digit 7 in a number | sed "s/digit \([0-9]\)/\1/"
This is 7 in a number

-> echo seven EIGHT | sed 's/\([a-z]\+\) \([A-Z]\+\)/\2 \1/'
EIGHT seven
```

### 组合多个表达式

三种组合多个表达式的方式

```bash
-> echo "hello who are you" | sed 's/who/i/' | sed 's/are/am/' | sed 's/you/jackie/'
hello i am jackie

-> echo "hello who are you" | sed -e 's/who/i/' -e 's/are/am/' -e 's/you/jackie/'
hello i am jackie

-> echo "hello who are you" | sed -e 's/who/i/;s/are/am/;s/you/jackie/'
hello i am jackie
```

### 引号引用

sed 中通常用单引号引用字符串，但也可以使用双引号，通常适用于需要解析变量时

```bash
-> var="world";echo "hello this is a world" | sed "s/$var/amazing world/"
hello this is a amazing world
```

## awk

awk 是一款用于数据流的工具，可以对行和列进行操作，并有內建的函数、数组等

### 工作原理

awk 脚本的结构如下：

`awk 'BEGIN{ print "start" } pattern { commands } END{ print "end" }' file`
`echo someinput | awk 'BEGIN{ print "start" } pattern { commands } END{ print "end" }'`
* awk 可从文件或 stdin 读入数据
* awk 的脚本包含在**单**引号中，经测试双引号不可以
* awk 的脚本由 BEGIN {}, pattern {}, END {} 三个语句块组成
* BEGIN {} 和 pattern 和 END {} 都是可选的，唯一必选的部分是 pattern 后的大括号括起来的语句块，即最简形式：awk '{ some commands ... }'
* BEGIN 和 END 必须大写，pattern 为正则表达式，如 /linux/ 匹配包含 linux 的字符串

执行过程：

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

### 特殊变量

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

### 使用外部变量的值

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

### getline 读取行

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

### 过滤要处理的行

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

### 设置字段定界符

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

### 读取命令输出

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

### 使用循环

```bash
for (i=0; i<10; i++) { print $i; }

for (i in array) { print array[i]; }
```

### 內建的字符串函数

`length(string)` 返回字符串 string 长度

`index(string,search_string)` 返回 search_string 在字符串 string 中出现的位置

`split(string,array,delimiter)` 用定界符 delimiter 把字符串 string 生成数组存入数组 array

`substr(string,start_position,end_position)` 返回子字符串

`sub(regex, replece_str, string)` 把正则表达式 regex 在字符串 string 里的第一个匹配替换为 replace_str

`gsub(regex, replece_str, string)` 把正则表达式 regex 在字符串 string 里的所有匹配替换为 replace_str

`match(regex,string)` 测试正则表达式是否匹配字符串，两个特殊变量 RSTART, RLENGTH，分别表示正则表达式所匹配内容的起始位置和长度

## 统计词频

## 压缩解压缩

## 按列合并多个文件

## 打印文件或行中的第 n 个单词或列

## 打印行或样式之间的文本

## 以逆序形式打印行

## 解析文本中的电子邮件地址和 URL

## 在文件中移除包含某个单词的句子

## 对目录中的所有文件进行文本替换

## 文本切片及参数操作
