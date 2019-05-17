---
title: Python - File Handling
categories:
- Python
tags:
- Python
- File
date: 2019-05-15 16:42:40
---

Python - File Handling

<!--more-->

## File

File 是系统存储上的一个命名位置，它记录数据以便以后访问。它支持在非易失性内存(如
硬盘)中进行持久存储。

在 Python 中，文件处理按以下顺序进行：
* 打开一个返回文件句柄的文件。
* 使用句柄执行读或写操作。
* 关闭文件句柄。

文件句柄即文件对象

## Open A File

使用 `open()` 函数打开文件：
```python
open(file, mode='r', buffering=-1,
     encoding=None, errors=None, newline=None, closefd=True, opener=None)
```

常用选项：
```python
file_object = open(file_name [, access_mode][, buffering][, encoding])
```

**buffering**

可选参数，
默认值为 0，表示不缓冲。
如果值为 1，则在访问文件时按行使用缓冲。
如果大于 1，则缓冲操作将根据缓冲区大小运行。
如果是负值，则使用默认行为。

**access_mode**

可选参数，是一个整数，代表文件的打开模式，如只读、只写、追加等。

默认值为 `r` 只读模式

其取值为左边三个值任一和右边三个值任一搭配，共 3 x 4 = 12 组合：

    r/w/a       无或t/b/+/b+

含义：

    r   只读模式，且以文本形式从文件读取数据
    w   aa
    a   追加模式

    b   以字节形式从文件读取数据
    +   aa
    b+  aa

搭配组合和含义如下：

    r	It opens a file in read-only mode while the file offset stays at the root.
    rb	It opens a file in (binary + read-only) modes. And the offset remains at the root level.
    r+	It opens the file in both (read + write) modes while the file offset is again at the root level.
    rb+	It opens the file in (read + write + binary) modes. The file offset is again at the root level.

    w	It allows write-level access to a file. If the file already exists, then it’ll get overwritten.
        It’ll create a new file if the same doesn’t exist
    wb	Use it to open a file for writing in binary format. Same behavior as for write-only mode.
    w+	It opens a file in both (read + write) modes. Same behavior as for write-only mode.
    wb+	It opens a file in (read + write + binary) modes. Same behavior as for write-only mode.

    a	It opens the file in append mode. The offset goes to the end of the file.
        If the file doesn’t exist, then it gets created.
    ab	It opens a file in (append + binary) modes. Same behavior as for append mode.
    a+	It opens a file in (append + read) modes. Same behavior as for append mode.
    ab+	It opens a file in (append + read + binary) modes. Same behavior as for append mode.

**encoding**



## File Object Attributes

`open()` 函数返回的对象即为文件句柄

可以通过 `dir()` 查看其属性：
```python
>>> f = open('1.txt')
>>> dir(f)
['_CHUNK_SIZE', '__class__', '__del__', '__delattr__', '__dict__', '__dir__',
'__doc__', '__enter__', '__eq__', '__exit__', '__format__', '__ge__',
'__getattribute__', '__getstate__', '__gt__', '__hash__', '__init__',
'__init_subclass__', '__iter__', '__le__', '__lt__', '__ne__', '__new__',
'__next__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__',
'__sizeof__', '__str__', '__subclasshook__', '_checkClosed', '_checkReadable',
'_checkSeekable', '_checkWritable', '_finalizing', 'buffer', 'close', 'closed',
'detach', 'encoding', 'errors', 'fileno', 'flush', 'isatty', 'line_buffering',
'mode', 'name', 'newlines', 'read', 'readable', 'readline', 'readlines',
'reconfigure', 'seek', 'seekable', 'tell', 'truncate', 'writable', 'write',
'write_through', 'writelines']
```

文件对象的常用属性：
* closed 是否已关闭
* mode 打开模式
* name 文件名
* softspace 一个布尔值，表明在 `print` 命令打印另一个值之前，是否会添加一个空格
  字符。??? 没有

```python
>>> f = open('1.txt')
>>> f.closed
False
>>> f.mode
'r'
>>> f.name
'1.txt'
>>> f.softspace
AttributeError: '_io.TextIOWrapper' object has no attribute 'softspace'
```

注意： `open()` 方法的 `w` 模式会在打开文件时就清空文件内容：
```python

```

## File Encoding

Python 3 中的字符串(文本)和字节(8位)之间有明显的区别，这表示字符 'a' 不代表
ASCII 值 97，除非您这样指定它。因此，当您希望以文本模式使用文件时，最好使用正确
的字符集。

此外，文件在磁盘上以字节的形式存储，因此在读取文件之前需要对字符串进行解码。同样
，在向文件写入文本时对它们进行编码。

