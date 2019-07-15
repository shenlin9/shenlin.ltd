---
title: Windows - Install or Uninstall Error Code 2753
categories:
- Windows
tags:
- Windows
- Error 2753
date: 2019-06-28 09:25:58
---

Windows - Install or Uninstall Error Code 2753

<!--more-->

安装或卸载软件时，发生错误：

    "The installer has encountered an unexpected error installing this package.
    This may indicate a problem with this package. This error code is 2753."

启动注册表编辑器，然后在 `HKEY_CLASSES_ROOT\Installer\Products\` 位置查找软件名
关键词，删除找到的项
