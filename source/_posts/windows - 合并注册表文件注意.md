---
title: windows - 合并注册表文件注意
categories:
  - Windows
tags:
  - Windows
---

如果合并 windows 注册文件时提示“只能导入二进位注册文件”，

则是因为文件格式不对。

<!--more-->

使用 vim 编辑 Windows 的注册表文件时注意：

必须设置为
```vim
set fenc=utf-16le
set bomb
```

或者
```vim
set fenc=utf-8
set nobomb
```

而换行格式倒是无所谓
```vim
set ff=unix
```
或
```vim
set ff=dos
```
都行

举例，隐藏 win10 此电脑下的 6 个文件夹，保存为 `.reg` 文件合并即可
```
Windows Registry Editor Version 5.00

;图片
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions\{0ddd015d-b06c-45d5-8c4c-f59713854639}\PropertyBag]
"ThisPCPolicy"="Hide"

;视频
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions\{35286a68-3c57-41a1-bbb1-0eae73d76c95}\PropertyBag]
"ThisPCPolicy"="Hide"

;下载
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions\{7d83ee9b-2244-4e70-b1f5-5393042af1e4}\PropertyBag]
"ThisPCPolicy"="Hide"

;音乐
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions\{a0c69a99-21c8-4671-8703-7934162fcf1d}\PropertyBag]
"ThisPCPolicy"="Hide"

;桌面
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions\{B4BFCC3A-DB2C-424C-B029-7FE99A87C641}\PropertyBag]
"ThisPCPolicy"="Hide"

;文档
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions\{f42ee2d3-909f-4907-8871-4c22fc0bf756}\PropertyBag]
"ThisPCPolicy"="Hide"
```
