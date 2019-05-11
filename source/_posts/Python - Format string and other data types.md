---
title: Python - Format string and other data types
categories:
- Python
tags:
- Python
- Python Format
date: 2019-05-10 18:00:55
---

Python - Format string and other data types

<!--more-->

## Format 函数

格式：`{}` 是占位符，`argN` 是要显式的任意数据类型
```python
'{} {}...{}'.format(arg1, arg2....argN)
```

占位符的数量可以少于 format 参数数量，但不能多于 format 参数数量：
```python
>>> '{}--{}---{}'.format(3, 4, 5)
'3--4---5'

>>> '{}--{}---'.format(3, 4, 5)
'3--4---'

>>> '{}--{}--{}--{}'.format(3, 4, 5)
IndexError: tuple index out of range
```

占位符可以指定次序：
```python
>>> '{}--{}---{}'.format(3, 4, 5)
'3--4---5'

>>> '{2}--{1}---{0}'.format(3, 4, 5)
'5--4---3'
```

## Format String

### 填充和对齐字符串

可以使用空格或其他字符对字符串进行对齐。
设置对齐长度，该长度应该大于目标字符串的大小。这是因为对齐只能发生在一个固定长度
的框中。
```python
>>> format('hello,world','#>20s')
'#########hello,world'
>>> format('hello,world','#<20s')
'hello,world#########'
>>> format('hello,world','#^20s')
'####hello,world#####'

>>> format('hello,world','>10s')
'hello,world'
>>> format('hello,world','>20s')
'         hello,world'
```

### 验证变量字符串展开

```python
>>> '---{0:30}---'.format('hello,world','wahaha')
'---hello,world                   ---'
>>> '---{1:30}---'.format('hello,world','wahaha')
'---wahaha                        ---'

>>> '---{1:<30}---'.format('hello,world','wahaha')
'---wahaha                        ---'
>>> '---{1:>30}---'.format('hello,world','wahaha')
'---                        wahaha---'
>>> '---{1:^30}---'.format('hello,world','wahaha')
'---            wahaha            ---'
```
`{1:>30}` 1 表示对应 format 的第二个参数，30 是字符串总长度，`<>^`则分别表示左对
齐，右对齐，中间对齐

`{1:>30}` 中的 1 也可省略，则按照顺序依次对应 format 参数：
```python
>>> '---{:<30}---{:>30}---'.format('hello,world','wahaha')
'---hello,world                   ---                        wahaha---'
```

## Format Interger

format 支持显式数字时使用千位分隔符，需要设置占位符格式为 `{:,}`
```python
>>> '{:,}'.format(1234567890123)
'1,234,567,890,123'
```

对字符串使用格式也支持整型：
```python
>>> '{:-^30,}'.format(1234567890123)
'------1,234,567,890,123-------'
```

格式化输出为二进制、八进制、十六进制：
```python
>>> '{0:b}'.format(128)
'10000000'

>>> '{0:o}'.format(128)
'200'

>>> '{0:x}'.format(128)
'80'
```

## Format Float

输出浮点数：
```python
>>> '{}'.format(888.123456789)
'888.123456789'

>>> '{0:f}'.format(888.123456789)
'888.123457'

>>> '{0:f}'.format(888.1)
'888.100000'

>>> '{0:<25.10f}'.format(888.134)
'888.1340000000           '
```

截至到小数点后3位：
```python
>>> '{0:.3f}'.format(888.123456789)
'888.123'
```

### General

```python
>>> '{0:^25.10g}'.format(2.2183)
'         2.2183          '
```

### Scientific

科学计数法
```python
>>> '{0:<25.5e}'.format(2.2183)
'2.21830e+00              '
```

## Format List

```python
>>> '--{}--'.format(('a','b','c'))
"--('a', 'b', 'c')--"
>>> '--{}--'.format({'a','b','c'})
"--{'b', 'a', 'c'}--"
>>> '--{}--'.format({'a':1,'b':2,'c':3})
"--{'a': 1, 'b': 2, 'c': 3}--"
```

```python
>>> '--{}--'.format(['a','b','c'])
"--['a', 'b', 'c']--"
```

通过索引只输出某项
```python
>>> '--{0[1]}--'.format(['a','b','c'])
'--b--'
```

将列表作为位置参数
```python
>>> '--{}--'.format(*['a','b','c'])
'--a--'
```

## Format Dict

```python
>>> '--{0[name]}--{0[age]}--'.format({'name':'shen', 'age':33})
'--shen--33--'

>>> '--{person[name]}--{person[age]}--'.format(person = {'name':'shen', 'age':33})
'--shen--33--'
```

