---
title: Python - Virtual Environment, Package and Distribution
categories:
- Python
tags:
- Python
- 
date: 2019-08-29 13:45:04
---

Python - Virtual Environment, Package and Distribution

<!--more-->


安装虚拟环境：
```python
C:\Users\T460P>pip3 install virtualenv
Collecting virtualenv
  Using cached https://files.pythonhosted.org/packages/f7/69/1ad2d17560c4fc60170056dcd0a568b83f3453a2ac91155af746bcdb9a07/virtualenv-16.7.4-py2.py3-none-any.whl
Installing collected packages: virtualenv
Successfully installed virtualenv-16.7.4
```

创建一个名为 pythondsp 的虚拟环境：
```python
C:\Users\T460P>virtualenv pythondsp -p py
Running virtualenv with interpreter C:\WINDOWS\py.exe
Already using interpreter C:\Python27\python.exe
New python executable in C:\Users\T460P\pythondsp\Scripts\python.exe
Installing setuptools, pip, wheel...
done.

C:\Users\T460P> virtualenv pythondsp -p c:\windows\py.exe
Running virtualenv with interpreter c:\windows\py.exe
Already using interpreter C:\Python27\python.exe
New python executable in C:\Users\T460P\pythondsp\Scripts\python.exe
Installing setuptools, pip, wheel...
done.
```

## Debug

### 使用 Python 解释器的 `-i` 参数

debug.py
```python
import sys
import pdb

if len(sys.argv) < 3:
    raise SystemExit('Not Enough Parameters')

print(sys.argv)

def add_num(n1,n2):
    return n1 + n2
```

可以在执行完文件后自动进入 Python Shell，方便查看多个变量值：
```python
C:\Users\T460P\python_test>py -i debug.py 123 abc
['debug.py', '123', 'abc']
>>> sys.argv
['debug.py', '123', 'abc']
```

进入 shell 后可以直接调用文件中的函数：
```python
C:\Users\T460P\python_test>py -i debug.py 1 2 3
['debug.py', '1', '2', '3']
>>> add_num(1,2)
3
>>> add_num(3,2)
5
```

### 使用 Python 调试器模块 pdb

```python
import pdb
pdb.set_trace()
```

测试文件 debug.py
```python
import sys
import pdb

pdb.set_trace()

if len(sys.argv) < 3:
    print('Not Enough Parameters')
    raise SystemExit('Not Enough Parameters')

print(sys.argv)
```

输入 s 键逐步执行：
```python
C:\Users\T460P\python_test>py debug.py 123 abc
> c:\users\t460p\python_test\debug.py(6)<module>()
-> if len(sys.argv) < 3:
(Pdb) s
> c:\users\t460p\python_test\debug.py(10)<module>()
-> print(sys.argv)
(Pdb)
['debug.py', '123', 'abc']
--Return--
> c:\users\t460p\python_test\debug.py(10)<module>()->None
-> print(sys.argv)
(Pdb) s
--Call--
Exception ignored in: <async_generator object _ag at 0x00000195DDB9B2F0>
Traceback (most recent call last):
  File "C:\Program Files\Python37\lib\types.py", line 27, in _ag
  File "C:\Program Files\Python37\lib\bdb.py", line 90, in trace_dispatch
  File "C:\Program Files\Python37\lib\bdb.py", line 134, in dispatch_call
  File "C:\Program Files\Python37\lib\pdb.py", line 251, in user_call
  File "C:\Program Files\Python37\lib\pdb.py", line 343, in interaction
AttributeError: 'NoneType' object has no attribute '_previous_sigint_handler'
```

### 下划线操作符

Python Shell 中的下划线保存了最后一次计算操作的结果：
```python
>>> 3
3
>>> _
3
>>> 3 + 2
5
>>> _
5
>>> print(9)
9
>>> _
5
>>> print('abc')
abc
>>> _
5
```

## CSV

```python
>>> with open('c:\\users\\t460p\\1.csv') as f:
	rows = csv.reader(f)
	header = next(rows)
	print(header)
    print('---------------')
	for row in rows:
		print(row)

['author', 'country', 'publish date', 'price']
---------------
['shen', 'china', '2019-01-10', '100']
['lee', 'usa', '2017-12-03', '30']
```

## 函数

### docstring

三个引号之间的字符串，若没有被赋值给变量，则被视为文档字符串 docstring，可以被
help 函数调用：
```python
>>> def add2num(n1,n2):
	'''add two numbers
	paramerters:n1, n2
	'''
	return n1 + n2

>>> help(add2num)
Help on function add2num in module __main__:

add2num(n1, n2)
    add two numbers
    paramerters:n1, n2
```

???There are two standard format of creating the docstrings, i.e. Numpy style
and Goole style, which are supported by Sphinx-documentation for generating the
auto-documentation of the project.

## glob 模块

返回未排序的列表对象
```python
>>> import glob
>>> glob.glob('*.txt')
['LICENSE.txt', 'NEWS.txt']
>>> glob.glob('*.csv')
[]
>>> glob.glob('news.*')
['NEWS.txt']
```

## 数据类型和对象

Python 中的一切都是对象，也就是内置数据类型如整型、字符串、列表、集合和字典都是
对象。用户还可以使用类来创建自己的对象。

### type

