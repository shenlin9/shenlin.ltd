---
title: windows - win10 激活密钥过期
categories:
  - Windows
tags:
  - Windows
---

使用网友的KMS服务器延长激活

详见：https://03k.org/kms.html

<!--more-->

## Win10

### 查询过期时间

Win + r 输入 

“slmgr.vbs -xpr”会提示 Win10 过期时间

“slmgr.vbs -dlv”会显示更详细的密钥信息

### 激活

管理员身份运行 cmd
```
slmgr /skms kms.03k.org
slmgr /ato
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
