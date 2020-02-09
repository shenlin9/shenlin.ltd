---
title: Windows - win10 微软输入法的 ctrl + 空格 - 快捷键冲突
categories:
- Windows
tags:
- Windows
- 快捷键冲突
date: 2020-02-09 17:37:42
---

Windows - win10 微软输入法的 ctrl + 空格 - 快捷键冲突

<!--more-->

微软拼音可以切换中英文模式，其默认的切换快捷键为 `ctrl + 空格`，还不能改，但因为
和很多软件如 GoLand 的内置快捷键冲突，所以想更改或禁用，经测试发现网络上说的下面
的方法没用，因为这是微软拼音内部的两种模式切换，不是切换输入法，下面的修改都是针
对切换输入法的，解决方法就是：
* 换个输入法
* 或添加个英语语言，只适用于像我这种以前输入英文也用微软拼音的英语模式的情况，则
  在 GoLand 编码时不用微软拼音的英文模式，用英语美国就可以了

下面的方法对微软拼音没用：

>>> HKEY_CURRENT_USER\Control Panel\Input Method\Hot Keys，保存的是当前用户的快捷键配置；
>>> HKEY_USERS\.DEFAULT\Control Panel\Input Method\Hot Keys，保存的是默认的快捷键配置；
>>> 每次重启之后，系统都会读取默认的快捷键配置，所以还是最好把“当前用户的快捷键配置
>>> ”和“默认的快捷键配置”都修改下，保险
>>> 
>>> `00000070` for the Chinese (Traditional) IME - Ime/NonIme Toggle hotkey
>>> `00000010` for the Chinese (Simplified) IME - Ime/NonIme Toggle hotkey
>>> 
>>> 将 Key Modifiers 中开头的02改为00
>>> 将 Virtual Key 中开头的20改为ff
>>> 
>>> 修改完后注销用户重新登录即可
