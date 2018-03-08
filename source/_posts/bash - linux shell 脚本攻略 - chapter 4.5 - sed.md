---
title: bash - linux shell 脚本攻略 - chapter 4.5 - sed
categories: 
 - Linux
 - Bash
tags: 
 - sed
---

sed , stream editor 流编辑器

<!--more-->

## s/pattern/replaced/g 替换匹配项

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

## /pattern/d 删除匹配

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

## 已匹配字符串

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

## 匹配的子字符串

也称为向后引用（back reference），`\1` 匹配第一个子字符串 ，`\2` 匹配第二个 ，以此类推

```bash
-> echo This is digit 7 in a number | sed "s/digit \([0-9]\)/\1/"
This is 7 in a number

-> echo seven EIGHT | sed 's/\([a-z]\+\) \([A-Z]\+\)/\2 \1/'
EIGHT seven
```

## 组合多个表达式

三种组合多个表达式的方式

```bash
-> echo "hello who are you" | sed 's/who/i/' | sed 's/are/am/' | sed 's/you/jackie/'
hello i am jackie

-> echo "hello who are you" | sed -e 's/who/i/' -e 's/are/am/' -e 's/you/jackie/'
hello i am jackie

-> echo "hello who are you" | sed -e 's/who/i/;s/are/am/;s/you/jackie/'
hello i am jackie
```

## 引号引用

sed 中通常用单引号引用字符串，但也可以使用双引号，通常适用于需要解析变量时

```bash
-> var="world";echo "hello this is a world" | sed "s/$var/amazing world/"
hello this is a amazing world
```

