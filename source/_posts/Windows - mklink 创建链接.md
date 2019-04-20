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

`/H` 创建文件硬链接，不能跨分区
```
mklink /H "config_bak/autohotkey.ahk"  "autohotkey.ahk"

为 config_bak/autohotkey.ahk <<===>> autohotkey.ahk 创建了硬链接
```

`/J` 创建目录联结，可以跨分区，和快捷方式区别：举例来说，要通过快捷方式或 mklink
的方式更改 Cocoon 浏览器的缓存目录
`C:\Users\T460P\AppData\Local\VirtualWorld\Cocoon`，如果在
`C:\Users\T460P\AppData\Local\VirtualWorld` 目录下创建一个名为 Cocoon 的快捷方
式指向目标目录，则仍会被浏览器视为缺少 Cocoon 目录而自动创建，但通过下面的方式创
建的目录联结则会被程序视为真实存在的目录：
```
C:\Users\T460P\AppData\Local\VirtualWorld>mklink /J Cocoon Z:\Cocoon
为 Cocoon <<===>> Z:\Cocoon 创建的联接

C:\Users\T460P\AppData\Local\Mozilla\Firefox\Profiles\xs7y91g2.default>mklink /J cache2 Z:\FireFox\cache2
为 cache2 <<===>> Z:\FireFox\cache2 创建的联接
```
注意执行命令时前面的目录不能存在会自动创建，而后面的目录必须存在不会自动创建