每个对象都有一个类型，注意类的类型是 type，对象的类型是所属的类：
```python
>>> type(123)
<class 'int'>
>>> type('abc')
<class 'str'>
>>> type(['a',2, 3.3])
<class 'list'>

>>> type(int)
<class 'type'>
>>> type(str)
<class 'type'>

>>> class Dogs:
	pass

>>> d = Dogs()
>>> type(d)
<class '__main__.Dogs'>
>>> type(Dogs)
<class 'type'>
>>> type(d) is Dogs
True
```

### identity

每个对象都有一个整数形式的唯一标识，这个唯一标识是对象的唯一内存地址：
```python
>>> id(123)
140718206743184
>>> id('abc')
2058620557440
>>> id(int)
140718206251488
>>> id(str)
140718206263376
>>> id(Dogs)
2058654809544
>>> d  = Dogs()
>>> id(d)
2058650910112
```

### is

`is` 操作符用来判断两个对象是否指向的是同一个唯一标识，即是否同一个内存地址：
```python
>>> s = [1,2,3,4]

>>> s is list
False

>>> s1 = s
>>> s1 is s
True
>>> s is s1
True

>>> s.append(5)
>>> s1 is s
True
>>> s is s1
True
>>> s
[1, 2, 3, 4, 5]
>>> s1
[1, 2, 3, 4, 5]
```

### 比较对象

可以比较对象的唯一标识、类型、值：
```python
>>> def compare_obj(a, b):
	if a is b:
		print('objects have same identity id')
	if a == b:
		print('values of objects are same')
	if type(a) is type(b):
		print('types of objects are same')

>>> compare_obj(2, 3)
types of objects are same

>>> compare_obj(2, '2')

>>> compare_obj(2, 2)
objects have same identity id
values of objects are same
types of objects are same

>>> type(2)
<class 'int'>
>>> type(2) is type(3)
True
>>> type(2) == type(3)
True
```

### isinstance

Note: The type of an object is itself an object, which is known as object’s
class. This object (i.e. object’s class) is unique for a given type, hence can
be compared using ‘is’ opeartor as shown below
```python
>>> 2 is int
False
>>> type(2) is int
True
>>> type(2)
<class 'int'>

>>> type([1,2,3]) is list
True
```

上面的操作还可使用 `isinstance(object, type)` 来执行：
```python
>>> isinstance([1,2,3], list)
True
```

## 引用计数和垃圾回收

```python
>>> import sys
>>> r = 1
>>> sys.getrefcount(r)
869
>>> o = r
>>> sys.getrefcount(r)
870
>>> s = [1, r, 2]
>>> s
[1, 1, 2]
>>> sys.getrefcount(r)
872
```

del 命令可以减少引用计数，当减少到 0 时对象就被垃圾回收：
```python
>>> s.pop()
2
>>> sys.getrefcount(r)
872
>>> s.pop()
1
>>> sys.getrefcount(r)
872
>>> s.pop()
1
>>> sys.getrefcount(r)
871

>>> del o
>>> sys.getrefcount(r)
869
```

getrefcount 返回的引用计数会比我们的期望值高一些，因为此引用计数还包括了对象被作
为参数传递给 getrefcount 方法时的临时引用：
```python
>>> sys.getrefcount(1)
869
>>> sys.getrefcount(2)
371
>>> sys.getrefcount(3)
152
>>> sys.getrefcount(4)
212
>>> sys.getrefcount(999)
2
>>> sys.getrefcount(999999999)
2
```

### 浅复制和深复制

对于可变对象，如列表、字典，有两种类型的引用：浅复制 shallow copy 和深层复制
deep copy

在浅复制中，内部列表或内部字典的数据是共享的：
```python
>>> a = [1, 2, [3, 4], 5, 6]
>>> b = a
>>> c = list(a)

>>> b is a
True
>>> c is a
False

>>> print(id(a), id(b), id(c), sep = '\n')
1741659795208
1741659795208
1741662334280

>>> print(a,b,c, sep = '\n')
[1, 2, [3, 4], 5, 6]
[1, 2, [3, 4], 5, 6]
[1, 2, [3, 4], 5, 6]

>>> a.append(7)
>>> print(a,b,c, sep = '\n')
[1, 2, [3, 4], 5, 6, 7]
[1, 2, [3, 4], 5, 6, 7]
[1, 2, [3, 4], 5, 6]

>>> a[2].append(8)
>>> print(a,b,c, sep = '\n')
[1, 2, [3, 4, 8], 5, 6, 7]
[1, 2, [3, 4, 8], 5, 6, 7]
[1, 2, [3, 4, 8], 5, 6]
```

深层复制会创建一个新对象并递归的复制它包含的所有对象：
```python
>>> import copy
>>> d = [1, 2, [3, 4], 5, 6]
>>> e = copy.deepcopy(d)

>>> e is d
False

>>> print(d, e, sep = '\n')
[1, 2, [3, 4], 5, 6]
[1, 2, [3, 4], 5, 6]

>>> d[2].append(7)
>>> print(d, e, sep = '\n')
[1, 2, [3, 4, 7], 5, 6]
[1, 2, [3, 4], 5, 6]
```

## First Class Object

```python
>>> s = len
>>> s('abc')
3
>>> s([1,2])
2

>>> s = int
>>> s('3')
3
```

## first

```python

```
