---
title: Python - Head First Python
categories:
- Python
tags:
- Python
date: 2019-08-05 09:19:02
---

Python - Head First Python

<!--more-->

## BIF

BIF Built-In Functions

查看内置方法列表
```
>>> dir(__builtins__)
['ArithmeticError', 'AssertionError', 'AttributeError', 'BaseException',
'BlockingIOError', 'BrokenPipeError', 'BufferError', 'BytesWarning',
'ChildProcessError', 'ConnectionAbortedError', 'ConnectionError',
'ConnectionRefusedError', 'ConnectionResetError', 'DeprecationWarning',
'EOFError', 'Ellipsis', 'EnvironmentError', 'Exception', 'False',
'FileExistsError', 'FileNotFoundError', 'FloatingPointError', 'FutureWarning',
'GeneratorExit', 'IOError', 'ImportError', 'ImportWarning', 'IndentationError',
'IndexError', 'InterruptedError', 'IsADirectoryError', 'KeyError',
'KeyboardInterrupt', 'LookupError', 'MemoryError', 'ModuleNotFoundError',
'NameError', 'None', 'NotADirectoryError', 'NotImplemented',
'NotImplementedError', 'OSError', 'OverflowError', 'PendingDeprecationWarning',
'PermissionError', 'ProcessLookupError', 'RecursionError', 'ReferenceError',
'ResourceWarning', 'RuntimeError', 'RuntimeWarning', 'StopAsyncIteration',
'StopIteration', 'SyntaxError', 'SyntaxWarning', 'SystemError', 'SystemExit',
'TabError', 'TimeoutError', 'True', 'TypeError', 'UnboundLocalError',
'UnicodeDecodeError', 'UnicodeEncodeError', 'UnicodeError',
'UnicodeTranslateError', 'UnicodeWarning', 'UserWarning', 'ValueError',
'Warning', 'WindowsError', 'ZeroDivisionError', '__build_class__', '__debug__',
'__doc__', '__import__', '__loader__', '__name__', '__package__', '__spec__',
'abs', 'all', 'any', 'ascii', 'bin', 'bool', 'breakpoint', 'bytearray', 'bytes',
'callable', 'chr', 'classmethod', 'compile', 'complex', 'copyright', 'credits',
'delattr', 'dict', 'dir', 'divmod', 'enumerate', 'eval', 'exec', 'exit',
'filter', 'float', 'format', 'frozenset', 'getattr', 'globals', 'hasattr',
'hash', 'help', 'hex', 'id', 'input', 'int', 'isinstance', 'issubclass', 'iter',
'len', 'license', 'list', 'locals', 'map', 'max', 'memoryview', 'min', 'next',
'object', 'oct', 'open', 'ord', 'pow', 'print', 'property', 'quit', 'range',
'repr', 'reversed', 'round', 'set', 'setattr', 'slice', 'sorted',
'staticmethod', 'str', 'sum', 'super', 'tuple', 'type', 'vars', 'zip']
```

## locals()

返回当前作用域中所有定义的标识符集合：
```python
>>> locals()
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__': <class '_frozen_importlib.BuiltinImporter'>, '__spec__': None, '__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, 'os': <module 'os' from 'C:\\Program Files\\Python37\\lib\\os.py'>, 'f': <_io.TextIOWrapper name='1.txt' mode='w+' encoding='cp936'>, 'a': 'ABC', 'b': 'abc', 'x': 'ABC', 'y': 'abc'}
>>> 'x' in locals()
True
>>> 'f' in locals()
True
>>> 'file' in locals()
False
```

举例：
```python
try:
    f = open('may_not_exist_file')
    ....
except IOError:
    ....
finally:
    if f in locals():
        f.close()
```
finally 里的 f.close() 能确保无论是否发生错误都关闭文件，但若文件不存在，则 f 文
件对象创建失败，就不会有 f.close() 方法，因此需要判断一下 f 是否创建成功。

因为处理文件时 try...except...finally 模式相当常用，因此 Python 提供了一个 with
语句，可以省略 finally 语句，with 语句可以确保文件被关闭：
```python
try:
    with open('') as f:
        f.readline()
except IOError as err:
    print(str(err))
```
这里的两个 as 都是表示用此关键字把传入的文件对象、异常对象赋值给一个标识符。

with 语句使用了一种名为上下文管理协议(context management protocol)的 Python 技术
，两个 with 语句还可以合到一起：
```python
try:
	with open('1.txt') as f, open('2.txt') as g:
		f.readline()
		g.readline()
except:
	print('file error')

'abcdefg\n'
''
```
注意只有一个 with，中间使用逗号分隔

## help

