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
注意上面的 list 函数，用于把可迭代对象转换为列表对象，如果所给的参数已经是列表对
象，则通过对列表对象进行一个浅拷贝来创建一个新的列表对象。

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

All objects in python are said to be ‘first class’ i.e. all objects that can
be named by an identifier have equal status：
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

可以利用此特性编写紧凑灵活的代码，例如，通过列表推导的方式批量更改列表元素的数据
类型：
```python
>>> ls_types = [str, int, float]
>>> stock = 'goog, 3, 12.3'
>>> ls_stock = stock.split(',')
>>> ls_stock
['goog', ' 3', ' 12.3']

>>> ls = [ty(val) for ty, val in zip(ls_types, ls_stock)]
>>> ls
['goog', 3, 12.3]
```

## Builtin Datatypes

    Category    Type name       Description
    --------------------------------------------
    None        NoneType        x = None

    Numbers     int             integer
                float           floating point
                complex         complex number
                boolean         True or False

    Sequences   str             character string
                list            list
                tuple           tuple

    Mapping     dict            Dictionary

    Sets        set             Mutable set
                frozenset       Immutable set

### None

```python
>>> x = None
>>> type(x)
<class 'NoneType'>
```

None 常用作函数定义时关键字参数的默认值：
```python
>>> def addNum(a = None, b = None):
	print(a + b)
	return None

>>> s = addNum(2, 3)
5
>>> type(s)
<class 'NoneType'>
```

None 被认为是 False：
```python
>>> if None:
	print('None is considered as True')
else:
	print('None is considered as False')

None is considered as False
```

isinstance 函数不能和 None 一起使用：
```python
>>> n = 1
>>> isinstance(n, None)
TypeError: isinstance() arg 2 must be a type or tuple of types

>>> isinstance('a', str)
True
>>> isinstance('a', (int,bool))
False
>>> isinstance('a', (int,bool,str))
True
```

### Numbers

复数表示为由两个浮点数组成的对：
```python
>>> c = 2 + 3j
>>> c.real
2.0
>>> c.imag
3.0
>>> c.conjugate()
(2-3j)
```

### Sequences

序列是可以通过非负整数索引的集合对象，Python 有三种序列类型：string, tuple, list
，string 和 tuple 是不可变类型，list 是可变类型。

针对所有序列都可用的常用操作：
```python
>>> s = [1, 2, 3, 4, 5]

>>> s[0:3:2]
[1, 3]

>>> len(s)
5
>>> min(s)
1
>>> max(s)
5

>>> sum(s)
15
>>> sum(s, 1)
16

>>> all(item > 0 for item in s)
True
>>> all(item > 4 for item in s)
False
>>> any(item < 0 for item in s)
False
>>> any(item > 4 for item in s)
True


>>> s = 'abcde'
>>> s[0:3:2]
'ac'
>>> len(s)
5
>>> min(s)
'a'
>>> max(s)
'e'
>>> sum(s)
TypeError: unsupported operand type(s) for +: 'int' and 'str'
>>> all(item < 'f' for item in s)
True
>>> any(item > 'f' for item in s)
False
```

只针对可变序列可用的常用操作：赋值和删除
```python
>>> s = [1,2,3,4,5]
>>> s[0] = 9
>>> s[0:3:2]
[9, 3]
>>> s
[9, 2, 3, 4, 5]
>>> s[0:3:2] = [8, 7]
>>> s
[8, 2, 7, 4, 5]
>>> del s[4]
>>> s
[8, 2, 7, 4]
>>> del s[0:3:2]
>>> s
[2, 4]
```

### List

