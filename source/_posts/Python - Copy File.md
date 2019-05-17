---
title: Python - Copy File
categories:
- Python
tags:
- Python
- shutil
date: 2019-05-17 13:42:33
---

Python - Copy File

<!--more-->

Python 提供了许多开箱即用的模块(如os、subprocess 和 shutil)来支持文件 I/O 操作。

为什么了解 Python 中哪种复制文件方法最适合是如此重要？这是因为文件I/O操作是性能
密集型的，常常导致瓶颈。这就是为什么您应该根据应用程序的设计选择最好的方法。

一些使用共享资源的程序更喜欢以阻塞模式复制文件，而另一些程序可能希望异步地复制文
件。例如，使用线程复制文件或启动单独的进程来复制文件。需要考虑的另一点是平台的可
移植性。这意味着你应该知道目标操作系统(Windows/Linux/Mac OS X等)，无论你在哪里运
行程序。

## shutil

使用shutil模块，可以自动复制文件和文件夹。该模块经过优化设计。
当没有真正的处理需求时，它可以拯救你于打开、读取、写入和关闭文件等耗时的操作。
它包含了很多实用函数和方法，可以让您执行复制、移动或删除文件和文件夹等任务。

## shutil copyfile() method

此方法仅在目标是可写的情况下将源的内容复制到目标
如果您没有正确的权限，那么将引发 IOError
它的工作原理是打开源文件进行读取，同时忽略其文件类型
其次，它处理特殊文件也没有任何不同，也不会创建它们的副本

`copyfile()` 方法调用了底层函数 `copyfileobj()`，它接受文件名作为参数，根据文件
名打开文件并将文件句柄传递给 `copyfileobj()`，该方法中有一个可选的第三个参数，可
以用来指定缓冲区长度，然后打开文件，读取指定缓冲区大小的块。此方法的默认行为是一
次性读取整个文件

```python
shutil.copyfile(source_file, destination_file)
```

它将源文件的内容复制到目标文件中
如果目标文件不可写，则复制操作将抛出 `IOError` 异常
如果源文件和目标文件都相同，则返回 `SameFileError` 错误
However, if the destination pre-exists with a different name, then the copy will
overwrite its content
但是，如果目标预先存在一个不同的名称，那么副本将覆盖其内容
如果目标是个目录，则会发生错误 13，就是说此方法不会把文件复制到文件夹内
It doesn’t support copying files such as character or block devices and the
pipes
它不支持复制字符设备、块设备和管道文件


## shutil copy() method
## shutil copyfileobj() method
## shutil copy2() method
## os popen method
## os system() method
## threading Thread() method
## subprocess call() method
## subprocess check_output() method
