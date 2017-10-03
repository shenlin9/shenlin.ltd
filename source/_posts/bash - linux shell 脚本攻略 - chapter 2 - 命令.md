---
title: bash - linux shell 脚本攻略 - chapter 2 - 命令
categories: bash
tags: bash
---

shell 命令

<!--more-->

## cat

concatenate 连接

用于读取、显示、拼接文件内容

```bash
-> cat 1.txt 2.txt 3.txt            # 读取 3 个文件的内容拼接在一起
aaa
bbb
ccc

-> echo aaa | cat -n                # 通过管道操作符从标准输入中读取
    1 aaa

-> echo ccc | cat - out.txt         # 通过管道操作符把标准输入和文件内容拼接在一起，注意必须有 - 号， - 号被作为 stdin 文本的文件名
ccc
aaa

-> echo ccc | cat out.txt           # 没有 - 号则拼接失败
aaa
```

去除多余的空行
```bash
$ cat -s multi-blank-file
```

把制表符显示为`^|`
```bash
$ cat -T file.py
```

输出时显示行号，并没有修改源文件
```bash
$ cat -n file1.txt
```

## script

使用 `script` 和 `scriptreplay` 命令可以录制命令的顺序和时序，并把这些儿数据记录在文本文件中，其他人则可以利用这些儿文本文件回放查看命令的输出

相对于录制视频更节省空间

```bash
-> script -t 2> timing.log -a output.session            # 开始录制终端会话
Script started, file is output.session

-> ls
abasdf         apr-util-1.5.4         go.sh             httpd-2.4.25.tar.bz2  nginx-1.13.0.tar.gz  pcre-8.40      php-7.1.5.tar.bz2  testproject
apr-1.5.2      apr-util-1.5.4.tar.gz  httpd-2.4.25      mysql                 output.session       pcre-8.40.zip  sudo               test.sh
apr-1.5.2.tar  err.txt                httpd-2.4.25.tar  nginx-1.13.0          out.txt              php-7.1.5      testdir            timing.log

-> cat out.txt
aaa

-> echo bcd
bcd

-> exit                                                 # 退出录制
exit
Script done, file is output.session

-> scriptreplay timing.log output.session               # 回放录制的终端会话
```
* timing.log 用于存储时序信息，描述每一个命令何时运行
* output.session 用于存储命令的输出
* -a 选项表示追加输出信息到文件
* -t 选项用于将时序数据输出到 stderr，而 2> 则用于将 stderr 重定向到timing.log

## find

find 命令将沿着文件的层级结构向下遍历

列出指定目录下的所有文件和文件夹
```bash
-> find /home/ssy/testproject
/home/ssy/testproject
/home/ssy/testproject/.git
/home/ssy/testproject/.git/index
/home/ssy/testproject/.git/branches
/home/ssy/testproject/.git/objects
```

find 默认以换行符分隔匹配文件，以 `\0` 分隔匹配的文件
```bash
-> find . -print0
../.mysql_history./httpd-2.4.25.tar.bz2./pcre-8.40.zip./.pki./.pki/nssdb./apr-1.5.2......
```

`-name` `-iname` 根据文件名匹配
```bash
-> find . -name "*.txt"
./apr-1.5.2/CMakeLists.txt
./apr-1.5.2/test/data/file_datafile.txt
./apr-1.5.2/test/data/mmap_datafile.txt
./apr-util-1.5.4/xml/expat/win32/MANIFEST.txt
```
* iname 不分区大小写

