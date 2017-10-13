---
title: bash - linux shell 脚本攻略 - chapter 4.4 - cut
categories: 
 - linux
 - bash
tags: 
 - linux
 - bash
 - cut
---

shell cut

<!--more-->

cut 是把文本按列进行切分的工具，其默认的列分隔符是制表符（注意 vim 的 expandtab 选项会把制表符替换为空格）

cut 可以按字段 `-f`，字节 `-b`，字符 `-c` 切分

## 按字段切分

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

## 按字符、字节提取

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

