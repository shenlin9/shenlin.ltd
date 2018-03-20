---
title: Windows - mklink 创建链接
categories:
  - Windows
tags:
  - mklink
---

mklink 创建链接

<!--more-->

```
MKLINK [[/D] | [/H] | [/J]] Link Target

        /D      创建目录符号链接。默认为文件符号链接。
        /H      创建硬链接而非符号链接。
        /J      创建目录联接。

        Link    要创建的链接
        Target  被创建链接的源文件或目录

$ mklink        为文件创建符号链接，类型是 <SYMLINK> 

$ mklink /H     为文件创建硬链接，没有类型

$ mklink /D     为目录创建符号链接，类型是 <SYMLINKD>，注意比文件多个 D

$ mklink /J     为目录创建**联**接，效果类似文件的硬链接，类型是 <JUNCTION>
```
* 符号链接可看作快捷方式，无论是文件还是目录的符号链接，删除源文件或目录后则创建
  的链接也失效
* 文件的硬链接删除源文件后不影响链接文件，但内容更新两者会同步
* 目录的联结删除源目录后则链接目录也失效

创建文件硬链接，不能跨分区
```
mklink /H "config_bak/autohotkey.ahk"  "autohotkey.ahk"

为 config_bak/autohotkey.ahk <<===>> autohotkey.ahk 创建了硬链接
```

创建目录联结，可以跨分区
```
$ mklink /J d:\git-repo\config_bak  config_bak
为 d:\git-repo\config_bak <<===>> config_bak 创建的联接
```
