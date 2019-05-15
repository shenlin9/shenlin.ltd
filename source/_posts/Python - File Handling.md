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
file_object = open(file_name [, access_mode][, buffering])
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

其取值为下面左列三个值和右列三个值的任意搭配：

    r       b
    w       +
    a       b+

    r 只读模式，且以文本形式从文件读取数据       b  以字节形式从文件读取数据
    w       +
    a 追加模式，      b+

搭配和含义如下：

    r	It opens a file in read-only mode while the file offset stays at the root.
    rb	It opens a file in (binary + read-only) modes. And the offset remains at the root level.
    r+	It opens the file in both (read + write) modes while the file offset is again at the root level.
    rb+	It opens the file in (read + write + binary) modes. The file offset is again at the root level.

    w	It allows write-level access to a file. If the file already exists, then it’ll get overwritten. It’ll create a new file if the same doesn’t exist
    wb	Use it to open a file for writing in binary format. Same behavior as for write-only mode.
    w+	It opens a file in both (read + write) modes. Same behavior as for write-only mode.
    wb+	It opens a file in (read + write + binary) modes. Same behavior as for write-only mode.

    a	It opens the file in append mode. The offset goes to the end of the file. If the file doesn’t exist, then it gets created.
    ab	It opens a file in (append + binary) modes. Same behavior as for append mode.
    a+	It opens a file in (append + read) modes. Same behavior as for append mode.
    ab+	It opens a file in (append + read + binary) modes. Same behavior as for append mode.


