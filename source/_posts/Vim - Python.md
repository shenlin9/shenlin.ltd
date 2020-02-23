---
title: Vim - Python 相关
categories:
  - Vim
tags:
  - Vim
  - Python
---

Vim 在编译时，指定了 Python 的具体版本号，所以若 Vim 编译时指定的 Python3 版本是 3.6，则系统安装最新版本 3.7 也对 Vim 无效。

<!--more-->

## Vim 编译时是否加入了 Python 支持？

Vim 中运行 `:version` 查看是否有 `+python/dyn`，`+python3/dyn`

## 检测目前系统的 Python 是否满足了 Vim 需求？

`:echo has("python")` 检测当前系统是否满足了 Vim 需要的 Python2.x 版本

`:echo has("python3")` 检测当前系统是否满足了 Vim 需要的 Python3.x 版本

## 确认 Vim 中指定的 Python 版本号

没有提示错误，则表示系统安装的 Python 版本符合 Vim 的要求

`:py import sys; print(sys.version)`

`:py3 import sys; print(sys.version)`

