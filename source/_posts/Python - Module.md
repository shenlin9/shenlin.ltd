---
title: Python - Module
categories:
- Python
tags:
- Python
- Module
date: 2019-05-22 16:37:36
---

Python - Module

<!--more-->

## Module

模块是 Python 模块化编程的支柱。

Python 模块带来了一些好处，比如我们可以减少代码中的冗余，可以让我们保持编码风格
的一致性。节省了时间，提高了可读性和生产力。它们还用于扩展 Python 的功能，并允许
世界各地的不同开发人员以协调的方式工作。

模块主要是文件(后缀 `.py`)，其中包含 Python 代码，这些代码定义了函数、类、变量等。

它们可以在一个文件中包含不同的函数、变量和类。我们也可以称它们为库。

Python 标准库：

    OS
    Time
    Math
    MatPlotlib
    ...
    ...

网上资源：

    Tensorflow  用于深度学习
    Keras       用于深度学习
    Numpy       用于数字操作
    Pandas      用于数组操作

## Python Module: Mechanism

对于预先安装 Python 的系统，或者使用 apt-get、dnf、zypper 等系统包管理器安装的
Python，或者使用 Anaconda 等包环境管理器安装 Python 的系统，可以采用以下方法。

当我们导入模块时，python 解释器依次从三个位置定位模块:
* 执行程序的目录(The directory from the program is getting executed)
* PYTHONPATH 变量(一个 shell 变量或环境变量)指定的目录
* 默认目录(取决于OS发行版)。

上述机制流程如下:

![import module](https://cdn.techbeamers.com/wp-content/uploads/2019/01/Python-Module-Flowchart.png "Python-Module-Flowchart")

## Modules Listing

要找出 python 中存在的模块列表，可以在 python 解释器 shell 中执行命令
`help("modules")`，将返回可用模块列表:
```python
异常退出???
```

如果想找到已安装模块的列表，可以在命令行中使用: `pip list` 或 `conda list`，具体
取决于安装方法:
```python
C:\Users\T460P>pip3 list
Package      Version
------------ --------
certifi      2019.3.9
chardet      3.0.4
Click        7.0
Flask        1.0.2
Flask-Cors   3.0.7
idna         2.8
itsdangerous 1.1.0
Jinja2       2.10.1
MarkupSafe   1.1.1
Pillow       6.0.0
pip          19.1
PySocks      1.6.8
PyYAML       5.1
requests     2.21.0
setuptools   40.6.2
six          1.12.0
urllib3      1.24.2
Werkzeug     0.15.2
WARNING: You are using pip version 19.1, however version 19.1.1 is available.
You should consider upgrading via the 'python -m pip install --upgrade pip' command.
```

## Import Modules

使用全名：
```python
import os
```

使用别名：
```python
import os as o
```

### 循环依赖 circular dependency

要避免递归 import 从而导致循环依赖，如模块 A import 模块 B，而模块 B 又 import
了模块 A，则会导致错误：
```python
import A
import B
```

在 Python 中，import 也是一个可执行语句。
每个 import 子句都将导致相应模块的执行。
此外，在相关代码（在 def 或类中）执行之前，嵌入在模块中的任何函数或类都不会生效。

递归导入模块可能会导致程序中的循环依赖。

A.py
```python
insert into t('') values()
```

B.py
```python
insert into s() values()('')
```

## Install New Modules

PIP
```python
python -m pip3 install module_package_name
```

Anaconda
```python
conda install module_package_name
```

System Package Manager 
```python
sudo apt install module_package_name
```

## User-Defined Modules

factorial_definition.py
```python
def factorial():
    out = 1
    if num < 0:
        print("Sorry, factorial does not exist for negative numbers")
    elif num == 0:
        print("The factorial of 0 is 1")
    else:
        for i in range(1, num + 1):
            out = out*i
    return out

# For testing purpose:
# num = 5
# print("The factorial of",num,"is",factorial())

Pi = 3.14
```
将此文件保存在 PYTHONPATH 中，或者保存在将导入模块的另一个程序所在的路径中。

要导入这个文件，我们在程序中使用下面的代码，它将加载模块。
```python
import factorial_definition
factorial_definition.factorial()
```
可以用 `factorial_definition.Pi` 调用变量 Pi

由于模块名很长，可以将其重命名为 `import factorial_definition as fact`，并使用它
来调用变量和变量。

如果只想要导入 Pi 变量，可以使用`from factorial_definition import Pi`。
