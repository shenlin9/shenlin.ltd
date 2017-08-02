title: Chocolatey
categories:
  - Windows
tags:
  - Windows
  - PowerShell
  - Chocolatey

---

Chocolatey 是 Windows 下的软件包管理工具。

<!--more-->

Cmder 和 Chocolatey 依赖于 PowerShell 2+，win7 自带 2.0

Chocolatey 可以安装 NeoVim,Cmder
https://github.com/neovim/neovim/wiki/Installing-Neovim

安装完 NeoVim 再安装 NyaoVim
https://www.npmjs.com/package/nyaovim

## PowerShell

查看 PowerShell 版本
```
PS C:\Windows\system32> $PSVersionTable.PSVersion

Major  Minor  Build  Revision
-----  -----  -----  --------
2      0      -1     -1
```

依赖关系

> powershell 4.0 需要 Windows Management Framework 4.0，
下载地址：http://www.microsoft.com/zh-CN/download/details.aspx?id=40855

> Windows Management Framework 4.0 需要 Microsoft .NET Framework 4.5
下载地址：https://www.microsoft.com/zh-CN/download/details.aspx?id=30653

执行策略

详细介绍：https://technet.microsoft.com/zh-CN/library/hh847748.aspx

查看执行策略
PowerShell  默认执行策略为“Restricted”
```
PS C:\Windows\system32> Get-ExecutionPolicy
Restricted
```

PowerShell 学习网站
http://www.pstips.net/

## Chocolatey

https://chocolatey.org/install

安装 Chocolatey 执行策略必须不能是 "Restricted" 所以修改为下面两种之一
```
Set-ExecutionPolicy AllSigned
Set-ExecutionPolicy Bypass
```

以管理员身份启动 PowerShell，执行下列命令安装 Chocolatey
```
iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))
```

验证安装
```
choco
choco -?
```

命令帮助
```
choco <command> --help
```

常用命令
```
//
λ clist -l
λ clist -li
λ choco list -lai
λ choco list --page=0 --page-size=25

* l 本地 --local-only
* a 所有版本
* i 包含上系统中没通过choco安装的程序

//
λ choco search neovim


//
λ choco info neovim

//
λ cinst -y neovim
λ choco install -y neovim
λ choco install -y neovim googlechrome atom 7zip
λ choco install neovim --force --force-dependencies

//可以直接从本地安装的是 nuspec/nupkg 文件
λ choco install <path/to/nuspec>
λ choco install <path/to/nupkg>

//升级时被钉住不能升级的包
λ choco pin   
λ choco pin list  
λ choco pin add -n=git
λ choco pin add -n=git --version 1.2.3
λ choco pin remove --name git

//列表已经过时可升级的包
λ choco outdated

//升级
λ cup neovim
λ choco upgrade neovim
λ choco upgrade all
λ choco upgrade all --except="'skype,conemu'"

//卸载
λ cuninst neovim
λ choco uninstall neovim git
λ choco uninstall -y neovim --remove-dependencies
```
