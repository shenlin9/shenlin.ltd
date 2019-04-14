---
title: Linux - Windows 下安装 Ubuntu for WSL 发行版
categories:
  - Linux
tags:
  - Linux
  - Ubuntu
---

Linux - Windows 下安装 Ubuntu for WSL 发行版

<!--more-->

## 安装

以管理员身份启动 PowerShell，运行命令：
```````
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
`````````````````````

## 提示 0x80073D0A 错误

Windows 防火墙被禁用，启用就可以了
