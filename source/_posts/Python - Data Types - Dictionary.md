---
title: Python - Data Types - Dictionary
categories:
- Python
tags:
- Python
- Dictionary
date: 2019-05-06 18:00:08
---

Python - Data Types - Dictionary

<!--more-->

## Dictionary

Python 中的 Dictionary 特点：
* 是 Python 内置的映射类型，使用键值对，提供了一种直观的数据存储方式
* 无序集合
* 键不能重复，但值可以重复

为什么需要 Dictionary?
Dictionary 解决了高效存储大数据集的问题，Python 对 Dictionary 对象进行了高度优化
，使其更适合于检索数据。

## 创建 Dictionary

创建 Dict 和 Set 一样，也使用大括号语法和逗号分隔元素，不过元素是键值对：
```python
>>> dict = {'a':1, 'b':2, 'c':3}
>>> print(dict)
{'a': 1, 'b': 2, 'c': 3}
>>> print(dict['b'])
2
>>> dict['c'] = 33
```

注意默认的空大括号是字典不是集合：
```python
>>> type({})
<class 'dict'>
```

值可以重复，但键不能重复，键名相同则后面出现的值会覆盖掉前面出现的值：
```python
>>> d = {'a':1, 'b':2, 'c':3, 'a':4, 'd':2}
>>> d
{'a': 4, 'b': 2, 'c': 3, 'd': 2}
```

可以混合使用任何 Python **不可变**数据类型作为键：
```python
>>> d = {1.2:'c', 'a':1, True:2, 33:44, 'string':'hi'}
>>> d[1.2]
'c'
>>> d[True]
2
>>> d[33]
44
```

Also, remember to use only immutable data types for the values while they can
have duplicates.

???还可以使用内置函数 dict 创建字典，其接收可迭代对象或 Mappting 对象：
```python
>>> d = dict(one=1,two=2,three=3)
>>> d
{'one': 1, 'two': 2, 'three': 3}

>>> d = dict(zip(['one','two', 'three'],[1, 2, 3]))
>>> d
{'one': 1, 'two': 2, 'three': 3}

>>> d = dict({'one':1, 'two':2, 'three':3})
>>> d
{'one': 1, 'two': 2, 'three': 3}

>>> d = dict([('one', 1),('two', 2),('three', 3)])
>>> d
{'one': 1, 'two': 2, 'three': 3}
```

## 获取元素

通过键或者通过 get 方法，后者在获取不存在的键时会报错 KeyError，后者在获取不存在
的键时返回 None：
```python
>>> dict = {'a':1, 'b':2, 'c':3}

>>> dict['a']
1
>>> dict['d']
KeyError: 'd'

>>> dict.get('a')
1
>>> dict.get('d')
>>> type(dict.get('d'))
<class 'NoneType'>
```

## 添加和更新元素

添加元素方式一：直接为不存在的键赋值
```python
>>> dict = {'a': 1, 'b': 2, 'c': 3}
>>> dict['d'] = 4
>>> dict
{'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

添加元素方式二：使用 update 方法，两种语法
```python
>>> dict = {'a': 1, 'b': 2, 'c': 3, 'd': 4}
>>> dict.update({'e': 5})
>>> dict
{'a': 1, 'b': 2, 'c': 3, 'd': 4, 'e': 5}

>>> d = {'a':1, 'b':2, 'c':3}
>>> d.update(d = 4)
>>> d
{'a': 1, 'b': 2, 'c': 3, 'd': 4}
```

## 删除元素

使用 pop 方法根据键删除指定元素，此方法返回都是指定键的值，注意不同于 List 的
pop 方法可无参数默认删除最后一个元素，字典的 pop 方法必须提供参数：
```python
>>> d = {'a':1, 'b':2, 'c':3}
>>> d.pop()
TypeError: pop expected at least 1 arguments, got 0
>>> d.pop('c')
3
>>> d
{'a': 1, 'b': 2}
```

popitem 随机删除一个元素，并以元组的形式返回被删除的元素，此方法不能有参数：
```python
>>> d = {'a': 1, 'b': 2}
>>> d.popitem('a')
TypeError: popitem() takes no arguments (1 given)
>>> d.popitem()
('b', 2)
>>> d
{'a': 1}
```

del 删除指定元素或删除整个字典变量，clear 清空元素：
```python
>>> del dict['e']
>>> dict
{'a': 1, 'b': 2, 'c': 3, 'd': 4}

>>> d.clear()
>>> d
{}

>>> del d
>>> d
NameError: name 'd' is not defined
```

## 字典属性

Python 对字典对象的“值”没有任何约束，可以使用标准 Python 数据类型或任何自定义
数据类型。但是“键”与“值”不同，处理方式完全不同。关于字典键的两个重要事实：
* 字典中的键不允许重复
* 字典的键应该只是不可变数据列序，即字符串、数字、布尔和元组
```python
>>> d = {True:1, 'Str':2, 3.4:3, 5:5, (1,2,3):4}
>>> d
{True: 1, 'Str': 2, 3.4: 3, 5: 5, (1, 2, 3): 4}

>>> d = {[1,2,3]:33}
TypeError: unhashable type: 'list'
```

## 迭代字典

使用字典对象只获取键，或使用字典对象的 items 方法获取键和值：
```python
>>> d = {True:1, 'Str':2, 3.4:3, 5:5, (1,2,3):4}
>>> for i in d:
...     print(i)
...
True
Str
3.4
5
(1, 2, 3)

>>> for i, v in d.items():
...     print(i,'       ---',v)
...
True    --- 1
Str     --- 2
3.4     --- 3
5       --- 5
(1, 2, 3)       --- 4
```

## 字典推导

像列表推导一样

语法格式:
`theDict = [key:val for key in oldDict if filter(key)]`

```python
>>> d = {k:k*2 for k in [1,2,3,4,5,6,7,8] if k%2 == 0}
>>> d
{2: 4, 4: 8, 6: 12, 8: 16}

>>> {k:v for k,v in enumerate(['a','b','c','d'])}
{0: 'a', 1: 'b', 2: 'c', 3: 'd'}
```

## 判断成员

```python
>>> d = {'a':1, 'b':2, 'c':3, 'd':4}
>>> 'a' in d
True
>>> 1 in d
False
>>> 1 not in d
True
```

## 内置方法

内置的 Dict 的方法：
* keys() 从字典提取所有键，返回类型是 dict_keys
* values() 从字典提取所有值，返回类型是 dict_values
* items() 从字典提取所有键值对返回，返回类型是 dict_items
```python
>>> dict = {'a':1, 'b':2, 'c':3}
>>> dict.keys()
dict_keys(['a', 'b', 'c'])
>>> dict.values()
dict_values([1, 2, 3])
>>> dict.items()
dict_items([('a', 1), ('b', 2), ('c', 3)])
```

注意其返回类型不是 List，不可以通过索引的方式取值，但可以循环：
```python
>>> type(dict.keys())
<class 'dict_keys'>
>>> type(dict.values())
<class 'dict_values'>
>>> type(dict.items())
<class 'dict_items'>

>>> print(dict.keys()[0])
TypeError: 'dict_keys' object does not support indexing

>>> for s in dict.keys():
...     print(s)
...
a
b
c
>>> for s in dict.values():
...     print(s)
...
1
2
3
>>> for s in dict.items():
...     for a in s:
...             print(a)
...
a
1
b
2
c
3
```

