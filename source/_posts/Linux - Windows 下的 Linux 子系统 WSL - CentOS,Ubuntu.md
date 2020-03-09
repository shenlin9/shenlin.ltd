---
title: Linux - Windows 下的 Linux 子系统 WSL
categories:
  - Linux
tags:
  - Linux
  - Windows
  - Ubuntu
  - CentOS
---

Linux - Windows 下的 Linux 子系统 WSL

<!--more-->

分两步，先启用 Windows 的子系统功能，然后安装具体的 Linux 发行版。

## 启用 WSL

以管理员身份启动 PowerShell，运行命令：
```````
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
`````````````````````

或者：
控制面板 --》启用或关闭 Windows 功能 --》 适用于 Linux 的 Windows 子系统，选上

## 安装发行版

若 Microsoft Store 有自己需要的 Linux 发行版，且是免费的，当然安装最方便，若没有
，则使用第三方制作的 Linux WSL。

### 安装 Ubuntu

可以直接去 Microsoft Store 搜索 linux 安装即可

安装后第一次启动会让你输入用户名和密码，

此处的用户名和密码和登录 Windows 的用户名密码无关。

### 安装 CentOS

这里下载：
https://github.com/yuk7/CentWSL

注意最新版本若注明是适用于 Windows Insider(Windows 预览者)，则不要下载此版本，

下载合适版本后解压缩，解压后的文件夹即为 CentOS WSL 的程序文件夹，

所以不要解压缩到内存盘，否则也会报错：HRESULT:0x80004005，

以管理员身份运行解压后的 CentOS7.exe，

此文件第一次运行主要解压目录下的 rootfs 文件和向系统注册 CentOS，

提示成功后，

则再次以管理员身份运行 CentOS7.exe，则直接以 root 身份进入了系统，

然后可以设置密码，添加用户等。。。

#### CentOS 配置

Usage :

    <no args>
      - Open a new shell with your default settings.

    run <command line>
      - Run the given command line in that distro. Inherit current directory.

    runp <command line (includes windows path)>
      - Run the path translated command line in that distro.

    config [setting [value]]
      - `--default-user <user>`: Set the default user for this distro to <user>
      - `--default-uid <uid>`: Set the default user uid for this distro to <uid>
      - `--append-path <on|off>`: Switch of Append Windows PATH to $PATH
      - `--mount-drive <on|off>`: Switch of Mount drives

    get [setting]
      - `--default-uid`: Get the default user uid in this distro
      - `--append-path`: Get on/off status of Append Windows PATH to $PATH
      - `--mount-drive`: Get on/off status of Mount drives
      - `--lxguid`: Get WSL GUID key for this distro

    backup [contents]
      - `--tgz`: Output backup.tar.gz to the current directory using tar command
      - `--reg`: Output settings registry file to the current directory

    clean
      - Uninstall the distro.

    help
      - Print this usage message.

## 错误提示

**提示 0x80073D0A 错误**

Windows 防火墙被禁用，启用就可以了

**提示 0x80004005 错误**

其中一个原因是目录所在磁盘为内存盘