列表可看作电子表格中的某一列，虽可以存储多种类型的数据，但常用于存储同一种数据类
型，常用的操作：
```python
>>> s = list('abcde')
>>> s
['a', 'b', 'c', 'd', 'e']

>>> s.append('f')
>>> s
['a', 'b', 'c', 'd', 'e', 'f']

>>> s.extend(['g', 'h'])
>>> s
['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']

>>> s.count('c')
1
>>> s.index('d')
3
>>> s.index('d',4)
ValueError: 'd' is not in list

>>> s.insert(3, 'i')
>>> s
['a', 'b', 'c', 'i', 'd', 'e', 'f', 'g', 'h']

>>> s.pop()
'h'
>>> s
['a', 'b', 'c', 'i', 'd', 'e', 'f', 'g']
>>> s.pop(3)
'i'
>>> s
['a', 'b', 'c', 'd', 'e', 'f', 'g']

>>> s.remove('c')
>>> s
['a', 'b', 'd', 'e', 'f', 'g']
>>> s.extend(['j', 'j'])
>>> s
['a', 'b', 'd', 'e', 'f', 'g', 'j', 'j']
>>> s.remove('j')
>>> s
['a', 'b', 'd', 'e', 'f', 'g', 'j']

>>> s.reverse()
>>> s
['j', 'g', 'f', 'e', 'd', 'b', 'a']
```

list 类的 sort 方法会直接修改原列表的元素顺序，而内置 sorted 函数却不修改原列表
的元素顺序，而是直接返回新的列表，若要用此列表则需要手动保存，而 sort 方法则不可
以直接保存其输出：
```python
>>> s
['j', 'g', 'f', 'e', 'd', 'b', 'a']
>>> sorted(s, reverse = True)
['j', 'g', 'f', 'e', 'd', 'b', 'a']
>>> sorted(s)
['a', 'b', 'd', 'e', 'f', 'g', 'j']
>>> s
['j', 'g', 'f', 'e', 'd', 'b', 'a']
>>> s.sort()
>>> s
['a', 'b', 'd', 'e', 'f', 'g', 'j']
>>> k = s.sort()
>>> k
>>> k is None
True
```

list 的 sort 方法和 sorted 函数都有关键字参数 key：
```python
>>> members = [['zhang', 12], ['li', 25], ['wang', 6]]

>>> members.sort(key = lambda name:name[0])
>>> members
[['li', 25], ['wang', 6], ['zhang', 12]]

>>> members.sort(key = lambda age:age[1])
>>> members
[['wang', 6], ['zhang', 12], ['li', 25]]

>>> m = sorted(members, key = lambda name:name[0])
>>> m
[['li', 25], ['wang', 6], ['zhang', 12]]
>>> m = sorted(members, key = lambda age:age[1])
>>> m
[['wang', 6], ['zhang', 12], ['li', 25]]
```

### String