Python默认启用了与平台相关的编码。因此，如果您不更改它，那么Windows下它将被设置
为 `cp1252`，Linux下它将被设置为 `utf-8`。

打开文件时可以指定文件编码：
```python
f = open('app.log', mode = 'r', encoding = 'utf-8')
```

注意：在 Python 2.x 中，需要导入 `io` 模块才能启用编码特性，而 Python 3.x 已经隐
式的导入了此模块。

## Close A File

虽然 Python 运行了一个垃圾收集器来清理未使用的对象，但当工作完成时，自己关闭文件
才是最佳实践，而不是把它留给 GC。

为避免在执行文件操作时发生异常，导致关闭文件的代码不能被执行，可以把相关的文件操
作放在 `try finally` 代码块中：
```python
try:
    f = open('1.txt')
    ....
    ... file operation ...
    ....
finally:
    f.close()
```

另外一种确保关闭文件的方法是使用 `with` 子句，它可以确保 `with` 子句里的代码块执
行完毕后关闭文件(注意只是关闭文件并不是删除了文件对象)，另外这个方法的优点是它不
需要显式地调用文件的 `close()` 方法：
```python
>>> with open('1.txt', 'w', encoding='utf-8') as f:
...     f.write('aaa')
...
3

>>> f.closed
True
```

打开失败的文件，文件对象就是关闭的，且多次关闭也不会报错：
```python
>>> f = open('1.txt','r')
FileNotFoundError: [Errno 2] No such file or directory: '1.txt'
>>> f.closed
True
>>> f.close()
>>> f.close()
```

## write()

`write()` 方法可以写入字符串或字节序列，返回的是每次调用写入的数据长度：
```python
>>> with open('1.txt','w',encoding='utf-8') as f:
...     f.write('first line\n')
...     f.write('second line\n')
...     f.write('third line\n')
...
11
12
11
```

## readlines()

```python
>>> with open('1.txt', 'r', encoding='utf-8') as f:
...     c = f.readlines()
...
>>> for l in c:
...     print(l)
...
first line

second line

third line
```

## read([size])

读取指定长度的数据，未指定长度则读取剩下的全部数据：
```python
>>> with open('1.txt','r',encoding= 'utf-8') as f:
...     f.read(3)
fir
...     f.read(5)
st li
...     f.read()
ne
second line
third line
...
```

按字节读取二进制文件
```python
>>> with open('1.png','rb') as f:
...     f.read(3)
...     f.read(3)
...     f.read(3)
...     f.read()
...
b'\x89PN'
b'G\r\n'
b'\x1a\n\x00'
b'\x00\x00\rIHDR\x00\x00\x00\x96\x00\x00\x00\x96\x01\x03\x00\x00\x00\x06\xcf\xe3\xa1\x00\x00\x00\x06PLTE\xff\xff\xff\x00\x00\x00U\xc2\xd3~\x00\x00\x02JIDATH\x89\xad\x961\xae\xab0\x10E\xc7\xa2\xa0\x8b7\x10\xd9\xdb\xa0\xb0\xc4\x96R\xd2\xe1\x8e2[B\xa2\xc86\x8c\xb2\x01\xa7\xa3\xb0|\xff\x1d\xde\x7f\xbf\xe6=\x7f\n\x04GQ\x98\x19\xdf\xb93\x02`\x91t\x0f=\x90\xc6W\xb4@\x91\xab,I\xbfd\']\x9co\x12D\xac\xf0\xde\xc2v\xbcb\xde\xd3xX1e\xe8\xad/?b\x8b\xdd\xb1-\xb8\xb9\x11\xc7\x7fap\xc2oT\t\x8343\xcd\xd7\xa6\x8ee\xfc\xecX\xa7\x7f5\xb8\xc2\xb4\xf6\xd6\xdd\xc3\xf4\xf8\xec\x1bC\xfd>\x8f\xdf2\xbd2R\x98\xe6\xca \'\xf9\xbe~\xcdB\x1f\xad\xc7\x16\xa5\xfa\xed\x15\x05\xefr\x99\xb9\xc0\x03Lw\x99\x9f\xd90\xbel\xf8\xb3\x16\xc6\xfa\x1d\xf0Z9\xd4\xeeud`md\xd2\xc3\x94qy\xde\\\xb7\x8a\x96\xf223\xa5_\xf8\x7f@\xce\xae;&k\x98o\x0b\xab\xccWR\xb7\x1db\xceGC]\xb50\x86\xcb\x0f\xa40\xe3f\x80h\xc5\x97\xcbl_\x07\x81\xeb\nu\xe07\xf6Lb\xd2-L:\xd5)_\xe3\xa7\x8e|\xddShc<\x0f\xf1\xef-\xda\x9b\x8c+e\xe2\xcbe\x862\x9e\xfe\x12\x1f\x06\x0c5{\xb4\xb1\x14\x86)\x9b7#\xab"#\xa8\x95\xd0\xc4\xaa\xf6Q\xa28\xb2\xe5\xe7\x16\xb6\xf4\x0f\x98\xa8!\xbdK\xff\xb4\xd8\x8e\x1e\xd2\x95&f\xd6\xf1\x10\xa8a\xd9\xda\xad\x13\xf4,[\x98\xa7B3K7\xe7\xca\x9e\x8e\xd6\xb0~WY\xa2y\x83\xf9.\xcf\xea\xc2\x08\r\xb5\x89\x01jp\xf4\x97|s"\x93\x98\x14\x9a\x18\xf3\xe5\\P/f\xe0\xb4\x8b\xfaU\xbfKl\xe7\x838_\xe6L\x1f\xc7\x92\xeb\xd9G\xbfgB\x83\xd3|\x0f\xedA\x96R\xe7L\x0b\xab\xe3)\x08\xf6`V\x17\xcc\\\x15.3\xd7\xbd\xce\x19%\x8fj\xd83g\xadZ\x98\x8a3\xabI=\x84\xf3\x1c\\3\xda\x98\x0b=G9c\xce:\xb7\x16T\xbf^f\xa7\xbf\xb8\xfb\x88l\xb87ATW-\x8cF\x1c\x91\xb87=x\x1ff\xab\xc3\xa6\x85q\xe6\xc5\xac\x06\xfad\x07\r*\x93\xebL\xf70\x9c\xba\xa2d\xa9S\xdd-\x1a\xd9\x92\xf1\xe6\xfc5\xfb\xb6\xce\\\xa1\xa4\x95\xd1\x188\x99\xe7\xcf\xae\'\xe1\xfe\xee\x98W\xd8\x99\xafc\xff\xe2S\xbbc>gT\x0b;k\xaf\x86\xf9\x00\xc7\x1f]\xcb7\xb1?\x03\xdd\x80$\xd4V\xb3Q\x00\x00\x00\x00IEND\xaeB`\x82'
```

## tell()

返回文件指针的当前偏移量
```python
>>> f = open('1.png','rb')
>>> f.tell()
0

