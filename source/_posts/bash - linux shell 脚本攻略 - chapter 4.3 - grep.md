---
title: bash - linux shell 脚本攻略 - chapter 4.3 - grep 搜索文本
categories: 
 - Linux
 - Bash
tags: 
 - grep
---

shell 文本操作

egrep 是默认使用正则表达式的 grep

<!--more-->

注意参数顺序，要搜索的字符串在文件之前
```bash
-> grep "hi" 11.txt
-> grep hi 11.txt
111 hi  beijing

-> grep 11.txt "hi"
grep: hi: No such file or directory
```

从 stdin 接受数据
```bash
-> echo -e "hello where are you from\nyou are a stupid" | grep 'where'
hello where are you from
```

搜索多个文件
```bash
-> grep "where" *.txt
link.txt:444 where are you from
s1.txt:444 where are you from
```

使用颜色标记搜到的词
```bash
-> grep 'where' *.txt --color=auto
link.txt:444 where are you from
s1.txt:444 where are you from
```

只输出匹配到的具体文本而不是整行
```bash
-> grep -o 'where' s1.txt           
where

-> grep -o 'where' *.txt
link.txt:where
s1.txt:where
```

输出除了匹配行之外的所有行
```bash
-> grep -v 'where' s1.txt           
key say hometown
111 hi  beijing
333 who are you tianjin
aaa i am shenlin    shanghai
```

使用正则搜索
```bash
-> grep -E " wh[a-z]{3,} " *.txt    
-> egrep " wh[a-z]{3,} " *.txt      
link.txt:444 where are you from
s1.txt:444 where are you from
```
* E 表示 extended
* egrep 默认使用正则搜索

统计匹配行数
```bash
-> grep -c 'where' s1.txt
1

-> grep -c 'where' *.txt
11.txt:0
data.txt:0
link.txt:1
s1.txt:1
s2.txt:0
```
* 注意只是匹配行数，不是匹配次数，单行的多次匹配只计数一次

利用技巧实现统计匹配次数
```bash
-> echo -e "abc\nbcd\ndbe\n\efb" | grep -o "b"
b
b
b
b

-> echo -e "abc\nbcd\ndbe\n\efb" | grep -o "b" | wc -l
4
```

显示行号
```bash
-> grep -n "where" s1.txt
4:ccc where are you from

-> cat -n s1.txt | grep "where"
     4	ccc where are you from
```

显示匹配字符的偏移量
```bash
-> cat s1.txt
key say hometown
aaa hi  beijing
bbb who are you tianjin
ccc where are you from
ddd i am shenlin    shanghai

-> grep -bo "are" s1.txt
41:are
67:are

-> grep -b "are" s1.txt
33:bbb who are you tianjin
57:ccc where are you from
```
* -b 总是和 -o 搭配使用
* 只使用 -b 表示搭配所在行的偏移量
* 偏移量是从文件开头算起，而不是从匹配行算起

显示匹配的文本位于哪个文件中
```bash
-> grep -l "where" *.txt
link.txt
s1.txt
```

和 l 相反，返回没有匹配文本的文件列表
```bash
-> grep -L "where" *.txt
11.txt
base64.txt
data.txt
multiline.txt
s2.txt
test1.txt
test2.txt
```

递归搜索目录忽略大小写
```bash
-> grep "who" . -r *.txt                # -r 就代表递归搜搜目录，后面 *.txt 让 txt 文件又被搜索一遍
./s1.txt.orig:333 who are you tianjin   # 可见每个 txt 文件被搜索两次，所以这种用法是错的
./11.txt:333 who are you tianjin        
./s1.txt:bbb who are you tianjin
./s2.txt:333 who are you tianjin
./1.txt:who
./s.patch: 333 who are you tianjin
11.txt:333 who are you tianjin
1.txt:who
link.txt:bbb who are you tianjin
s1.txt:bbb who are you tianjin
s2.txt:333 who are you tianjin

-> grep "who" . -r                      # 正确写法
-> grep "who" *.txt


-> grep "who" . -R                      # -R 和 -r 的区别
./s1.txt.orig:333 who are you tianjin
./11.txt:333 who are you tianjin
./link.txt:bbb who are you tianjin      # -R 会追踪符号链接文件，这里有符号链接文件 link.txt 指向 s1.txt，两者的内容是一样的
./s1.txt:bbb who are you tianjin
./s2.txt:333 who are you tianjin
./1.txt:who
./s.patch: 333 who are you tianjin

-> grep "who" . -r                      # -r 不追踪符号链接文件，所以这里没有 link.txt
./s1.txt.orig:333 who are you tianjin
./11.txt:333 who are you tianjin
./s1.txt:bbb who are you tianjin
./s2.txt:333 who are you tianjin
./1.txt:who
./s.patch: 333 who are you tianjin

-> grep "are" -nr . 
./s1.txt.orig:3:333 who are you tianjin
./11.txt:4:333 who are you tianjin
./.git/hooks/commit-msg.sample:12:# Doing this in a hook is a bad idea in general, but the prepare-commit-msg
./.git/hooks/prepare-commit-msg.sample:21:# still be edited.  This is rarely a good idea.
./.git/info/exclude:2:# Lines that start with '#' are comments.
./.git/config:4:	bare = false
./s1.txt:3:bbb who are you tianjin
./s1.txt:4:ccc where are you from
./filestat.sh:8:declare -A statarray;
./s2.txt:3:333 who are you tianjin
./s.patch:7: 333 who are you tianjin
./s.patch:8:-444 where are you from
```
* 开发人员使用较多的命令，搜索目录中包含指定方法名的文件
* -n 显示行数 -r 递归
* -R 和 -r 都是递归搜索，区别是 -R 对于符号链接会追踪到原文件内容去查找匹配后输出符号链接的名字和匹配内容，而 -r 不追踪符号链接文件

