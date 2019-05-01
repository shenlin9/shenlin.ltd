---
title: Python - Comment and Docstring
categories:
- Python
tags:
- Python
- Python Docstring
date: 2019-04-30 13:41:45
---

"Software spends only 10% time of its life in development and rest of 90% in maintenance"

<!--more-->

## Comment

最好在创建或更新程序的同时就添加注释，否则你可能会记不清代码的上下文环境，且以后
添加的注释可能不会很有效。

注释可以：
* 使给定的代码不言自明且增强可读性。
* 帮助其他开发人员在同一个项目上工作。
* 让测试人员参考以明确的进行白盒测试。

注释以 `#` 开头，以 EOL (end of the line) 结尾，且 `#` 后最好跟一个空格
```python
# To Learn any language you must follow the below rules.
# 1. Know the basic syntax, data types, control structures and conditional statements.
# 2. Learn error handling and file I/O.
# 3. Read about advanced data structures.
# 4. Write functions and follow OOPs concepts.

def main():
    print("Let's start to learn Python.")
...
```

注释的缩进要保持和其注释的代码同等缩进：
```python
# Define a list of months
months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul','Aug','Sep','Oct','Nov','Dec']

# Function to print the calender months
def showCalender(months):
    # For loop that traverses the list and prints the name of each month
    for month in months:
        print(month)

showCalender(months)
```

## Docstring

Python 的文档字符串为程序员提供了一种简单的方法，可以为每个Python模块，函数，类
和方法添加快速注释。

可以通过添加字符串常量来定义文档字符串。 它必须是对象（模块，函数，类和方法）定
义中的第一个语句。

文档字符串的使用范围比注释大得多，因此它应该描述函数作什么，而不是怎么做。

### 定义

使用三个引号来定义文档字符串，紧跟着放置在函数或类定义之后，或一个模块的顶部：
```python
def my_func():
    '''
Here is the function document strings,
They could spread to multiple lines,
But they are still regular strings,
That's means they are executable statements,
If they are not labeled,
They will be garbage collected as soon as the code executes.
    '''
    print("hello")

def your_func():
    """
Here is the function document string too
wa hahaha
wa hahaha
    """
    print("world")
```

注意上面奇怪的格式，引号缩进了，但其间的字符串内容并没有缩进。下面会讲到原因。

## 文档字符串和注释的区别

以三个引号开头的文档字符串，其还是普通的 Python 字符串（只是可以换行），也就是说
它们还是可执行语句，如果它们没有被引用，没有被标签，则代码执行完毕就会被垃圾回收
器回收。

也因此三个引号必须缩进，因为它们实际上仍然是函数下的代码块：
```python
def your_func():
"""
Here is the function document string too
wa hahaha
wa hahaha
"""
    print("world")

C:\Users\T460P>py test.py
  File "test.py", line 31
    """
      ^
IndentationError: expected an indented block
```
但引号内的字符则缩进不缩进都可以，正如前面的例子所示。


Python 解释器会忽略注释，但不会忽略文档字符串：以三个引号开头，紧跟函数、类定义
之后，或在模块的顶部的字符串，会被转换为文档字符串，可以通过变量 `obj.__doc__`
访问到：
```python
def your_func():
"""
Here is the function document string too
wa hahaha
"""
    print("world")

print(your_func.__doc__)
```