>>> f.read(5)
b'\x89PNG\r'
>>> f.tell()
5

>>> f.read(5)
b'\n\x1a\n\x00\x00'
>>> f.tell()
10
```

## seek(offset[, from])

设置文件指针的偏移量，并返回当前绝对偏移量

offset  表示位移的相对量
from    表示位移的起点位置：
* 0 从文件开头开始
* 1 从当前文件指针所在的位置开始(文本文件不支持)
* 2 从文件末尾开始

```python
>>> f = open('1.png','rb')
>>> f.seek(2,0)
2
>>> f.seek(8,0)
8

>>> f.seek(2,1)
10
>>> f.seek(2,1)
12

>>> f.seek(0,2)
661
>>> f.seek(-2,2)
659
>>> f.seek(-4,2)
657

>>> f.close()
```

???文本方式不支持 from 取值为 1：
```python
>>> f = open('1.txt','r')
>>> f.seek(2, 1)
io.UnsupportedOperation: cant do nonzero cur-relative seeks
```

定位到文件开头
```python
>>> f.seek(0, 0)
0
```

定位到文件末尾
```python
>>> f.seek(0, 2)
37
```

## os.rename(cur_name, new_name) and os.remove(file_name)

rename, remove 函数位于 os 模块中，需导入 os 模块

```python
>>> import os
>>> os.rename('1.txt','2.txt')
>>> os.remove('2.txt')
```

## File Object Methods

file.close()	Close the file. You need to reopen it for further access.
file.flush()	Flush the internal buffer. It’s same as the <stdio>’s <fflush()> function.
file.fileno()	Returns an integer file descriptor.
file.isatty()	It returns true if file has a <tty> attached to it.
file.next()     Returns the next line from the last offset.
file.read(size)	Reads the given no. of bytes. It may read less if EOF is hit.
file.readline(size)	It’ll read an entire line (trailing with a new line char) from the file.
file.readlines(size_hint)	It calls the <readline()> to read until EOF. It returns a list of lines read from the file. If you pass <size_hint>, then it reads lines equalling the <size_hint> bytes.
file.seek(offset[, from])	Sets the file’s current position.
file.tell()	Returns the file’s current position.
file.truncate(size)	Truncates the file’s size. If the optional size argument is present, the file is truncated to (at most) that size.
file.write(string)	It writes a string to the file. And it doesn’t return any value.
file.writelines(sequence)	Writes a sequence of strings to the file. The sequence is possibly an iterable object producing strings, typically a list of strings.