和逻辑运算符 `-o` 合用
```bash
-> find . ( -name "*.phpt" -o -name "*.txt" )
-bash: syntax error near unexpected token `('

-> find . \( -name "*.phpt" -o -name "*.txt" \)
./apr-1.5.2/CMakeLists.txt
./apr-1.5.2/test/data/file_datafile.txt
./apr-1.5.2/test/data/mmap_datafile.txt
./apr-util-1.5.4/xml/expat/win32/MANIFEST.txt
```
* \(\) 用于把两个条件合成一个整体

`-path` 把部分路径作为整体进行匹配
```bash
-> find . -path "*intl/tests*"      # 注意 * 号，否则匹配不到
./php-7.1.5/ext/intl/tests
./php-7.1.5/ext/intl/tests/timezone_countEquivalentIDs_basic.phpt
./php-7.1.5/ext/intl/tests/locale_get_display_region2.phpt
./php-7.1.5/ext/intl/tests/bug58756_MessageFormatter_variant2.phpt
./php-7.1.5/ext/intl/tests/msgfmt_format_intlcalendar_variant2.phpt
./php-7.1.5/ext/intl/tests/collator_asort_variant2.phpt
./php-7.1.5/ext/intl/tests/calendar_setTimeZone_error2.phpt
./php-7.1.5/ext/intl/tests/dateformat_get_set_timezone_variant5.phpt
```

`-regex` `-iregex`正则匹配路径
```bash
-> find . -regex ".+\(\.txt\|\.phpt\)"
./apr-1.5.2/CMakeLists.txt
./apr-1.5.2/test/data/file_datafile.txt
./apr-1.5.2/test/data/mmap_datafile.txt
./apr-util-1.5.4/xml/expat/win32/MANIFEST.txt
./apr-util-1.5.4/CMakeLists.txt
```
* `-iregex` 不区分大小写

`!` 否定，查找所有不以 txt 结尾的文件
```bash
-> find . ! -name "*.txt"
.
./.mysql_history
./httpd-2.4.25.tar.bz2
./pcre-8.40.zip
./.pki
./.pki/nssdb
./apr-1.5.2
./apr-1.5.2/libapr.dsp
./apr-1.5.2/apr-config.in
./apr-1.5.2/apr.dep
```

find 默认遍历所有深度子目录
选项 `-maxdepth` 设定最大搜索深度，设为 1 则只搜索当前目录，
选项 `-mindepth` 设定最小搜索深度，可以用来查找那些距离起始路径一定深度的文件
```bash
-> find . -maxdepth 1 -name "*.txt"
./err.txt
./out.txt

-> find . -maxdepth 2 -name "*.txt"
./apr-1.5.2/CMakeLists.txt
./pcre-8.40/makevp_l.txt
./pcre-8.40/makevp_c.txt
./testproject/2.txt
./testproject/3.txt
./testproject/1.txt
./err.txt
./out.txt

-> find . -mindepth 6 -name "*.txt"
./httpd-2.4.25/modules/lua/test/htdocs/find_me.txt
./php-7.1.5/ext/phar/tests/bug53872/second.txt
./php-7.1.5/ext/phar/tests/bug53872/third.txt
./php-7.1.5/ext/phar/tests/bug53872/first.txt
./php-7.1.5/ext/intl/tests/_files/resourcebundle.txt
```

`-type` 根据文件类型搜索，类 unix 系统的文件类型有：普通文件 f、目录 d、字符设备 c、块设备 b、符号链接 l、硬链接、套接字 s、FIFO p等
```bash
-> find . -maxdepth 2  -type d -name "*doc*"
./apr-1.5.2/docs
./apr-util-1.5.4/docs
./pcre-8.40/doc
./httpd-2.4.25/docs 
```
* 注意参数顺序导致的执行效率差异，先使用 -maxdepth 过滤层级，再使用 -type 类型过滤，最后根据名字模糊匹配

根据时间搜索：-atime,-mtime,-ctime
```bash
-> find . -mtime -1
.
./.lesshst
./test.sh
./.viminfo
./find
./timing.log
./output.session
./.bash_history
./err.txt
./out.txt
```
* 以天为单位
    * atime 文件最后一次被访问的时间
    * mtime 文件内容最后一次被修改的时间
    * ctime 文件元数据（权限、所有者等）最后一次被改变的时间
* 以分钟为单位
    * amin
    * mmin
    * cmin
* + - 号分别表示大于小于，这两个符号比较重要，因为很少有刚好整天数或整分钟的文件
* 注：Unix 没有文件"创建时间"这个概念
* ? atime 怎么算被访问了 ctime 还有什么元数据

根据相对时间搜索：newer，如比 out.txt 更晚修改的文件
```bash
-> find . -newer out.txt
.
./.lesshst
./.viminfo
./find
./timing.log
./output.session
./.bash_history
./err.txt
```
* ? 是否比较的是文件内容的修改时间

根据文件大小搜索
```bash
-> find . -size +2M
./httpd-2.4.25.tar.bz2
./pcre-8.40.zip
./apr-1.5.2.tar
```
* 文件大小单位
    * b 块，512 字节
    * c 字节
    * w 字，2 字节
    * k 1024 字节
    * M 1024k 字节
    * G 1024M 字节
* + - 号分别表示大于小于，这两个符号比较重要，因为很少有大小刚好的文件

`-delete` 删除匹配的文件
```bash
-> find . -name "aba*" -delete
```

`-print` 输出匹配的文件
```bash
-> find . -name "aba*" -print
```
* 不使用 `-print` 也一样输出

`-perm` 根据文件权限查找，如查找执行权限不当的 php 文件
```bash
> find .  ! -perm 644  -name "*.php"
./php-7.1.5/pear/fetch.php
./php-7.1.5/win32/build/mkdist.php
./php-7.1.5/win32/build/registersyslog.php
```

`-user` 根据文件拥有者查找，如查找 ssy 拥有的文件
```bash
-> find . -type f -user ssy
./.mysql_history
./httpd-2.4.25.tar.bz2
./pcre-8.40.zip
./apr-1.5.2/libapr.dsp
```

`-prune` 跳过指定目录，查找链接文件，但跳过名为 `.git` 的目录
```bash
-> find . \( -name ".git" -prune \) -o \( -type l \)
./apr-1.5.2/.libs/libapr-1.so.0
./apr-1.5.2/.libs/libapr-1.so
./apr-1.5.2/.libs/libapr-1.la
./apr-util-1.5.4/xml/expat/.libs/libexpat.so
```
* ? 为什么是 -o，逻辑或？

和 `-exec` 配合运行命令 : `find ... -exec ... {} \;`
```bash
# 找到 root 用户的文件更改所有者为 slynux
-> find . -type f -user root -exec chown slynux {} \;

# 找到所有 .c 文件输出到一个文件 all_c_files.txt
-> find . -type f -name "*.c" -exec cat {} \;>all_c_files.txt

# 把 1 天内修改的 txt 文件复制到 testproject 目录
-> find . -type f -name "*.txt" -mtime -1 -exec cp {} testproject/ \;
```
* {} 是和 exec 搭配使用的特殊字符串，对于每一个匹配的文件，{} 会被替换为相应的文件名
    * 例如 find 找到 test1.txt 和 test2.txt ，则 `chown slynux {}` 被解析为 `chown slynux test1.txt` 和 `chown slynux test2.txt`
    * 如果不希望对每个匹配文件都像上面那样执行一次命令，而希望使用文件列表作为命令参数，这样就可以少运行几次命令，则可以在 exec 中使用 `+` 来代替 `;`
* 因为 find 命令的全部输出就只有一个数据流（ stdin ），所以输出到 all_c_files.txt 文件时不使用追加 `>>` ，只有当多个数据流被追加到单个文件中时才有必要使用 >>

## xargs

管道符可以把上一个命令的标准输出重定向到下一个命令的标准输入，但有些儿命令只能通过参数的形式接受数据，而无法通过 stdin 数据流的形式接受数据，而 xargs 就擅长把 stdin 数据流转换为命令行参数。

单行命令是一个命令序列，各命令之间不使用分号，而是使用管道符链接。单行命令可以更高效简洁的完成任务。 xargs 是构建单行命令的重要组件之一。

xargs 还可以把单行文本变为多行，或者多行变为单行。

xargs 应紧跟在管道符后面，以 stdin 数据流作为主要的源数据流，把接收到的数据重新格式化，再将其作为参数提供给其他命令。

### 多行转换为单行

xargs 默认把换行符 `\n` 用空格替换，实现多行变一行

```bash
-> cat out.txt
aaa
bbb
ccc
ddd

-> cat out.txt|xargs
aaa bbb ccc ddd
```

### 单行转换为多行

使用 `-n` 参数指定每行最大的参数数量，参数默认由空格分隔

```bash
> cat err.txt
111 222 333 444

-> cat err.txt|xargs -n 1
111
222
333
444

-> cat err.txt|xargs -n 2
111 222
333 444

-> cat err.txt|xargs -n 3
111 222 333
444
```

### 自定义定界符

多行和一行转换时默认的定界符是换行符和空格，而 `-d` 选项可以自定义定界符

```bash
-> echo "aaaXbbbXcccXddd" | xargs -d X
aaa bbb ccc ddd

-> echo "aaaXbbbXcccXddd" | xargs -d X -n 2
aaa bbb
ccc ddd

-> echo -e "aaa\0bbb\0ccc\0ddd" | xargs -d "\0" -n 2        # \0 表示 null 字符
aaa bbb
ccc ddd

-> echo -e "aaa\0bbb\0ccc\0ddd" | xargs -0 -n 2             # null 作定界符的另一种简洁写法，只适用于 null
aaa bbb
ccc ddd
```

### 将 stdin 数据流转换为命令行参数

举例：test.sh 为脚本文件，需要的参数保存在 args.txt 里

```bash
-> cat test.sh                          # 脚本只是输出参数，让我们可以看见执行过程
echo $@

-> cat args.txt                         # 参数文件里每行一个参数
apple
banana
orange

-> cat args.txt|xargs ./test.sh         # 一次性传递所有参数
apple banana orange

-> cat args.txt|xargs -n 1 ./test.sh    # 每次传递一个参数，可见每传递一次就执行一次脚本
apple
banana
orange

-> cat args.txt|xargs -n 1 -I {} ./test.sh -p 123 -y {}  -l         # 这种情况 ：test.sh 接受多个选项和参数，且 args.txt 里保存的只是某个选项的一个参数
-p 123 -y apple -l                                                  # 则要 xargs 使用 -I 选项指定替换字符串，如此例中的 {}，并在后面的命令中使用这个指定
-p 123 -y banana -l                                                 # 的替换字符串，使用 -I 时命令以循环的方式执行
-p 123 -y orange -l
```

### 和 find 结合使用

xargs 和 find 结合使用会很方便，但注意：find 和 xargs 结合使用时，一定要使用 `-print0` 选项，即以 null 字符 `\0` 来分隔输出

错误的例子：使用 find 找到文件并删除
```bash
-> find . -type f -name "*.txt" -print | xargs rm -f
```
find 命令的输出结果的定界符是无法预测的，有可能是换行符`\n`，也可能是空格，这样就很危险，有可能意想不到的文件被删除，如一个文件
名里包含空格`hello text.txt`，则文件里的空格可能被 xargs 误认为是定界符，删除的文件就变成了 `hello` 和 `text.txt`，所以正确方法：
```bash
-> find . -type f -name "*.txt" -print0 | xargs -0 rm -f
```
同理，统计所有 C 语言源代码的行数:
```bash
-> find source_dir -type f -name "*.c" -print0 | xargs -0 wc -l

-> find . -maxdepth 1 -type f -name "*.txt" -print0 | xargs -0 wc -l
 3 ./args.txt
 1 ./err.txt
 4 ./out.txt
 8 总用量

-> find . -maxdepth 1 -type f -name "*.txt" -exec wc -l {} \;       # exec 实现相同效果
3 ./args.txt
1 ./err.txt
4 ./out.txt

-> find . -maxdepth 1 -type f -name "*.txt" -print -exec wc -l {} \;    # 有无 print 的区别
./args.txt
3 ./args.txt
./err.txt
1 ./err.txt
./out.txt
4 ./out.txt

-> find . -maxdepth 1 -type f -name "*.txt" -print0 -exec wc -l {} \;   # 以 null 字符分隔的区别
./args.txt3 ./args.txt
./err.txt1 ./err.txt
./out.txt4 ./out.txt
```

### 对同一个参数执行多条命令的技巧

xargs 只能为一组命令提供参数，如果涉及到多组命令操作同一个参数，则无法实现，如
```bash
-> cat args.txt | xargs -n 1 -I {} echo {} && echo {}
apple
banana
orange
{}

ssy@e42.matrix ~
-> cat args.txt | xargs -n 1 -I {} echo {};echo {}
apple
banana
orange
{}
```

可以使用 while 循环和子 shell 实现多条命令处理一个参数

```bash
-> cat args.txt | (while read arg;do echo $arg;echo $arg;printf "$arg\n";done)
apple
apple
apple
banana
banana
banana
orange
orange
orange
```
* () 中的为子 shell，多个命令可作为一个整体来运行

```bash
$ cmd0 | ( cmd1;cmd2;cmd3) | cmd4
```
如果 cmd1 是 `cd /` ，那么就会改变子shell工作目录，然而这种改变仅局限于子 shell 内部。 cmd4 则完全不知道工作目录发生了变化。

## tr

translate 转换

tr 用于对来自标准输入的内容进行字符替换、字符删除和重复字符压缩，可以把一组字符变成另一组字符。

tr 只接受 stdin，不接受命令行参数输入

tr 命令格式 `tr [options] set1 set2` 将输入字符从 set1 映射到 set2，如果 set2 长度小于 set1，那么 set2 会不断重复其最后一个字 符，直到长度与 set1 相同。如果 set2 的长度大于 set1 ，那么在 set2 中超出 set1 长度的那部分字符则全部被忽略

### 基本替换

定义集合的格式 ：不连续字符采用枚举方式，如`aA,.` 连续字符格式 `起始字符-终止字符`，如果字符不能连续则仅表示 3 个字符，分别是起始字符、终止字符和 -
```bash
-> echo abcdef|tr a-f A-F
ABCDEF
```

ROT13 rotateby13places，回转 13 位，是一个简易的加密算法，把 26 个字母的前 13 位和后 13 位一一对调，文本加密和解密都使用同一个函数，
```bash
-> echo "this is a ROT13 encoded string" | tr "a-zA-Z" "n-za-mN-ZA-M"
guvf vf n EBG13 rapbqrq fgevat

-> echo "guvf vf n EBG13 rapbqrq fgevat" | tr "a-zA-Z" "n-za-mN-ZA-M"
this is a ROT13 encoded string
```

利用替换巧妙实现文件中数字列表的计算
```bash
-> cat num.txt
42
78
234
98

-> cat num.txt | echo $[ $(tr '\n' '+' ) 0 ]
452

-> cat num.txt | echo $[ $(tr '\n' '+' ) ]                                      # 没有多加个 0 则末尾有个多余的 + 号会出错
-bash: 42+78+234+98+ : syntax error: operand expected (error token is "+ ")
```
* 把换行符替换为加号，实现每行相加的效果
* 末尾多个加号，所以再拼上一个 0，防止出错
* `$[ operation ]` 实现数学运算

### 删除字符

`-d` 删除字符集合中的字符

```bash
-> echo "this is a ROT13 encoded string" | tr -d "A-Z0-9"
this is a  encoded string
```

### 字符补集

`-c` 表示除了字符集合中指定的字符以外的所有字符

```bash
-> echo "this is a ROT13 encoded string" | tr -c "A-Z0-9" "+"   # 表示除了大写字母 A 到 Z 数字 0 到 9 外的所有字符都映射为 + 号
++++++++++ROT13++++++++++++++++

-> echo "this is a ROT13 encoded string" | tr -d -c "A-Z0-9"    # 和 -d 选项组合，表示除了大写字母 A 到 Z 数字 0 到 9 外的所有字符都删除
ROT13
```

### 压缩连续重复字符

`-s` 选项指定要压缩的字符列表

```bash
-> echo "this string    has   multile     spaces and aaa aaa aa aaaa bbb bb bbbbb" | tr -s " ab"
this string has multile spaces and a a a a b b b
```

### 字符类

tr 的字符类，使用方式：`tr [:class:] [:class:]`
* alpha 字母
* digit 数字
* alnum 字母和数字
* graph 图形字符
* lower 小写字母
* cnctrl 控制字符，不可打印
* print 可打印字符
* punct 标点符号
* space 空白字符
* upper 大写字母
* xdigit 十六进制字符

```bash
-> echo "this is a ROT13 encoded string" | tr "[:lower:]" "[:upper:]"
THIS IS A ROT13 ENCODED STRING
```

## 校验和

文件可以通过网络或任何存储介质分发到不同的地点。出于多种原因，数据有可能在传输过程中丢失了若干位，从而导致文件损坏。
因此，我们需要采用一些测试方法来确定接收到的文件是否存在错误。
通过对接受到的文件进行校验和计算，再和源文件的校验和进行比较，就可以核实文件的完整性。

最广泛的校验和技术是 md5 和 sha-1

### md5sum

md5 生成的是 32 位字符的十六进制字符串

```bash
-> cp num.txt dig.txt

# 一次生成多个文件的校验和
-> md5sum num.txt dig.txt err.txt
0a9e3ef53c955a1952fd673906a26f74  num.txt
0a9e3ef53c955a1952fd673906a26f74  dig.txt
8b37ac8b627c28ade3316398eb4c8839  err.txt

# 把文件校验和存入文件，这里的文件名 num.md5,err.md5 是任意的，因为生成校验和文件里保存有文件名
-> md5sum num.txt > num.md5
-> md5sum err.txt > err.md5

# 用MD5文件来验证接受到的文件
-> md5sum -c num.md5
num.txt: 确定

-> md5sum -c *.md5
err.txt: 确定
num.txt: 确定
```

### sha1sum

SHA-1 生成的是 40 位字符的十六进制字符串

```bash
-> sha1sum out.txt > out.sha1

-> sha1sum -c out.sha1
out.txt: 确定
```

### md5deep 和 sha1deep

对目录也可以进行校验和计算，方法是对目录中的所有文件以递归的方式进行计算

可以使用 md5deep 进行计算
```bash
$ md5deep -rl directory_path > directory.md5
```
* -r 使用递归的方式
* -l 使用相对路径。默认情况下，md5deep 会输出文件的绝对路径

也可以结合 find 来递归计算校验和：
```bash
-> find . -maxdepth 1 -type f -name "*.txt"
./num.txt
./args.txt
./dig.txt
./err.txt
./out.txt
./asdf.txt

-> find . -maxdepth 1 -type f -name "*.txt" -print0 | xargs -0 md5sum > total.md5

-> md5sum -c total.md5
./num.txt: 确定
./args.txt: 确定
./dig.txt: 确定
./err.txt: 确定
./out.txt: 确定
./asdf.txt: 确定
```

## 加密

### crypt

### gpg

### base64

## 散列

### md5sum sha1sum

### shadow-like

## 排序、唯一和重复

## 临时文件命名和随机数

shell 脚本常用到临时文件，最适合保存临时数据的地方是 /tmp 目录，系统重启后会自动清空

mktemp 命令可以创建临时文件或目录，并返回其名称

```bash
# 创建临时文件
$ mktemp
/tmp/tmp.0tgAPFFREN

$ tmp=`mktemp`;echo $tmp;
/tmp/tmp.6p89BpTAwS

# -d 创建临时目录
$ mktemp -d
/tmp/tmp.2JY0vOspJV

$ ll /tmp/tmp.2JY0vOspJV/
total 0

# -u 生成临时文件名不创建文件
$ mktemp -u
/tmp/tmp.vMlpAEYFfJ

$ ll /tmp/tmp.vMlpAEYFfJ
ls: cannot access '/tmp/tmp.vMlpAEYFfJ': No such file or directory

# 根据模板生成临时文件名，模板必须至少 3 个大写 X，不能是小写 x 或其他字符
$ mktemp -u tesXXXttt
tesl8Dttt

$ mktemp -u test.XXX
test.6pw

$ mktemp -u test.XX
mktemp: too few X's in template ‘test.XX’

$ mktemp -u test.xxx
mktemp: too few X's in template ‘test.xxx’

$ mktemp -u test.AAAA
mktemp: too few X's in template ‘test.AAAA’
```

## 分割文件和数据

早期分割文件主要是因为软盘之类的存储设备容量有限，切割为多个小文件才能分散存储到多张软盘中；
现在分割文件主要是为了提高可读性、生成日志或通过E-mail发送文件等

### 按数据块大小分割

数据块单位 ：k （KB）， M （MB）、 G （GB）、 c （byte）、 w （word）

??? c 和 w 测试好像不行

??? 还可以分割到 n 个文件

```bash
$ ll
-rw-r--r-- 1 ssl 197121  7992 十月  3 15:04 5ECC.tmp

$ split -b 1000 5ECC.tmp

$ ll
-rw-r--r-- 1 ssl 197121  7992 十月  3 15:04 5ECC.tmp
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:17 xaa
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:17 xab
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:17 xac
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:17 xad
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:17 xae
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:17 xaf
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:17 xag
-rw-r--r-- 1 ssl 197121   992 十月  3 18:17 xah
```

### 设定分割文件前后缀

分割文件的默认前缀是 x，默认后缀是字母
```bash
$ split -b 1000  5ECC.tmp -d -a 3 part                  # 两条命令执行效果相同，重点是前缀必须在命令行的最后
$ split -b 1000 -d -a 3 5ECC.tmp part
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:34 part000
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:34 part001
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:34 part002
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:34 part003
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:34 part004
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:34 part005
-rw-r--r-- 1 ssl 197121  1000 十月  3 18:34 part006
-rw-r--r-- 1 ssl 197121   992 十月  3 18:34 part007
```
* -d 表示使用数字后缀，否则默认是字母
* -a 3 表示后缀长度是 3 位
* split 最后参数是前缀，必须放命令行最后，默认是 x

### 按行数分割文件

`-l` 指定多少行分割成一个文件

```bash
$ cat text.txt
aaa
bbb
ccc
ddd

$ split -l 2 -d text.txt text

$ cat text00 ; echo '----' ; cat text01
aaa
bbb
----
ccc
ddd
```

### 按文件数分割文件

`-n` 指定分割成多少个文件

```bash
$ split -n 4 text.txt

$ cat xaa;echo '--';cat xab;echo '--';cat xac;echo '--';cat xad
aaa
--
bbb
--
ccc
--
ddd
```

### csplit

能够依据指定的条件和字符串匹配选项进行分割

```bash
$ cat server.log
SERVER-1
[connection] 192.168.0.1 success
[connection] 192.168.0.2 failed
[disconnect] 192.168.0.3 pending
[connection] 192.168.0.4 success
SERVER-2
[connection] 192.168.0.1 failed
[connection] 192.168.0.2 failed
[disconnect] 192.168.0.3 success
[connection] 192.168.0.4 failed
SERVER-3
[connection] 192.168.0.1 pending
[connection] 192.168.0.2 pending
[disconnect] 192.168.0.3 pending
[connection] 192.168.0.4 failed

$ csplit server.log /SERVER/ -n 2 -s {*} -f server -b "%02d.log" && rm server00.log
csplit: ‘{339A5648-B4B7-4c06-ACAF-12C54A7C842B’}: integer required between '{' and '}'
```
* /SERVER/ 用来匹配某一行，分割过程即从此处开始。
* /[REGEX]/ 表示文本样式。包括从当前行（第一行）直到（但不包括）包含“ SERVER ”
的匹配行。
* {*} 表示根据匹配重复执行分割，直到文件末尾为止。可以用{整数}的形式来指定分割执行的次数。
* 因为分割后的第一个文件没有任何内容（匹配的单词就位于文件的第一行中），所以我们删除了server00.log。
* -s 使命令进入静默模式，不打印其他信息。
* -n 指定分割后的文件名后缀的数字个数，例如01、02、03等。
* -f 指定分割后的文件名前缀（在上面的例子中，server就是前缀）。
* -b 指定后缀格式。例如 %02d.log ，类似于C语言中 printf 的参数格式。在这里文件名= 前缀+后缀= server + %02d.log 。
* ??? 执行出错

## 切分文件名

`${VAR%.*}` 和 `${VAR%%.*}`
* % 和 %% 的左侧是要处理的变量，右侧是通配符
* 通配符从右向左匹配
* % 是非贪婪匹配，即从右向左找到最短匹配，%% 是贪婪匹配，即从右向左找到最长匹配
* 从变量 VAR 中删除通配结果

`${VAR#*.}` 和 `${VAR#*.}`
* # 和 ## 的左侧是要处理的变量，右侧是通配符
* 通配符从左向右匹配
* # 是非贪婪匹配，即从左向右找到最短匹配，## 是贪婪匹配，即从左向右找到最长匹配
* 从变量 VAR 中删除匹配结果

因为是从变量中删除匹配结果，所以为了获取扩展名时通配符是在匹配文件名，获取文件名时是在匹配扩展名

文件中出现多个`.`很常见，所以 `##` 的贪婪匹配能更准确提取扩展名，而 `%` 的非贪婪模式能更准确获取文件名

```bash
$ var="custome.class.jpg";echo ${var%.*}
custome.class

$ var="custome.class.jpg";echo ${var%%.*}
custome

$ var="custome.class.jpg";echo ${var#*.}
class.jpg

$ var="custome.class.jpg";echo ${var##*.}
jpg
```

## 批量重命名和移动

批量重命名 png 和 jpg 文件
```bash
$ cat rename.sh
#!/bin/bash
count=1;
for img in `find . -iname '*.png' -o -iname '*.jpg' -type f -maxdepth 1`
do
new=image-$count.${img##*.}
echo "Renaming $img to $new"
mv "$img" "$new"
let count++
done
```

实现通用重命名???
```bash

将  *.JPG 更名为  *.jpg ：
$ rename *.JPG *.jpg

将文件名中的空格替换成字符“ _ ”：
's/ /_/g' 用于替换文件名，而  * 是用于匹配目标文件的通配符，它也可以以  *.txt 或其他样式出现。
$ rename 's/ /_/g' *

转换文件名的大小写：
$ rename 'y/A-Z/a-z/' *
$ rename 'y/a-z/A-Z/' *

将所有的 .mp3文件移入给定的目录：
$ find path -type f -name "*.mp3" -exec mv {} target_dir \;

将所有文件名中的空格替换为字符“ _ ”：
$ find path -type f -exec rename 's/ /_/g' {} \;
```

## 词典和拼写检查 ???

/usr/share/dict 词典文件，词典文件是包含单词列表的文本文件，可以用来检查某个单词是否为词典中的单词

aspell 拼写检查工具

## 自动应答交互

和用户交互的例子

```bash
-> cat test.sh
read -p "input your name : " name
read -p "input your age : " age
echo "your name is $name age is $age"

-> ./test.sh
input your name : shenlin
input your age : 37
your name is shenlin age is 37
```

上面需要用户交互的脚本，也可实现自动应答
```bash
# 通过命令行
-> echo -e "shenlin\n38\n" | ./test.sh
your name is shenlin age is 38

# 通过文件实现
-> echo -e "shenlin\n38\n" > answer.txt

-> ./test.sh < answer.txt
your name is shenlin age is 38
```

上面的交互都是根据命令执行顺序按顺序自动的传递参数，如果有分支判断，则可能无法实现，这时可以使用 expect 命令

expect 可以根据提示信息提供对应的输入
```bash
-> cat expect.sh
#!/usr/bin/expect               # <---注意这里是 expect 的路径
spawn ./test.sh                 # spawn 指定需要自动化哪个命令或脚本
expect "input your name : "     # expect 指定需要等待的消息
send "shenlin\n"                # send 设置要发送的消息
expect "input your age : "
send "37\n"
expect eof                      # expect eof 表示交互结束，不可缺少

-> ./expect.sh
spawn ./test.sh
input your name : shenlin
input your age : 37
your name is shenlin age is 37
```

## 并行进程

多进程利用 CPU 的多核心加速程序执行，如 CPU 密集型的 md5sum 命令

```bash
PIDARRAY=()
for file in out.txt err.txt num.txt dig.txt pcre-8.40.zip
do
    md5sum $file &
    PIDARRAY+=("$!")
done
wait ${PIDARRAY[@]}
```
* `&` 把进程放入后台执行
* `$!` 获取最后一个进程ID放入数组
* 使用 wait 命令等待进程结束