通过 help 获取内置函数的帮助
```python
>>> help(min)
Help on built-in function min in module builtins:

min(...)
    min(iterable, *[, default=obj, key=func]) -> value
    min(arg1, arg2, *args, *[, key=func]) -> value

    With a single iterable argument, return its smallest item. The
    default keyword-only argument specifies an object to return if
    the provided iterable is empty.
    With two or more arguments, return the smallest argument.
```

通过 help 获取类方法的帮助，要指定类名：
```python
>>> help(split)
Traceback (most recent call last):
  File "<pyshell#16>", line 1, in <module>
    help(split)
NameError: name 'split' is not defined

>>> help(str.split)
Help on method_descriptor:

split(self, /, sep=None, maxsplit=-1)
    Return a list of the words in the string, using sep as the delimiter string.

    sep
      The delimiter according which to split the string.
      None (the default value) means split according to any whitespace,
      and discard empty strings from the result.
    maxsplit
      Maximum number of splits to do.
      -1 (the default value) means no limit.
```

通过 help 获取标准库帮助，要先 import 模块：
```python
>>> help(pickle.dump)
NameError: name 'pickle' is not defined
>>> import pickle
>>> help(pickle.dump)
Help on built-in function dump in module _pickle:

dump(obj, file, protocol=None, *, fix_imports=True)
```

## IDLE

IDLE (Integrated Development and Learning Environment)

Tab 自动完成
Alt + P 上一条语句
Alt + N 下一条语句

```
> python3 -V
Python 3.7.2
```

## 列表

Python 列表的设计目的是存储相关事务的数据，列表并不关心这些相关的数据的数据类型
是否一致，所以列表是允许存储混合数据类型的

Python 的标识符不需要声明类型信息，标识符只是个名字，可以指示某个类型的数据对象。
但 Python 的标识符必须被赋值后才可以使用，否则报错：NameError: name 'xxx' is not
defined。

Python 中创建一个列表数据对象时，会在内存中创建一个堆栈，自下而上的堆放数据，也
就是下标 0 在最底层，上面依次是下标 1，下标 2.....

列表方法：

    append('item')
    insert(index, 'item')
    extend(['new_item1','new_item2'])

    pop(), pop(index)
    remove('item')

### 列表推导

如要将列表中的每一项小写转换为大写，且保留原列表对象，看下面的代码
```python
>>> s = ['a', 'b', 'c', 'd', 'e']
>>> t = []
>>> for i in s:
	t.append(i.upper())
>>> t
['A', 'B', 'C', 'D', 'E']
```

进行的操作如下：
* 创建新列表存放转换后的数据
* 迭代原列表
* 在迭代到源列表的每一项时，对此项进行转换处理
* 将转换完成的数据追加到新的列表

类似上面的列表转换是一种很常见的操作，因此 Python 特别提供了一个工具，称为列表推
导(List Comprehension)，用于减少列表转换时的代码量：
```python
>>> t = [s.upper() for s in ['a', 'b', 'c', 'd', 'e']]
>>> t
['A', 'B', 'C', 'D', 'E']
```
解读：
```python
t =     [s.upper()      for s in        ['a', 'b', 'c', 'd', 'e']]
新列表  指定的转换          数据项      现有列表
```

当然无需保留原列表时，列表转换也可以直接修改原列表：
```python
>>> s = ['a', 'b', 'c', 'd', 'e']
>>> s = [i.upper() for i in s]
>>> s
['A', 'B', 'C', 'D', 'E']
```

列表推导的使用很自然，和你大脑的考虑数据的方式和想要应用的转换是一致的，但并不意
味着任何时候列表推导都是适用的，列表迭代能完成列表推导的所有功能，只是代码多一些
儿，在需要提供更多灵活性的地方，如要去除列表中的重复项，还是需要通过列表迭代实现
```python
>>> s = ['a', 'b', 'a', 'c', 'b']
>>> t = []
>>> for i in s:
        if i not in t:
            t.append(i)

>>> t
['a', 'b', 'c']
```

### 相关函数

reversed() 函数
```python
>>> for i in reversed(range(3)):
	i

2
1
0
```

enumerate() 函数
```python
>>> for i, v in enumerate(['a','b','c']):
	i, v

(0, 'a')
(1, 'b')
(2, 'c')
```

sorted() 函数
```python
>>> sorted([5, 2, 7, 1])
[1, 2, 5, 7]

>>> sorted([5, 2, 7, 1], reverse = True)
[7, 5, 2, 1]
```

## 集合

Python 集合最突出的特性是：无序，不允许重复项，向集合添加重复项会被忽略，所以上
面的去除重复项也可以使用集合完成:
```python
>>> s = ['a', 'b', 'a', 'c', 'b']
>>> t = set([i for i in s])
>>> t
{'b', 'a', 'c'}
```

## 字典 

选择的数据结构要与数据匹配，不同的数据结构对代码的复杂性带来很大差别。