和列表方法不同，字符串方法不直接修改原字符串，而是返回另一个新的字符串对象：
```python
>>> 'abc'.startswith('ab')
True
>>> 'abc'.startswith('abc')
True
>>> 'abc'.endswith('c')
True
>>> 'a\tb\tc'.expandtabs(8)
'a       b       c'


>>> 'abcabc'.count('b')
2
>>> 'abcabcabc'.replace('bc','BC')
'aBCaBCaBC'

>>> 'abcabc'.find('b')
1
>>> 'abcabc'.rfind('b')
4
>>> 'abcabc'.find('d')
-1
>>> 'abcabc'.index('b')
1
>>> 'abcabc'.rindex('b')
4
>>> 'abcabc'.index('d')
ValueError: substring not found


>>> 'abc123'.isalnum()
True
>>> 'abc'.isalpha()
True
>>> '123'.isdigit()
True
>>> 'ab c'.isspace()
False
>>> '   '.isspace()
True

>>> 'Hello World'.istitle()
True
>>> 'i am from china'.capitalize()
'I am from china'
>>> 'i am from china'.title()
'I Am From China'


>>> 'abc'.center(20)
'        abc         '
>>> 'abc'.center(20, '-')
'--------abc---------'
>>> 'abc'.ljust(10, '-')
'abc-------'
>>> 'abc'.rjust(10, '-')
'-------abc'
>>> 'abc'.zfill(10)
'0000000abc'


>>> 'abcabc'.strip('ac')
'bcab'
>>> ' abc'.lstrip(' ab')
'c'
>>> 'abcabc'.rstrip('bc')
'abca'

>>> 'abc'.islower()
True
>>> 'Ab'.isupper()
False
>>> 'ABC'.isupper()
True
>>> 'ABC'.lower()
'abc'
>>> 'abc'.upper()
'ABC'
>>> 'Hello World'.swapcase()
'hELLO wORLD'

>>> '-'.join(['a','b','c'])
'a-b-c'

>>> 'abcabcabc'.split('c')
['ab', 'ab', 'ab', '']
>>> 'abcabcabc'.rsplit('c')
['ab', 'ab', 'ab', '']
>>> 'abcabcabc'.splitlines()
['abcabcabc']
>>> 'abc\nabc\nabc\n'.splitlines()
['abc', 'abc', 'abc']
>>> 'abc\nabc\nabc\n'.splitlines(1)
['abc\n', 'abc\n', 'abc\n']
>>> 'abc\nabc\nabc\n'.splitlines(keepends = 1)
['abc\n', 'abc\n', 'abc\n']

>>> 'My name is {}, I am {} years old, my height is {}'.format('shen', 20, 173.5)
'My name is shen, I am 20 years old, my height is 173.5'

>>> 'I am {1} years old, my height is {2}, my name is {0}'.format('shen', 20, 173.5)
'I am 20 years old, my height is 173.5, my name is shen'

>>> 'I am {age} years old, my height is {height}, my name is {name}'.format(name
= 'shen', age = 20, height = 173.5)
'I am 20 years old, my height is 173.5, my name is shen'
```

## Mapping Types

### Dict

字典是 Python 中唯一内置的映射类型
```python
>>> d = {'a':1, 'b':2, 'c':3}
>>> len(d)
3
>>> d['b']
2
>>> 3 in d
False
>>> 'c' in d
True
>>> d.get('c', 'not foud')
3
>>> d.get('d', 'not foud')
'not foud'
>>> d.get('c')
3
>>> d.get('c', 33)
3
>>> d.get('e', 55)
55

>>> d.update({'d':4, 'e':5})
>>> d
{'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}

>>> e = d.copy()
>>> id(d);id(e)
2234402513688
2234402513904

>>> f = {}
>>> f = f.fromkeys(d, 1)
>>> f
{'a': 1, 'b': 1, 'c': 1}
>>> f = f.fromkeys(d)
>>> f
{'a': None, 'b': None, 'c': None}
>>> dict.fromkeys(['a','b','c'])
{'a': None, 'b': None, 'c': None}
>>> dict.fromkeys(['a','b','c'], 3)
{'a': 3, 'b': 3, 'c': 3}

>>> d.setdefault('c', 33)
3
>>> d.setdefault('d', 4)
4
>>> d
{'a': 1, 'b': 2, 'c': 3, 'd': 4}

>>> d.pop('c', 'not found')
3
>>> d
{'a': 1, 'b': 2, 'd': 4}
>>> d.pop('c', 'not found')
'not found'

>>> d.keys()
dict_keys(['a', 'b', 'd'])
>>> list(d.keys())
['a', 'b', 'd']
>>> d.values()
dict_values([1, 2, 4])
>>> list(d.values())
[1, 2, 4]
>>> d.items()
dict_items([('a', 1), ('b', 2), ('d', 4)])
>>> list(d.items())
[('a', 1), ('b', 2), ('d', 4)]
```

### Set

* 集合元素是无序的、不可重复的。
* 集合分两种类型：set 和 frozenset
* 两种集合中的元素都必须是不可更改类型，如把可更改类型列表添加到集合则出错。
* set 可以添加、删除元素，但 frozenset 则不允许对元素进行此操作。

```python
>>> s = {1,2,3,4,2,3,4}
>>> s
{1, 2, 3, 4}
>>> s. add(['a','b'])
TypeError: unhashable type: 'list'
>>> s. add(('a','b'))
>>> s
{1, 2, 3, 4, ('a', 'b')}
>>> s. add({'a':1 ,'b': 2})
TypeError: unhashable type: 'dict'
```

