---
title: Python - Sockets
categories:
- Python
tags:
- Python
- Sockets
date: 2019-05-25 13:42:04
---

Python - Sockets

<!--more-->

## Sockets

套接字允许位于同一台机器上的不同进程之间进行通信，或者在不同环境中工作的不同机器
通信，甚至可以跨大陆通信。

套接字是双向通信链路的端点。端点是IP地址和端口号的组合。

对于客户机-服务器通信，将在两端配置套接字来发起连接，侦听传入消息，然后在两端发
送响应，从而建立双向通信。

Python的套接字接口类似于 C 和 Java。但是在 Python 中使用套接字要简单得多。

可以使用 Python 提供的两种 API 库进行套接字编程：
* 在底层，Python 使用 `sockets` 库为无连接和面向连接的网络协议实现客户机和服务器
  模块。
* 在更高级别上，可以使用诸如 ftplib 和 httplib 之类的库与 FTP 和 HTTP 之类的应用
  程序级网络协议进行交互。

## Create Sockets Object

使用 `socket` 模块的 `socket.socket()` 方法创建套接字：
```python
sock_obj = socket.socket(socket_family, socket_type, protocol = 0)
```
* socket_family: 定义了用作传输机制的协议族。它可以有两个值：
    * AF_UNIX
    * AF_INET(IP version 4 or IPv4).
* socket_type: 定义两个端点之间的通信类型。它可以有以下值：
    * SOCK_STREAM (用于面向连接的协议，例如 TCP)
    * SOCK_DGRAM (用于无连接的协议，例如 UDP)
* protocol: 我们通常保留这个字段或将这个字段设置为 0

例如：
```python
import socket
try:
    sk = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
except socket.error as er:
    print('Err Code:{}\nErr Msg:{}'.format(er[0], er[1]))

print('Socket Initialized')
```

## Socket Method

套接字方法可分为 3 类：
* 服务器套接字方法
* 客户都套接字方法
* 通用套接字方法

### Server Socket Methods

* sock_object.bind(address):
    * 此方法将套接字绑定到地址(主机名、端口号对)
* sock_object.listen(backlog):
    * 此方法用于侦听与套接字关联的连接
    * `backlog` 参数表示排队连接的最大数量，最大值为 5，最小值为 0
* sock_object.accept():
    * 此方法返回一个套接字对象，它与使用 `socket.socket()` 创建的套接字对象不同。
    * 此方法返回的是 `(conn, address)` 对，其中 `conn` 是用于在通信通道上发送和
      接收数据的新套接字对象，而 `address` 是通道另一端绑定到套接字的IP地址。
    * 这个新的套接字对象专门用于管理与 accept 所在的特定客户端进行通信。
    * 此机制还可以帮助服务器同时维护与 n 个客户机的连接。

### Client Socket Methods

* sock_object.connect():
    * 此方法用于连接客户端到主机和端口，并启动到服务器的连接。

### General Socket Methods

* sock_object.recv():
    * 当协议参数的值为TCP时，使用此方法在端点接收消息
* sock_object.send():
    * 如果协议是TCP，则应用此方法从端点发送消息
* sock_object.recvfrom():
    * 如果使用UDP协议，则调用此方法在端点接收消息
* sock_object.sendto():
    * 如果协议参数为UDP，则调用此方法从端点发送消息
* sock_object.gethostname():
    * 此方法返回主机名
* sock_object.close():
    * 此方法用于关闭套接字。远程端点不会从这一端接收数据。

## 

![Python Socket Programming WorkFlow](https://cdn.techbeamers.com/wp-content/uploads/2016/02/Python-Socket-Programming-WorkFlow.png "Python Socket Programming WorkFlow")