字典是 Python 的内置数据结构，允许把数据和键关联，这样使得内存中的数据和实际数据
的结构保持一致。

字典的重点是维护关联关系，而不负责维护数据顺序。

字典在其他语言中的名字：映射、散列、关联数组

遍历字典：
```python
>>> d = {'a':1, 'b':2, 'c':3}

>>> for k in d:
	k

'a'
'b'
'c'

>>> for k, v in d:
	k,v

ValueError: not enough values to unpack (expected 2, got 1)

>>> for k, v in d.items():
	k,v

('a', 1)
('b', 2)
('c', 3)
```

通过列表创建字典：
```python
>>> zip(['a','b','c'], [1,2,3])
<zip object at 0x000002BC4D0738C8>

>>> dict(zip(['a','b','c'], [1,2,3]))
{'a': 1, 'b': 2, 'c': 3}

>>> dict(enumerate(['a','b','c']))
{0: 'a', 1: 'b', 2: 'c'}
```

## 函数

Python 3 默认递归深度不能超过 100

Python 代码块的英语术语：suite，也称为组

## 模块

把代码转化为模块，则可以在 Python 编程社区实现代码的共享

```python
>>> import sys
>>> sys.path
['', 'C:\\Program Files\\Python37\\Lib\\idlelib', 'C:\\Program
Files\\Python37\\python37.zip', 'C:\\Program Files\\Python37\\DLLs',
'C:\\Program Files\\Python37\\lib', 'C:\\Program Files\\Python37', 'C:\\Program
Files\\Python37\\lib\\site-packages']
```

### 创建模块

### 发布模块

### 导入模块

### 命名空间

### 注册 PyPI

### 上传代码到 PyPI

## 文件

```python
import os
os.getcwd()
os.chdir('new_path')
os.path.exists('path')

f = open('path_to_file')
f.readline()
f.seek(0)
```

多重赋值 Multiple Assignment：
```python
>>> s = 'a,b'
>>> x,y = s.split(',')
>>> x
'a'
>>> y
'b'
```

## 异常

```python
try:

except:
    pass
finally:

```

## print()

print(...)

    print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)
    
    Prints the values to a stream, or to sys.stdout by default.

    Optional keyword arguments:
    sep:   string inserted between values, default a space.
    end:   string appended after the last value, default a newline.
    file:  a file-like object (stream); defaults to the current sys.stdout.
    flush: whether to forcibly flush the stream.

关键字参数 sep，输出的多个值之间的分隔符，默认值为一个空格
```python
>>> print('aa','bb','cc')
aa bb cc
>>> print('aa','bb','cc', sep='--')
aa--bb--cc
```

关键字参数 end，输出的最后一个值之后添加的字符，默认值为换行符：
```python
>>> print('aa');print('bb');print('cc')
aa
bb
cc
>>> print('aa', end='--');print('bb', end='--');print('cc', end='')
aa--bb--cc
```

关键字参数 file，默认值为标准输出到显示器(python 中为 sys.stdout)，还可输出到文件：
```python
>>> f = open('1.txt', 'w')
>>> print('zzzz', file=f)
>>> f.close()
```
写入文件时，文件对象记得一定要关闭，确保所写的数据 flush 到磁盘。
特别注意 'w' 模式会清空文件内容，即打开文件时就清空了内容，不是在写的时候才清空
要追加内容使用 'a' 模式

## open()

open(file, mode='r', buffering=-1, encoding=None, errors=None, newline=None, closefd=True, opener=None)

    'x'       Create：create a new file and open it for writing
    'r'       Read：open for reading (default)
    'w'       Write：open for writing, truncating the file first
    'a'       Append：open for writing, appending to the end of the file if it exists

    '+'       open a disk file for updating (reading and writing)，不能单独使用
             ，必须搭配上面的某一个 create/read/write/append 模式

    'b'       binary mode
    't'       text mode (default)

    'U'       universal newline mode (deprecated)

```python
>>> f = open('1.txt','+')
ValueError: Must have exactly one of create/read/write/append mode and at most one plus
```

??? 'w+' 模式的意义? 既然 w 模式打开时就清空文件内容，还需要 + 模式读取什么?

## 字符串

Python 的变量只包含数据对象的一个引用，数据对象才真正包含数据，

Python 中的字符串不可变，因为不知道还有哪些变量指向同一个字符串，所以当要改变一
个变量指向的字符串时，会创建原字符串的副本，并把变量指向此副本然后修改此副本：
```python
>>> x = 'abc'
>>> y = x
>>> id(x)
1957257730176
>>> id(y)
1957257730176

>>> x = x.upper()
>>> x
'ABC'
>>> y
'abc'
>>> id(x)
1957295421176   # x 的唯一标识已经改变
>>> id(y)
1957257730176
```

