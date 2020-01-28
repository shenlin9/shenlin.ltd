---
title: Windows - 固态硬盘 SSD 性能相关 - LPM,HIPM,DIPM
categories:
- Windows
tags:
- Windows
- LPM
- HIPM
- DIPM
date: 2020-01-28 13:42:58
---

Windows - 固态硬盘 SSD 性能相关 - LPM,HIPM,DIPM

<!--more-->

## LPM

LPM:SATA Link Power Management 链路电源管理

和CPU、显卡一样，固态硬盘也具备一套闲置时进入节能状态的机制，也就是LPM节能。

LPM链路电源管理机制由主板PCH芯片中的磁盘控制器和固态硬盘内部的主控进行沟通
，共同决定何时让固态硬盘进入低能耗睡眠状态，并在需要的时候快速唤醒恢复工作。

由主板PCH芯片（主机端）发起的睡眠节能叫HIPM（Host-Initiated LPM），由固态硬盘主
控自主发起的睡眠节能叫做DIPM（Device-Initiated LPM）。

## 判断当前 LPM 的启用状态

HIPM、DIPM的支持由操作系统、磁盘控制器驱动和固态硬盘三方共同决定，要了解自己当前
所用固态硬盘是否开启和支持了HIPM、DIPM功能，可以通过Txbench软件实现，在Drive
Information当中可以看到左侧Supported Features是系统支持的特性，右侧Enabled
Features是当前已启用的特性

## 设置 LPM

要启用SATA LPM节能特性：
1. 在主板BIOS中打开主动LPM支持
2. 需要安装英特尔快速存储技术驱动。微软Windows 10系统自带的storahci驱动默认只有在节能模式下才支持
DIPM。
3. Windows 相关设置

### BIOS 设置

进入BIOS设置，找到Peripherals中的SATA Configuration，比较新的主板当中会有“
Aggressive LPM Support”选项，Disabled即为禁用HIPM主机触发节能
。

如果你的主板设置当中找不到Aggressive LPM Support选项也不要紧，只需找到对应固态硬
盘的SATA端口，将“Hot Plug”热插拔支持选项从Disabled改为Enabled，也能起到禁用
HIPM的效果。

### Windows 相关设置

#### 为电源管理添加节能模式控制选项

powershell 中以管理员身份执行：
```
powercfg -attributes 0012ee47-9041-4b5d-9b77-535fba8b1442 0b2d69d7-a2a1-449c-9680-f91c70521c60 -ATTRIB_HIDE
```

打开“高级电源设置”窗口，在“硬盘”分支下就可以看到新增加的“AHCI Link Power Management - HIPM/DIPM”选项

其下拉列表中几个选项的含义如下：
* HIPM - 主机控制
* DIPM - 设备控制
* HIPM + DIPM - 混合控制
* Lowest - 最低功耗模式
* Active - 关闭节能模式

#### 为电源管理添加低功耗模式自适应选项

目前SSD存在SATA、M.2(NVMe)、PCI-E 3种接口，在电源管理上都是不同的。

** 适用于 SATA SSD **

powershell 中以管理员身份执行：
```
powercfg -attributes 0012ee47-9041-4b5d-9b77-535fba8b1442 dab60367-53fe-4fbc-825e-521d069d2456 -ATTRIB_HIDE
```

然后打开“高级电源设置”窗口，就可以看到新增加的“AHCI Link Power Management - Adaptive”选项

设置的是多少毫秒的空闲则自动从Partical轻度睡眠模式转换到Slumber深度睡眠模式，默
认 100 毫秒，设为 0 则不转换。

LPM节能共有两种模式，轻度睡眠Partical模式和深度睡眠Slumber模式，浅度睡眠恢复到全
速工作的速度较快，深度睡眠节能效果更好但恢复速度较慢。

Intel Battery Life Analyzer 可以分析处于两种模式的时间。

** 适用于 M.2(NVMe) SSD **

powershell 中以管理员身份执行：
```
powercfg -attributes 0012ee47-9041-4b5d-9b77-535fba8b1442 d639518a-e56d-4345-8af2-b9f32fb26109 -ATTRIB_HIDE
```

打开“高级电源设置”窗口，就可以看到新增加的“Primary NVMe Idle Timeout”选项

** 适用于 PCI-E SSD  **

PCI-E 接口的 SSD 还支持一种称为 ASPM(Active State Power Management 活动状态电源
管理) 的电源管理，具体模式分为：L0s、L1、L2。

在“高级电源设置”窗口中展开“PCI Express - 链接状态电源管理”，其选项分别对应上
述模式：
* 关闭：关闭所有链接的 ASPM，即不进行电源管理，设备一直处于开启状态，并且一直是全功率运行，属于最好性能状态。
* 中等电源节省：链接空闲时则尝试 L0s 状态，一段时间内不使用，自动关闭PCIE设备。属于节能和性能兼备
* 最大电源节省量：链接空闲时则尝试 L1 状态，根据你的 PCIE 设备使用情况，实时的调整设备带宽、频率等。属于倾向于节能，设备性能会有所影响。

注意这里所指的PCIE设备并不局限于固态硬盘和常见的显卡，这也是很多人存在的误区，其
实现在芯片组把硬盘、网卡、声卡、显卡、采集卡。。。之类的都归属于PCIE总线，所以，
这里指的PCIE设备也包括上边这些硬件
