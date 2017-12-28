---
title: linux - Filesystem Hierarchy Standard 文件系统层次化标准
categories:
  - Linux
  - FHS
tags:
  - Linux
  - FHS
---

Filesystem Hierarchy Standard 文件系统层次化标准

<!--more-->

因为Linux的开发人员实在太多了，如果每个人都使用自己的目录配置方法，那么将可能会带来很多管理问题。你能想象，你进入一个企业之后，所接触到的Linux目录配置方法竟然跟你以前学的完全不同吗？所以，后来就有所谓的文件系统层次标准(Filesystem Hierarchy Standard，FHS)出台。

多数Linux版本采用这种文件组织形式，类似于Windows操作系统中c盘的文件目录，FHS采用树形结构组织文件。

FHS定义了系统中每个区域的用途、所需要的最小构成的文件和目录，同时还给出了例外处理与矛盾处理。

## 规范

FHS定义了两层规范

第一层是 `/` 下面的各个目录应该要放什么文件数据，例如 `/etc` 应该要放置设置文件，`/bin` 与 `/sbin` 则应该要放置可执行文件等等。

第二层则是针对/usr及/var这两个目录的子目录来定义。例如/var/log放置系统登录文件、/usr/share放置共享数据等等。

## 特点

由于FHS仅是定义出最上层(/)及子层(/usr, /var)的目录内容应该要放置的文件数据，因此，在其他子目录层级内，就可以随开发人员自行配置了。举例来说，FC4的网络设置数据放在/etc/sysconfig/network-script/目录下，但SuSE Server 9则是将网络放在/etc/sysconfig/network/目录下，目录名称是不同的。

另外，在Linux中，所有的文件与目录都由根目录/ 开始。那是所有目录与文件的源头。然后再一个一个分支下来，有点像树状。因此，我们也称这种目录配置方式为：“目录树(directory tree)”。

这个目录树主要特性有：
* 目录树的起始点为根目录(/, root)。
* 每一个目录不仅能使用本地端分区的文件系统，也可以使用网络上的文件系统。举例来说，可以利用网络文件系统(Network File System，NFS)服务器载入某特定目录等。
* 每一个文件在此目录树中的文件名(包含完整路径)都是独一无二的。

此外，根据文件名写法的不同，也可将路径(path)定义为绝对路径(absolute)与相对路径(relative)。
绝对路径为：由根目录(/)开始写起的文件名或目录名称，例如/home/dmtsai/.bashrc；
相对路径为相对于当前路径的文件名写法。例如./home/dmtsai或../../home/dmtsai/等等。反正开头不是/ 就属于相对路径的写法。必须要了解，相对路径是以“当前所在路径的相对位置”来表示的。举例来说，当前在/home目录下，如果想要进入/var/log目录时，怎么写呢？
```bash
cd /var/log(absolute)
cd ../var/log(relative)
```
因为在/home中，所以要回到上一层(../)之后，才能继续向/var移动。

特别注意这两个特殊的目录：
.：表示当前目录，也可以使用./来表示。
..：表示上一层目录，也可以../来表示。
.与..的目录概念很重要，你常常会看到cd ..或 ./command之类的命令方式，就是表示上一层与当前所在目录的工作状态。

此外，针对“文件名”与“完整文件名(由/ 开始写起的文件名)”的字符限制大小为：单一文件或目录的最大容许文件名为255个字符。包含完整路径名称及目录(/)的完整文件名为4096个字符。

我们知道，/var/log/下面有个文件名为message，这个message文件的最大文件名可达255个字符。var与log这两个上层目录最长也分别可达255个字符。但总的来说， /var/log/messages这样完整的文件名最长则可达4096个字符。

提示:root在Linux里面的意义很多。如果从“账号”的角度来看，root指“系统管理员”身份，如果以“目录”的角度来看，root指的是根目录，就是/ 。要特别注意。

## 目录

### etc

初期：早期UNIX中，贝尔实验室的解释是：etcetra directory 。 etc. 就是Et cetra。表示其他、等等什么的，英语里能常常看都这个缩写的。是用来放其他不能归类到其他目录中的内容。
后来FHS规定用来放配置文件，就解释为："Editable Text Configuration" 或者 "Extended Tool Chest"。

### opt

Optional application software packages

http://baike.baidu.com/item/FHS
http://www.cnblogs.com/lifeinsmile/p/4280223.html
http://www.cnblogs.com/happyframework/p/4480228.html



