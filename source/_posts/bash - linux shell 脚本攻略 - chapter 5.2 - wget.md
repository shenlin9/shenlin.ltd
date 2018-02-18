---
title: bash - linux shell 脚本攻略 - chapter 5.2 - wget
categories: 
 - linux
 - bash
tags: 
 - linux
 - bash
 - wget
---

wget 是一个用于下载文件的命令行工具，选项繁多使用灵活。

<!--more-->

```bash
# 下载文件
-> wget http://www.baidu.com/

# 下载多个文件
-> wget http://www.baidu.com/ http://www.sohu.com/

# 指定保存的文件名
-> wget http://www.baidu.com/ -O baidu

# 下载过程不作任何输出，把进度写入日志文件
-> wget http://www.baidu.com/ -o baidu.log

# 指定重试的次数
-> wget -t 2 http://www.google.com/

# 要不停的一直重试
-> wget -t 0 http://www.google.com/

# 限制下载速度,k 或 m 字节
-> wget --limit-rate 1k  http://www.sohu.com/

# 限制占用的空间 ??? 只针对多个文件时有效？一个文件没测试成功
# Q quota 配额
-> wget -Q 0.5k  http://www.sohu.com/

# 断点续传
-> wget -c  http://www.sohu.com/

# 访问需用户名、密码认证的网页
-> wget --user shenlin --password shuang http://www.baidu.com/

# 询问密码
-> wget --user shenlin --ask-password  http://www.baidu.com/
```

完整复制整个网站建立网站镜像
```bash
-> wget --mirror --convert-links baidu.com

-> wget -Nkrl 2 http://www.baidu.com/
```
* -k 或 --convert-links 都是把页面的链接地址转换为本地地址
* -N 使用文件的时间戳
* -r 递归下载
* -l 指定下载层级
