---
title: bash - linux shell 脚本攻略 - chapter 4.7~4.16 - 文本综合练习
categories: 
 - linux
 - bash
tags: 
 - linux
 - bash
---

shell 文本综合练习

<!--more-->

##  统计词频

```bash
if [ $# -ne 1 ];
then
echo "Usage: $0 filename";
exit -1
fi
filename=$1
egrep -o "\b[[:alpha:]]+\b" $filename | \
awk '
    { count[$0]++ }
    END{ 
        printf("%-14s%s\n","Word","Count") ;
        for(ind in count)
        { printf("%-14s%d\n",ind,count[ind]); }
    }
'
```
* \b 是单词边界标记符

##  压缩解压缩 Javascript

javascript 代码为了可读性有很多的空格、缩进、换行、注释，所谓压缩 Javascript 就是把这些在生产环境使用不到的字符移除，而解压缩则是还原这些内容

```bash
$ cat sample.js | \
tr -d '\n\t' | tr -s ' ' \
| sed 's:/\*.*\*/::g' \
| sed 's/ \?\([{}();,:]\) \?/\1/g'
```

##  按列合并多个文件

cat 是把文件按行拼接起来，而 paste 则可以按列拼接多个文件
```bash
-> cat 1.txt
111
222

-> cat 2.txt
who
you

-> cat 1.txt 2.txt
111
222
who
you

-> paste 1.txt 2.txt
111	who
222	you
```

paste 默认使用制表符作为列之间的定界符，`-d` 可以自定义定界符，注意只能使用一个字符作定界符，即使输入多个也只使用第一个字符作为定界符
```bash
-> paste 1.txt 2.txt
111	who
222	you

-> paste -d "---" 1.txt 2.txt
111-who
222-you
```

##  打印文件或行中的第 n 个单词或列

##  打印行或样式之间的文本

##  以逆序形式打印行

tac 是 cat 反过来写，其作用就是把文件的文本行倒序输出来

tac 默认分隔符是 `\n` ，可以用 `-s` 选项指定分隔符

```bash
-> cat 1.txt
111
222

-> tac 1.txt
222
111

-> cat 2.txt
who
you

-> tac 2.txt
you
who

-> tac 1.txt 2.txt
222
111
you
who
```

awk 实现倒序输出
```bash
$ seq 9 | \
awk '{ lifo[NR]=$0 } END{ for(lno=NR;lno>-1;lno--){ print lifo[lno]; } }'
```

##  解析文本中的电子邮件地址和 URL

##  在文件中移除包含某个单词的句子

##  对目录中的所有文件进行文本替换

##  文本切片及参数操作
