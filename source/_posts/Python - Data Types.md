---
title: Python - Data Types
categories:
- Python
tags:
- Python
- Python Data Types
date: 2019-04-30 16:27:34
---

Python - Data Types

<!--more-->

## Booleans

True    False

## Numbers

参考

## Strings

参考

## Bytes

Python 中的 byte 是可不变类型，可以通过索引读取字节，但不能修改其值。
```python
>>> type(bytes(4))
<class 'bytes'>

>>> print(bytes(4))
b'\x00\x00\x00\x00'

>>> b = bytes(3)
>>> b[1]
0
>>> b[1] = 1
TypeError: 'bytes' object does not support item assignment
```

Byte 和 String 区别：
* Byte 对象包含字节序列，而 String 对象包含字符序列
* Byte 对象是机器可读的对象，而 String 对象是人类可读的形式
* Byte 对象机器可读，则可以直接写入磁盘，而 String 对象在写入磁盘前需要先编码

对于带缓存的 I/O 操作，Byte 对象很重要，例如一个程序连续通过网络接收数据，在消息
头和终止符出现在流中时解析数据，并把不断传入的字节追加到缓冲区，使用 Python 的
Byte 对象则很容易实现上，伪代码如下：
```python
buf = b''
while message_not_complete(buf):
    buf += read_from_socket()
```

## Lists

参考

## Tuples

参考

## Sets

参考

## Dictionaries

参考
