---
title: Windows - PowerShell - 安装 git
categories:
  - Windows
  - PowerShell 
tags:
  - 
---

https://github.com/dahlbyk/posh-git

<!--more-->

## 安装 

## 安装 posh-git

查看执行策略列表
```
λ  Get-ExecutionPolicy -List

        Scope ExecutionPolicy
        ----- ---------------
MachinePolicy       Undefined
   UserPolicy       Undefined
      Process          Bypass
  CurrentUser          Bypass
 LocalMachine       Undefined
```

把 Bypass 都改为 RemoteSigned
```
λ  Set-ExecutionPolicy RemoteSigned -Scope  Process -Confirm

λ  Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Confirm
```

确认当前策略 RemoteSigned 已生效
```
λ  Get-ExecutionPolicy
RemoteSigned
```
