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

Git支持四种协议，而除本地传输外，还有：git://, ssh://, http:// 协议，
`git clone https://github.com/username/repo.git`
`git clone git@github.com:username/repo.git`

使用 HTTP协议 时，设置代理需要配置 http.proxy
使用 git协议 时，设置代理需要配置 core.gitproxy
而是用 ssh协议 时，代理需要配置ssh的 ProxyCommand 参数

## 设置 HTTP 代理

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

设置全局代理，注意此代理也可代理 https 请求
```
git config --global http.proxy "http://127.0.0.1:1088"
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

如果使用了HTTPS，可能碰到HTTPS 证书错误的情况，比如提示： SSL certificate
problem 。。。，此时，可以尝试将 sslVerify 设置为 false ：
```
git config --global http.sslVerify false
```


## 设置 git 协议代理

```

```

## 设置 SSH 代理

SSH 代理最方便之处就是无需输入密码

修改如下文件：

linux
```
vi ~/.ssh/config
```

windows
```
C:\Users\用户名\.ssh\config
```

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
