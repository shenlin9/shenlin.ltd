---
title: Git - 代理设置 - git proxy
categories:
- Git
tags:
- Git
- git proxy
date: 2020-02-04 09:49:15
---

Windows 下设置 Git 代理

<!--more-->

git 主要有以下三种协议与服务器通信：
* https
* ssh
* git protocol

各自的代理 socks 配置方法不同：
* 使用 HTTPs 协议 时，设置代理需要配置 http.proxy
* 使用 git协议 时，设置代理需要配置 core.gitproxy
* 使用 ssh协议 时，代理需要配置ssh的 ProxyCommand 参数

## 设置 HTTP 代理

直接修改配置文件 `~/.gitconfig` 针对某个网站使用代理：
```
[http "https://github.com/"]
    proxy = https://<host>:<port>/
```

针对单次请求设置代理：
```
$ git clone https://github.com/xxx --config 'http.proxy=socks5://127.0.0.1:1087'
```

针对单个库设置代理：
```
git config http.proxy http://127.0.0.1:1088
```

针对单个库设置代理，且需要鉴权：
```
git config http.proxy http://username:password@127.0.0.1:1088
```

设置全局代理，注意虽然属性名是 http.proxy，但也可代理 https 请求
```
git config --global http.proxy "https://127.0.0.1:1088"
```

设置 SOCKS v5 代理
```
git config --global http.proxy 'socks5://127.0.0.1:1087'
```

设置 SOCKS v5 代理，同时主机名也通过代理来解析，则使用 socks5h
```
git config --global http.proxy socks5h://127.0.0.1:1087
```

取消代理
```
git config --global --unset http.proxy
```

查看代理设置
```
git config --get --global http.proxy
```

如果使用了HTTPS，可能碰到HTTPS 证书错误的情况，比如提示： `SSL certificate
problem...`，此时，可以尝试将 sslVerify 设置为 false ：
```
git config --global http.sslVerify false
```

## 设置 git 协议代理

git 协议比较特殊，有专门一个配置 core.gitproxy 做代理，但需要额外写一个脚本，才
能使用上 socks 代理。

`~/.gitconfig` 配置如下：
```
[core]
    gitproxy = /path/to/gitproxy for github.com
```
或执行如下代码：
```
git config --global core.gitproxy "/path/to/gitproxy for github.com"
```

gitproxy 脚本例子：
```
#!/bin/sh
#
# Proxy wrapper for git protocol (9418).
#
# git config --global core.gitproxy "ido gitproxy"
#
exec /usr/bin/nc -X 5 -x <socks_host>:<socks_port> $1 $2
```

## 设置 SSH 代理

SSH 代理最方便之处就是无需输入密码

SSH 协议与 git 关系不大，是 git 调用 SSH 命令与服务器通信，修改 SSH 配置文件
`~/.ssh/confg`：

针对某网站的代理
```
Host github.com
    User git
    ProxyCommand /usr/bin/nc -X 5 -x <socks_host>:<socks_port> %h %p
```

全局的代理
```
# 这里的 -a none 是 NO-AUTH 模式，参见 https://bitbucket.org/gotoh/connect/wiki/Home 中的 More detail 一节
ProxyCommand connect -S 127.0.0.1:1087 -a none %h %p

Host github.com
  User git
  Port 22
  Hostname github.com
  # 注意修改路径为你的路径
  IdentityFile "C:\Users\T460P\.ssh\id_rsa"
  TCPKeepAlive yes

Host ssh.github.com
  User git
  Port 443
  Hostname ssh.github.com
  # 注意修改路径为你的路径
  IdentityFile "C:\Users\T460P\.ssh\id_rsa"
  TCPKeepAlive yes
```
