---
title: windows - 隐藏文件夹
categories:
  - Windows
tags:
  - Windows
---

通过下列命令隐藏的文件夹，在资源管理器中即使选中 "显示隐藏文件" 复选框也不显示，

但可以通过直接输入路径访问到

<!--more-->

隐藏文件夹
```
attrib +a +s +r +h d:\private-folder
```

显示文件夹
```
attrib -a -s -r -h d:\private-folder
```
