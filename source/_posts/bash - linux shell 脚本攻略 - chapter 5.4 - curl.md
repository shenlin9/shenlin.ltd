---
title: bash - linux shell 脚本攻略 - chapter 5.4 - curl
categories: 
 - Linux
 - Bash
tags: 
 - curl
---

wget 默认把下载文件保存的磁盘，把进度信息输出到 stdout，

而 curl 默认把下载文件的内容输出到 stdout，把进度信息输出到 stderr

<!--more-->

```bash
# -s silent 出错时不输出信息
-> curl http://www.baiduxxxxxxx.com/
curl: (6) Couldn''t resolve host 'www.baiduxxxxxxx.com'

-> curl -s http://www.baiduxxxxxxx.com/

-> curl -t http://www.baidu.com/
```
