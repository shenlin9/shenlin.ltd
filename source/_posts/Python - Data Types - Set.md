---
title: Python - Data Types - Set
categories:
- Python
tags:
- Python
- Python Set
date: 2019-05-05 14:23:09
---

Python - Data Types - Set

<!--more-->

Set 数据类型支持数学运算，例如并集、交集、补集、对称差分等。

Set 数据类型特点：
* 元素无序
* 没有索引
* 元素需唯一不可重复（因此 Set 数据类型由数学的集合派生其实现）
* 元素均需为不可变对象，因此一旦添加就不能更改
* 集合本身是可变的，可以添加、删除元素
* 以大括号包含元素，元素之间逗号分隔

## 为什么需要 Set 数据类型：

当检查是否包含特定的元素时，Set 类型比 List 具有明显的优势，因为 Set 实现了一个
基于哈希表的高度优化的方法。

## 创建 Set 数据类型

如果元素数量固定，直接使用大括号语法：
```python
>>> my_set = {'abc', True, 123, "abc"}`     # 重复元素会被自动过滤
>>> print(my_set)
{True, 123, 'abc'}
```

还可以使用内置 set 函数，其接收的参数是可迭代的对象：
```python
>>> my_set = set("abc")
>>> print(my_set)
{'b', 'a', 'c'}

>>> s = set(['11', '22', '33'])
>>> s
{'22', '11', '33'}
>>> s = set(('11', '22', '33'))
>>> s
{'22', '11', '33'}
```

因为集合和字典都使用大括号，所以对于一对空的大括号，Python 创建的是字典对象：
```python
>>> sd = {}
>>> type(sd)
<class 'dict'>
>>> sd
{}
```

创建空集合对象，请使用 set：
```python
>>> sd = set()
>>> type(sd)
<class 'set'>
>>> sd
set()
```

Set 中的元素可以为各种类型，但必须都是不可变的，像列表、集合、字典等可变类型不可
作为集合的元素，这是因为集合使用了哈希算法，需要所有的元素都是可进行哈希运算的：
```python
>>> s = {1, True, (1, 2, 3)}
>>> s
{1, (1, 2, 3)}

>>> s = {1, True, [1, 2, 3]}            # 把上面的元组换为列表出错
TypeError: unhashable type: 'list'
```

## 集合元素操作

无法直接访问集合中某个元素，可以使用循环获取元素：
```python
>>> for s in {1, 2, 3, 4}:
...     s
...
1
2
3
4
```

使用 in 检测元素是否在集合中：
```python
>>> 1 in {1, 2, 3}
True
>>> 4 in {1, 2, 3}
False
```

## 修改集合

集合对象没有索引，因此集合元素没有顺序，也不能通过索引操作集合元素：
```python
>>> s = {1, 2, 3, 4}
>>> s[0]
TypeError: 'set' object does not support indexing
```

使用集合的 add 方法一次添加一个元素：
```python
>>> s = {1, 2, 3, 4}
>>> s.add(5)
>>> s
{1, 2, 3, 4, 5}
```

集合的 update 方法接收可迭代对象（元组、列表、集合、字符串），因此可以一次添加多
个元素：
```python
>>> s = {1, 2, 3, 4}
>>> s.update(5, 6, 7)
TypeError: 'int' object is not iterable
>>> s.update("567")
>>> s
{'5', 1, 2, 3, 4, '7', '6'}
>>> s.update([8, 9])
>>> s
{'5', 1, 2, 3, 4, '7', 8, 9, '6'}
>>> s.update({'a', 'b'})
>>> s
{'5', 1, 2, 3, 4, '7', 'b', 8, 9, 'a', '6'}
```

## 删除元素

集合也支持 pop 和 clear 方法，但因为集合没有索引，元素没有顺序，无法确定最后一个
元素，所以 pop 方法也只是随机的移除一个元素：
```python
>>> s
{'5', 1, 2, 3, 4, '7', 'b', 8, 9, 'a', '6'}
>>> s.pop()
'5'
>>> s.pop()
1
>>> s.pop()
2
>>> s.pop()
3
>>> s.pop()
4
>>> s.pop()
'7'
>>> s
{'b', 8, 9, 'a', '6'}
```

clear 方法清空所有元素：
```python
>>> s
{'b', 8, 9, 'a', '6'}
>>> s.clear()
>>> s
set()
```

集合还可使用 discard 和 remove 方法来移除元素，两者的区别是当要移除的元素不存在
时，discard 方法不抛出异常，而 remove 方法抛出 KeyError 异常：
```python
>>> s = {1, 2, 3, 4}
>>> s.discard(2)
>>> s
{1, 3, 4}
>>> s.remove(3)
>>> s
{1, 4}
>>> s.discard(9)
>>> s
{1, 4}
>>> s.remove(9)
KeyError: 9
>>>
```

## 集合运算

**并集**

使用操作符 `|` 或使用集合的 union 方法：
```python
>>> a = {1, 2, 3}
>>> b = {3, 4, 5}
>>> a | b
{1, 2, 3, 4, 5}
>>> a.union(b)
{1, 2, 3, 4, 5}
>>> b.union(a)
{1, 2, 3, 4, 5}
```

**交集**

使用操作符 `&` 或使用集合的 intersection 方法：
```python
>>> a = {1, 2, 3}
>>> b = {3, 4, 5}
>>> a & b
{3}
>>> a.intersection(b)
{3}
>>> b.intersection(a)
{3}
```

**差集**

集合 A 对集合 B 的差集，指的是在集合 A 中但不在集合 B 中。

使用操作符 `-` 或使用集合的 difference 方法：
```python
>>> a = {1, 2, 3}
>>> b = {3, 4, 5}

>>> a - b
{1, 2}
>>> b - a
{4, 5}

>>> a.difference(b)
{1, 2}
>>> b.difference(a)
{4, 5}
```

**对称差集**

指的是在集合 A 或在集合 B 中，但不能同时位于集合 A 和集合 B 中。

使用操作符 `^` 或使用集合的 symmetric_difference 方法：
```python
>>> a = {1, 2, 3}
>>> b = {3, 4, 5}
>>> a ^ b
{1, 2, 4, 5}
>>> b ^ a
{1, 2, 4, 5}

>>> a.symmetric_difference(b)
{1, 2, 4, 5}
>>> b.symmetric_difference(a)
{1, 2, 4, 5}
```

## Frozen Set

普通的集合本身是可变的，即可添加、删除元素，这使得普通集合不可哈希，也意味着普通
集合不可用作字典的键。

Frozen Set 是一种特殊的集合类型，这种集合创建后默认是不可改变的，即不可添加、删
除元素。这使得 Frozen Set 可进行哈希运算，可以作为字典的键。

使用 frozenset 方法创建并返回 Frozen Set，其参数为可迭代对象：
```python
>>> type(frozenset(['a', 'b', 'c']))
<class 'frozenset'>
>>> print(frozenset(['a', 'b', 'c']))
frozenset({'c', 'b', 'a'})
```

Frozen Set 支持适用于集合的所有方法和操作符，但不支持更改集合内容的方法和操作符
。如普通集合可以通过 add 添加元素，而 Frozen Set 则不可：
```python
>>> a = {1, 2, 3}
>>> b = frozenset(a)
>>> a.add(4)
>>> print(a)
{1, 2, 3, 4}
>>> print(b)
frozenset({1, 2, 3})
```

Frozen Set 支持的方法：

    copy()
    difference()
    intersection()
    isdisjoint()
    issubset()
    issuperset()
    symmetric_difference()
    union()

