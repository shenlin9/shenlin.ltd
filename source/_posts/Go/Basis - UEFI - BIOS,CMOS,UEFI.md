---
title: Basis - UEFI - BIOS,CMOS,UEFI
categories:
- Basis
tags:
- Basis
- BIOS
- CMOS
- UEFI
date: 2020-02-27 11:31:36
---

Basis - UEFI - BIOS,CMOS,UEFI

<!--more-->

## BIOS

BIOS，Basic Input/Output System

是一个固件(固化软件，不能被轻易更改)，存储在主板的 BIOS 芯片上，

BIOS 芯片是非挥发性的，即断电后固件程序不会丢失。

BIOS 芯片里只保存了 BIOS 固件，没有保存 BIOS 的设置，如启动顺序、系统时间等，这

些数据保存在了 CMOS 芯片里。

BIOS 负责通电自检(POST，power-on self-test)，检查硬件状态，

若有硬件有问题，则通过 beep 的响声来提示问题的种类，

若自检通过后则调用 boot loader 或直接启动系统。

## CMOS

CMOS，Complementary Metal-Oxide-Semiconductor，互补金属氧化物半导体

指的是主板上的 CMOS 芯片，是挥发性的，即断电后数据丢失。

若 CMOS 被断电，则保存的 BIOS 设置丢失，会恢复到出厂时的 BIOS 设置。

以前的主板有独立的 CMOS 芯片，现在的主板都把此芯片集成到了主板南桥里。

## UEFI

UEFI : Unified Extensible Firmware Interface，统一可扩展固件接口

UEFI是BIOS的升级替代方案。

## MBR

## GPT

UEFI + GPT 支持Secure Boot。
通过保护预启动或预引导进程，抵御bootkit攻击，从而提高安全性。
所有在开机时比Windows内核更早加载，实现内核劫持的技术，都可以称之为Bootkit。

BIOS+MBR的系统引导文件可以和系统文件在同一分区的根目录，也可以不与系统文件同一分区，只要系统引导文件所在分区为活动的主分区即可启动操作系统；而UEFI+GPT只能把系统引导文件放置在ESP分区，且操作系统必须在另外的主分区，也就是说，UEFI+GPT强制要求系统启动文件与系统文件必须分离，不在同一分区。

因为MBR和PBR很隐蔽，而且又是在启动过程中最先执行的，木马病毒会想方设法地躲到这里，达到劫持系统、自我保护的目的。(这一类木马有个名字，叫BootKit)。

传统BIOS下，开机启动(引导)的过程是这样的:1.加载并执行BIOS2.BIOS会进行开机硬件自检(POST)。没问题的话，就继续加载并执行硬盘MBR里的启动代码(没有文件实体)3.MBR启动代码会找到这块硬盘的活动分区，然后执行这个分区的PBR代码(没有文件实体)4.PBR代码会找到这个分区里的bootmgr文件，然后把它加载执行5.bootmgr会在这个分区里找到启动配置数据库(\Boot\BCD)，这样就可以显示出操作系统选择菜单了。如果菜单只有一项可选，那就直接启动，不显示菜单6.内核与各种驱动被加载，系统控制权开始从bootmgr转交给Windows7.然后，加载服务、桌面等等……

微软联合硬件厂商，为了快速开机/傻瓜化维护，搞了很蛋疼的bootmenupolicy，让引导器不再负责显示操作系统选择菜单，这就是把F8、DEL、F12都废了，强推WinRE。开机时，引导器会直接忽略F8，执拗地启动默认的操作系统启动项，如果默认的操作系统启动时出现问题，就进WinRE执行“自动诊断”。如果Windows启动到半路时没出问题，而且发现你安装了多个操作系统，则会调用bootim.exe搞一个“假”的操作系统选择菜单出来。你可以选择继续，也可以选择启动其他操作系统——这个时候，实际上执行的是修改BCD设置->重启这个动作。

用bootice这个工具可以关闭bootmenupolicy。bcd编辑，智能编辑模式，去掉每个启动项的“启用Win8 Metro启动界面”的勾，就可以了，启动时多系统选择界面就变回传统的了！

