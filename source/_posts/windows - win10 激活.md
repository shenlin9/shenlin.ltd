---
title: windows - win10 激活
categories:
  - Windows
tags:
  - Windows
---

激活方法

<!--more-->

## Win10

### 查询过期时间

Win + r 输入 

“slmgr.vbs -xpr”会提示 Win10 过期时间，若使用下述的数字证书激活则会提示"已永久激活"

“slmgr.vbs -dlv”会显示更详细的密钥信息

### 激活

#### KMS 激活

```
slmgr /skms kms.03k.org
slmgr /ato
```

#### 密钥激活

```
slmgr.vbs /upk
slmgr /ipk VK7JG-NPHTM-C97JM-9MPGT-3V66T 
slmgr /skms zh.us.to
slmgr /ato
```

#### 数字许可证永久激活

什么是“数字权利激活”？

数字许可证激活是 Windows 10 中新加入的激活方式，是一种授权方法的分类。数字许可证
会记录您的硬件设备信息，只要在CPU和主板设备没有更换的情况下就可以连接微软服务器
自动永久性的激活系统，重新安装系统时无需再次输入产品密钥，安装后会自动永久激活。
“数字权利激活”在不更换电脑硬件的情况下一直有效，无论您安装的系统是正式版还是预
览版，不影响激活效果。

参见：http://www.yipojie.cn/4339.html/comment-page-2#comments
下载工具 `c# 版 v2.6.6` 成功激活:
```
+++++++++++++++++++++++++++++++++++++++++++++
产品名称: Professional [17134.1]
20:36:08 正在准备...
20:36:08 正在安装密钥：VK7JG-NPHTM-C97JM-9MPGT-3V66T
成功地安装了产品密钥 VK7JG-NPHTM-C97JM-9MPGT-3V66T。

20:36:10 SKU: 48
20:36:10 正在添加注册表项...
20:36:10 正在执行 GatherOsState.exe 获取门票...
20:36:21 正在删除注册表项...

20:36:21 正在应用门票 GenuineTicket.xml...
Done.
Converted license Microsoft.Windows.48.X19-98841_8wekyb3d8bbwe and stored at C:\ProgramData\Microsoft\Windows\ClipSvc\Install\Migration\9e5f37dc-ed82-4cab-a429-6b054df29425.xml.
Successfully converted 1 licenses from genuine authorization tickets on disk.
Done.
+++++++++++++++++++++++++++++++++++++++++++++
20:36:22 正在激活系统...
正在激活 Windows(R), Professional edition (4de7cb65-cdf1-4de9-8ae8-e3cce27b9f2c) ...
成功地激活了产品。

20:36:25 正在删除临时文件...
20:36:25 完成。
```

## Office

### 查询过期时间

```
cscript "C:\Program Files (x86)\Microsoft Office\Office16\OSPP.VBS" /dstatus

    SKU ID              为激活密钥
    REMAINING GRACE     为剩余天数
    LICENSE DESCRIPTION 注意office必须是vol版本，否则无法用下面方法激活

slmgr /xpr  <SKU ID>

    也可以这样直接显示过期日期
```

### 激活

管理员身份运行 cmd
```
cscript "C:\Program Files (x86)\Microsoft Office\Office16\OSPP.VBS" /sethst:kms.03k.org
cscript "C:\Program Files (x86)\Microsoft Office\Office16\OSPP.VBS" /act
```
