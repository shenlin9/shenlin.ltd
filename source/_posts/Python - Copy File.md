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

文件内容复制：
```python
shutil.copyfile(source_file, destination_file)
```

此方法把源文件的内容复制到目标文件中：
* 它的工作原理是打开源文件进行读取，同时忽略其文件类型
* 它处理特殊文件也没有任何不同，也不会创建它们的副本
* 仅在目标可写的情况下将源的内容复制到目标，如果目标文件不可写，例如没有正确的权限，那么将抛出 `IOError` 异常
* 如果指定的源文件和目标文件是同一个文件，则返回 `SameFileError` 错误
* 目标文件的内容会被覆盖掉
* 它不支持复制文件，例如字符设备、块设备和管道设备等
* 如果目标是个目录，则会发生错误 13，就是说此方法不会把文件复制到文件夹内

`copyfile()` 方法调用了底层函数 `copyfileobj()`，它接受文件名作为参数，根据文件
名打开文件并将文件句柄传递给 `copyfileobj()`，该方法中有一个可选的第三个参数，可
以用来指定缓冲区长度，然后打开文件，读取指定缓冲区大小的块。此方法的默认行为是一
次性读取整个文件。


## shutil copy() method

```python
shutil.copy(source_file, destination_file|destinartion_dir)
```
`copy()` 方法的功能类似于 Unix 中的 `cp` 命令：如果目标是一个文件夹，它将在其中
创建一个与源文件同名(basename)的新文件。此外，在复制源文件的内容之后，该方法将同
步目标文件的权限。如果您正在复制相同的文件，它也会抛出 `SameFileError` 错误。

`copy()` 和 `copyfile()` 的区别：
* copy() 复制内容的同时还会设置权限位，而 copyfile() 只复制数据。
* 如果目标是一个目录，copy()将复制一个文件，而copyfile()将失败返回错误13。
* 有趣的是，copyfile() 方法在其实现中使用了 copyfileobj() 方法，而 copy() 方法在其实现中依次使用了 copyfile() 和 copymode() 函数。
* 从第3点可以很明显看出，copyfile()要比copy()快一些，因为后者多了一个额外的复制权限的任务。

## shutil copyfileobj() method

```python
copyfileobj()
```
此方法将文件复制到目标路径或文件对象。
如果目标是文件对象，那么需要在调用 `copyfileobj()` 之后显式地关闭它。
它假设一个可选参数(缓冲区大小)，您可以使用该参数来设置缓冲区长度。
它是在复制过程中保存在内存中的字节数。系统使用的默认大小是 16KB。

???
```python
from shutil import copyfileobj
status = False
if isinstance(target, string_types):
    target = open(target, 'wb')
    status = True
try:
    copyfileobj(self.stream, target, buffer_size)
finally:
    if status:
        target.close()
```

## shutil copy2() method

`copy2()` 方法类似于 `copy()` 方法，复制同一个文件会导致 SameFileError 错误，但
在赋值时还会额外的把文件的访问时间和修改时间添加到了元数据中，：
```python
copy2()
```

```python
from shutil import *
import os 
import time
from os.path import basename

def displayFileStats(filename):
    file_stats = os.stat(basename(filename))
    print('\tMode    :', file_stats.st_mode)
    print('\tCreated :', time.ctime(file_stats.st_ctime))
    print('\tAccessed:', time.ctime(file_stats.st_atime))
    print('\tModified:', time.ctime(file_stats.st_mtime))

os.mkdir('test')

print('SOURCE:')
displayFileStats(__file__)

copy2(__file__, 'testfile')

print('TARGET:')
displayFileStats(os.path.realpath(os.getcwd() + './test/testfile'))
```

copy() 和 copy2() 区别：
* copy() 只设置权限位，而 copy2() 还使用时间戳更新文件的元数据
* copy() 方法内部调用了 copyfile() 和 copymode()，而 copy2() 方法内部调用了
  copyfile() 和 copystat()

### Copymode() Vs Copystat()

```python
copymode(source, target, *, follow_symlinks=True)
```
* 它打算将权限位从源文件复制到目标文件，传递的参数是字符串。
* 文件内容、所有者和组保持不变。
* 如果 follow_symlinks 参数为 false，前两个参数为符号链接，那么 copymode() 将尝
  试更新目标链接，而不是它所指向的实际文件。

```python
copystat(source, target, *, follow_symlinks=True)
```
* 它试图保存权限位、最后使用的时间/更新时间和目标文件的标志。
* copystat() 在 Linux 上复制时包含扩展属性。文件内容/所有者/组保持不变。
* 如果 follow_symlinks 参数是 False，并且前两个参数是符号链接，那么 `copystat()`
  将更新它们，而不是它们指向的文件。

## os popen method

```python
os.popen(command[, mode[, bufsize]])
```
此方法根据打开模式 mode 的设置(默认 'r')，创建一个到 command 的管道或创建一个来
自 command 的管道，并返回一个连接到此管道的文件对象：
* mode 只能为 r 或 w，默认为 r
* bufsize 值为 0，则没有缓冲区；值为 1，则按行缓冲；值大于 1，则表示这个值就是缓
  冲区大小；值为负数，则采用默认缓冲区大小(???)。

For Windows OS.
```python
import os
os.popen('copy 1.txt.py 2.txt.py')
```

For Linux OS.
```python
import os
os.popen('cp 1.txt.py 2.txt.py')
```

## os system() method

```python
system()
```
system() 方法允许执行操作系统的命令或 subshell 中的脚本，只需要把要执行的命令或
脚本作为参数传递给 system()，此方法调用了标准 c 库中的函数，它的返回值是命令的退
出状态。

For Windows OS.
```python
import os
os.system('copy 1.txt.py 2.txt.py')
```

For Linux OS.
```python
import os
os.system('cp 1.txt.py 2.txt.py')
```

## threading Thread() method

```python
thread()
```
如果希望异步复制文件，可以使用 Python 的线程模块在后台运行复制操作。

使用此方法时，请确保使用锁定以避免死锁。在应用程序使用多个线程读取/写入文件时，
可能会遇到这种情况。

```python
import shutil
from threading import Thread

src="1.txt.py"
dst="3.txt.py"

Thread(target=shutil.copy, args=[src, dst]).start()
```

## subprocess call() method

```python
call()
```
subprocess 模块提供了一个简单的接口来处理子进程。它使我们能够启动子进程，附加到
它们的输入/输出/错误管道，并检索返回值。

subprocess 模块的目的是取代传统的模块和功能，如 `os.system, os.spawn*,
os.popen*, popen2.*`

它公开一个call()方法来调用系统命令来执行用户任务：
```python
import subprocess

src="1.txt.py"
dst="2.txt.py"
cmd='copy "%s" "%s"' % (src, dst)

status = subprocess.call(cmd, shell=True)

 if status != 0:
     if status < 0:
         print("Killed by signal", status)
     else:
         print("Command failed with return code - ", status)
else:
     print('Execution of %s passed!\n' % cmd)
```

## subprocess check_output() method

```python
check_output()
```

使用 subprocess 模块的 check_out() 方法，可以运行外部命令或程序并捕获它的输出，
它也支持管道：
```python
import os, subprocess

src=os.path.realpath(os.getcwd() + "./1.txt.py")
dst=os.path.realpath(os.getcwd() + "./2.txt.py")
cmd='copy "%s" "%s"' % (src, dst)

status = subprocess.check_output(['copy', src, dst], shell=True)

print("status: ", status.decode('utf-8'))
```