Python 对字符串排序时，短横线排点号前面，点号排冒号前面，因此若涉及时间时格式要
统一：
```python
>>> print(sorted(['2:23','2.23','2-23']))
['2-23', '2.23', '2:23']

>>> print(sorted(['2:23','2.23','2-23'], reverse=True))
['2:23', '2.23', '2-23']
```

### Template

Template 类支持简单的字符串替换：
```python
>>> from string import Template
>>> Template(title = title_str)
```

## glob

glob 模块非常适用于处理文件名列表：
```python
>>> import glob
>>> files = glob.glob('*.txt')
>>> print(files)
['LICENSE.txt', 'NEWS.txt']
```

## pickle

Python 2 和 Python 3 的 Pickle 格式不兼容

Pickle 模块可以高效的把 Python 数据对象保存到磁盘以及从磁盘恢复。

Python 的 Pickle 模块使用了一种定制的二进制格式(这称为它的协议)，因此打开模式要
用 'wb' 或 'rb'：
```python
import pickle

with open('1.txt', 'wb') as f, open('2.txt', 'wb') as g:
    pickle.dump(['a','b','c'], f)
    pickle.dump([1,2,3,4], g)
    print('write over')

with open('1.txt', 'rb') as f, open('2.txt', 'rb') as g:
    print(pickle.load(f))
    print(pickle.load(g))
```

## 类

定义类：
```python
>>> class Athlete:
	def  __init__(self, words):
		print(words)
```

你写的代码和 Python 实际执行的代码：
```python
>>> a = Athlete('hello')
hello
>>> Athlete.__init__(a, 'hello')
hello

>>> b = Athlete('world')
world
>>> Athlete.__init__(b, 'world')
world
```

Python 支持多重继承
```python
class Athlete(Animal):
    def __init__(self):
        print('hello')
```

### 属性

使用修饰符 `@property` 定义**类属性**，在类方法上面加上此修饰符即可：
```python
class Athlete:
	def __init__(self):
		print('hello')

	def run(self):
		print('I am running')

	@property
	def jump(self):
		print('I am jumping')

>>> a = Athlete()
hello
>>> a.run()
I am running
>>> a.jump()                        # 调用时无需括号
I am jumping
Traceback (most recent call last):
  File "<pyshell#18>", line 1, in <module>
    a.jump()
TypeError: 'NoneType' object is not callable

>>> a.jump
I am jumping
```

## 杂项

### 工厂函数

用于创建某种类型的新的实例的函数，就称为工厂函数，类比于现实世界中生产产品的工厂
，Python 的工厂函数，如 set() 生成一个集合类型的实例，list() 生成一个列表类型实
例。

### 原地排序和复制排序

列表的 sort 方法可以进行原地排序，即转换后直接替换原列表对象：
```python
>>> s = [8, 3, 2, 7, 5]
>>> s.sort()
>>> s
[2, 3, 5, 7, 8]
```

BIF sorted() 可以进行复制排序，即转换后然后返回副本，不改变原对象：
```python
>>> s = [8, 3, 2, 7, 5]
>>> sorted(s)
[2, 3, 5, 7, 8]
>>> s
[8, 3, 2, 7, 5]
```

sort 和 sorted 均有关键字参数 reverse = True 倒序排序

## CGI

Common Gateway Interface 通用网关接口，允许 web 服务器运行一个服务器端程序。

CGI 跟踪模块 cgitb，启用此模块才会在浏览器显示详细的错误信息：
```python
import cgitb
cgitb.enable()
```

获取表单元素值：
```python
import cgi
form_data = cgi.FieldStorage()
v = form_data['which_name'].value
```

## http server

Python 提供了自己的 web 服务器，包含在 http.server 模块中，将下列代码保存为
httpd.py：
```python
#! /usr/local/bin/python3

from http.server import HTTPServer, CGIHTTPRequestHandler
port = 80
httpd = HTTPServer(('', port), CGIHTTPRequestHandler)
print('Starting http server on port' + httpd.server_port)
httpd.serve_forever()
```

在类 Unix 系统中，脚本必须先设置为可执行，web 服务器才能执行这些脚本：
```python
>>> chmod +x httpd.py
>>> python3 httpd.py
```

## 手机上的 Python

SL4A：Scripting Layer For Android，只支持 Python 2， Python 3 不兼容 Python 2

## JSON

JSON：Javascript Object Notation
Web 上使用最广泛的数据交换格式，基于JavaScript语言的轻量级的数据交换格式

```python
>>> ls = ['a', 'b', [1, 2, 3], 'd']
>>> js = json.dumps(ls)
>>> js
'["a", "b", [1, 2, 3], "d"]'
>>> json.loads(js)
['a', 'b', [1, 2, 3], 'd']
```
