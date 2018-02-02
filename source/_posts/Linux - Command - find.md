---
title: Linux - Command - find
categories:
  - Linux
  - Command
tags:
  - find
---

find

<!--more-->

排除多个目录
```
$ find . \( -path './alipay' -o -path './WxpayAPI' \) -prune -o -name '*.php' -print
```
* ./alipay 写成 ./alipay/ 或 alipay 都可以
* 但 '*.php' 必须使用单引号或双引号括起来

```
$ find . \( -path './alipay' -o -path './WxpayAPI' \) -prune -o -type f -name '*.php' -exec dos2unix {} \;
```
* {} 和 \ 之间必须有空格