set 和 frozenset 都有的操作：
```python
>>> s
{1, 2, 3, 4, ('a', 'b')}
>>> len(s)
5
>>> t = s.copy()
>>> t
{1, 2, 3, 4, ('a', 'b')}
>>> id(s);id(t)
1952032744616
1952032744392
>>> t.add(5)
>>> s;t
{1, 2, 3, 4, ('a', 'b')}
{1, 2, 3, 4, 5, ('a', 'b')}

>>> s.isdisjoint(t)
False
>>> s.issubset(t)
True
>>> t.issuperset(s)
True

>>> s.difference(t)
set()
>>> t.difference(s)
{5}
>>> s.intersection(t)
{1, 2, 3, 4, ('a', 'b')}
>>> s.symmetric_difference(t)
{5}

>>> s.union(t)
{1, 2, 3, 4, 5, ('a', 'b')}
```

frozenset 没有而 set 独有的操作：
```python
>>> s = {1, 2, 3, 4}
>>> s.add(5)
>>> s
{1, 2, 3, 4, 5}
>>> t = s.copy()
>>> t
{1, 2, 3, 4, 5}
>>> t.update([5,6,7])
>>> t
{1, 2, 3, 4, 5, 6, 7}
>>> t.remove(4)
>>> t.pop()
1
>>> t
{2, 3, 5, 6, 7}
>>> t.remove(99)
Traceback (most recent call last):
  File "<pyshell#10>", line 1, in <module>
    t.remove(99)
KeyError: 99
>>> t.discard(99)
>>> s.intersection_update(t)
>>> s
{2, 3, 5}
>>> t
{2, 3, 5, 6, 7}
>>> s.difference_update(t)
>>> t
{2, 3, 5, 6, 7}
>>> s.symmetric_difference_update(t)
>>> s
{2, 3, 5, 6, 7}
>>> s.clear()
>>> s
set()
```

## 表示程序结构的内置类型

### 可调用类型

#### User-defined functions

包含了 def 语句创建的函数和 lambda 操作符创建的函数

自定义函数的属性：
```python
>>> def add2num(x, y = 3):
	'''This function add 2 numbers
	and return the sum'''
	z = 1
	return x + y + z

>>> add2num.__name__
'add2num'

>>> add2num.__doc__
'This function add 2 numbers\n\tand return the sum'

>>> add2num.__dict__
{}

>>> add2num.__code__
<code object add2num at 0x0000019F971E8D20, file "<pyshell#18>", line 1>

>>> add2num.__defaults__
(3,)

>>> add2num.__globals__
{'__name__': '__main__', '__doc__': None, '__package__': None, '__loader__':
<class '_frozen_importlib.BuiltinImporter'>, '__spec__': None,
'__annotations__': {}, '__builtins__': <module 'builtins' (built-in)>, 'x':
<function <lambda> at 0x0000019F9725C048>, 'add2num': <function add2num at
0x0000019F9687C268>}

>>> add2num.__closure__
```

#### Methods

类中包含 3 种方法：类方法、实例方法、静态方法

```python
git status
git add -A
git commit -m "notes"
git push
```

#### Classes and instances are callable

```python
from functions import wrap

    @wraps_me
    def wrapper(*args, **kwargs):
        def
    return wrapper

    @aps_me
    def wrapper():
        pass

    @weibo
    def test():
        def test2():
            sss
```

### Class, types and instances 

### 模块

### 特殊方法

#### Object creation and destruction

```python
git status
```

#### String representation

```python

```

#### Type checking

```python

```

#### Attribute access

```python

```

#### Descriptors

```python

```

#### Sequence and mapping methods

```python

```

#### Iteration

```python

```

#### Callable interface

```python

```

#### Context management protocol

```python

```

#### Object inspection and dir()

```python

```
