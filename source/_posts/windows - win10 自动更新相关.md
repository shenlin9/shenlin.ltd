---
title: windows - win10 自动更新相关
categories:
  - Windows
tags:
  - Windows
---

win10 自动更新相关

<!--more-->

## 更新报错

0x80240034的报错一般和系统更新相关的服务缺失有较大关联，不排除其他因素。

1.查看windows update，app readiness，Cryptographic Services，Background Intelligent Transfer Service，Windows Installer是否是正常开启的状态。

2.C盘空间预留确保25GB以上。

3.如果有外接设备，将外接设备移除。

4.把C:\$WINDOWS.~BT这个文件夹删除,建议做一个文件备份。 

5.如果有，打开第三方防护以及优化软件，查看优化项目列表里面是否有系统服务，将优化项目还原。

6.安装有某些网银插件也有可能导致此问题出现,卸载排除一下问题。

7.是否有安装winrar，Mactype，火绒，start back，WindowsBlinds，卸载再查看。

```
net stop wuauserv
net Stop cryptSvc
net Stop bits
net Stop msiserver
::注释
::ren C:\Windows\SoftwareDistribution SoftwareDistribution.old
::ren C:\Windows\System32\catroot2 catroot201d
net Start wuauserv
net start cryptSvc
net Start bits
net start msiserver
pause
```

## 半年频道

为了帮助与 Windows 10 和 Office 365 专业增强版保持一致，微软采用新术语，更改如下：

    Current Branch (CB) 称为半年频道（定向）[Semi-Annual Channel (Targeted)]，
    Current Branch for Business (CBB) 将被称为半年频道(Semi-Annual Channel)。
    Long-Term Servicing Branch (LTSB) 将被称为 Long-Term Servicing Channel (LTSC)。

Windows 10 提供 3 个服务渠道。

    Windows 预览体验计划为组织提供测试将通过下一次功能更新推出的功能并提供相应反馈的机会。

    半年频道通过每年两次的功能更新发布提供新功能。在半年服务渠道中，功能更新在 Microsoft 发布后即可使用， 组织可以选择何时部署半年频道的更新。

    定向部署(Targeted deployment)是指当一个新的Windows版本发布时，在组织中进行试点测试，通常选择配置较新的设备，如 Surface。
    
    广泛部署(Broad deployment)是指在定向部署有成功的反馈后，才会在组织中其他计算机上进行部署。

    仅用于控制医疗设备或 ATM 机等专用设备（通常不运行 Office）的 Long Term Servicing Channel，大概每三年接收一次新功能发布。

Current Branch (CB) 半年通道（定向/目标）
Current Branch for Business (CBB) 半年通道

## 更新挂起通常解决方法 

1.“Windows+X”>>计算机管理>>服务和应用程序>>服务，找到Windows update和Background Intelligent Transfer Service服务，关闭。
2.删除路径 C:\Windows\SoftwareDistribution\DataStore和C:\Windows\SoftwareDistribution\Download下的所有文件。
3.重新开启Windows update和Background Intelligent Transfer Service服务。
4.再次尝试升级更新。


在管理员命令提示符下键入以下命令：

Dism /Online /Cleanup-Image /ScanHealth
这条命令将扫描全部系统文件并和官方系统文件对比，扫描计算机中的不一致情况。

Dism /Online /Cleanup-Image /CheckHealth 
这条命令必须在前一条命令执行完以后，发现系统文件有损坏时使用。

DISM /Online /Cleanup-image /RestoreHealth 
这条命令是把那些不同的系统文件还原成官方系统源文件。

完成后重启，再键入以下命令：sfc /SCANNOW
