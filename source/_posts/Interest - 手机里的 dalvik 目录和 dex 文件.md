---
title: Interest - 手机里的 dalvik 目录和 .dex 文件
categories:
  - Interest
tags:
  - dalvik
---

Android 手机启动时有时提示“正在优化应用”即是在生成 `.dex` 文件

<!--more-->

目录 `/data/dalvik-cache/` 随着使用占用大量空间， 子目录下包含的大量 `.dex` 文件

是 java 的 dalvik 虚拟机产生的 apk 的优化文件（不是字节码），可以删除再让系统自动

产生， 这样可以清理一些儿已卸载软件的残留


android 6.0 后改为 art 虚拟机，产生的优化文件是 `.odex`