忽略大小写
```bash
-> grep "WHO" -i s1.txt
bbb who are you tianjin
```

指定多个搜索匹配
```bash
-> grep -e "who" -e "you" -e "I" -o  s1.txt
who
you
you
```

把一个文件的每一行作为匹配模式进行搜索
```bash
-> cat 1.txt
who
shen

-> grep -f 1.txt s1.txt
bbb who are you tianjin
ddd i am shenlin    shanghai

-> grep -of 1.txt s1.txt
who
shen
```

搜索指定文件
```bash
-> grep "who" -R . --include=*.{txt,php}
./11.txt:333 who are you tianjin
./link.txt:bbb who are you tianjin
./s1.txt:bbb who are you tianjin
./s2.txt:333 who are you tianjin
./1.txt:who
```

排除特定文件
```bash
-> grep "who" -R . --exclude=*.{txt,php}
./s1.txt.orig:333 who are you tianjin
./s.patch: 333 who are you tianjin
```

根据文件的内容排除特定文件
```bash
-> cat 1.txt
11.txt
1.txt

-> grep "who" -R . --exclude-from=1.txt
./s1.txt.orig:333 who are you tianjin
./link.txt:bbb who are you tianjin
./s1.txt:bbb who are you tianjin
./s2.txt:333 who are you tianjin
./s.patch: 333 who are you tianjin
```

排除特定目录
```bash
-> grep "who" -r .
./s1.txt.orig:333 who are you tianjin
./subdir/s1.txt:bbb who are you tianjin

-> grep "who" -r . --exclude-dir=subdir
./s1.txt.orig:333 who are you tianjin
```

和 xargs 搭配使用
```bash
-> grep "who" *.txt -lZ | xargs -0 ls -l
-rwxrwxrwx. 1 ssy ssy  86 Oct  4 14:11 11.txt
-rw-rw-r--. 1 ssy ssy   8 Oct  7 15:24 2.txt
lrwxrwxrwx. 1 ssy ssy   6 Oct  5 14:04 link.txt -> s1.txt
-rwxrwxrwx. 1 ssy ssy 109 Oct  7 13:03 s1.txt
-rwxrwxrwx. 1 ssy ssy  99 Oct  5 17:32 s2.txt
```
* 和 find 命令一样，和 xargs 搭配使用时，必须以 `\0` 作为分隔符
* grep 的 -Z 参数即是 `\0`
* -Z 常和 -l 参数结合使用

### 静默运行

```bash
-> grep "who" *.txt -q

-> echo $?
0

-> grep "whoooo" *.txt -q

-> echo $?
1
```
* 静默运行不输出任何内容，仅根据程序是否匹配成功返回退出码，成功返回 0
* 静默运行应用于仅仅测试能否匹配成功而不关心匹配结果的情况

### 打印匹配行的上下文

grep 不仅可以输出匹配的行，还可以输出匹配行的上下几行

* -A after 表示后几行
* -B 表示 before 前几行
* -C 表示前后行都有

```bash
-> seq 10 | grep 3 -A 2
3
4
5

-> seq 10 | grep 3 -B 2
1
2
3

-> seq 10 | grep 3 -C 2
1
2
3
4
5

-> echo -e "a\nb\na\nb\na\nb" | grep b -B 1
a
b
a
b
a
b

-> echo -e "a\nb\nc\na\nb\nc\na\nb" | grep b -B 1   # 未匹配的行用 -- 表示???
a
b
--
a
b
--
a
b
```
