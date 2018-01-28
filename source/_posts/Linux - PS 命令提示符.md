---
title: linux - PS 命令提示符
categories:
  - Linux
  - PS
tags:
  - Linux
  - PS
---

通过PS1变量修改命令提示符的颜色，达到快速区分命令和输出内容

<!--more-->

PS(Prompt Sign): 是指命令提示符

PS1：就是用户平时的提示符。
PS2：第一行没输完，等待第二行输入的提示符。
PS4

使用下列命令得到PS1的值
```bash
$echo PS1
$set|grep PS1
```

默认值如下
```bash
PS1='[\u@\h \W]\$ '
```

即表示提示符显示的样式为：
```bash
[ssy@localhost ~]$
```
含义就是
当前用户名@主机名的第一个名字 工作目录的最后一层目录名

PS1各参数含义：

    \d ：代表日期，格式为weekday month date，例如："Mon Aug 1"
    \h ：主机名
    \H ：包含域名的完整主机名称
    \t ：显示时间为24小时，格式：HH:MM:SS
    \T ：显示时间为12小时格式，格式：HH:MM:SS
    \A ：显示时间为24小时格式：HH：MM
    \@ The current time in 12-hour am/pm format
    \u ：当前用户的账号名称
    \v ：BASH的版本信息
    \V The release level of the bash shell
    \w ：完整的工作目录名称
    \W ：利用basename取得工作目录名称，只显示最后一个目录名
    \# ：下达的第几个命令
    \! The bash shell history number of this command
    \$ ：提示字符，如果是root用户，提示符为 # ，普通用户则为 $
    \a The bell character
    \e The ASCII escape character
    \j The number of jobs currently managed by the shell
    \l The basename of the shell’s terminal device name
    \n The ASCII newline character
    \r The ASCII carriage return
    \s The name of the shell
    \nnn The character corresponding to the octal value nnn

    \\ A backslash
    \[ Begins a control code sequence
    \] Ends a control code sequence

在PS1中设置字符颜色的格式为：
```
\[\e[F;Bm\]
```

其中“F“为字体颜色，编号为30-37，“B”为背景颜色，编号为40-47，例如
```
\[\e[34;40m\]
```
表示此处位置后的显示都使用黑底蓝字，如遇到下一个颜色设置才改变

颜色对照表：

    F    B
    30  40 黑色
    31  41 红色
    32  42 绿色 
    33  43 黄色 
    34  44 蓝色 
    35  45 紫红色 
    36  46 青蓝色 
    37  47 白色

可以关闭颜色输出
```
\[\e[0m\]
```

其它代码含义：

    0 OFF
    1 高亮显示
    4 underline
    5 闪烁
    7 反白显示
    8 不可见


可以直接更改PS1的值
```bash
PS1="\[\e[37;40m\][\[\e[34;40m\]\u\[\e[37;40m\]@\h \[\e[36;40m\]\w\[\e[0m\]]\\$ "
```

但只是本次登录起作用，永久作用需将上面的设置写入下面文件
```
/etc/bashrc
```

但不建议这样做，建议的做法是在目录
```
/etc/profile.d
```

下建立用户自己的配置文件
```bash
$ touch custom.sh
```

然后把PS1的设置写入文件,再加载此文件使配置生效
```bash
$ source custom.sh
```
