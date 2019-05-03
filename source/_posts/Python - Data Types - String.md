---
title: Python - Data Types - String
categories:
- Python
tags:
- Python
- Python String
date: 2019-05-03 09:37:53
---

Python - Data Types - String

<!--more-->

## String Functions

### Conversion Functions

**Capitalize()**

最大化首字母，其他字母全部转换为小写字母：
```python
>>> "HELLO, WORLD!".capitalize()
'Hello, world!'
```

**lower(), upper()**

全部转换为小写或大写：
```python
>>> "Hello, world!".lower()
'hello, world!'

>>> "Hello, world!".upper()
'HELLO, WORLD!'
```

**swapcase()**

大小写调换，即大写字母转为小写，小写字母转为大写：
```python
>>> "Hello, world!".swapcase()
'hELLO, WORLD!'
```

**title()**

按标题样式返回字符串，即每个单词的首字母大写：
```python
>>> "You are welcome in China at any time!".title()
'You Are Welcome In China At Any Time!'
```

**count( str[, beg [, end]])**

返回在指定的起使和终止索引范围内（左含右不含）子字符串出现的次数，大小写敏感：
```python
>>> "01230123".count('0')
2
>>> "01230123".count('0', 0, 4)
1
>>> "01230123".count('0', 0, 5)
2
```

### Comparison Functions

**islower(), isupper()**

当全部字符串都是小写或都是大写时返回 True，全是数字字符串则始终返回 False，包括
数字时则忽略数字只根据字母判断：
```python
>>> "Hello".islower()
False
>>> "Hello".isupper()
False

>>> "01230123".islower()
False
>>> "01230123".isupper()
False

>>> "abc01230123".islower()
True
>>> "abc01230123".isupper()
False

>>> "ABC01230123".islower()
False
>>> "ABC01230123".isupper()
True
```

**isdecimal()**

如果字符串中的所有字符都是十进制字符(需至少包含一个字符，即为非空字符串)，则返回 True
，否则返回 false。

???十进制字符是那些可以用来形成以 10 为基数的数字，例如 U+0660，阿拉伯-印度数字 0。

???在形式上，十进制字符是 Unicode 一般类别“Nd”中的字符。
```python
>>> '123'.isdecimal()
True
>>> u'0660'.isdecimal()
True

>>> '123A'.isdecimal()
False
```

**isdigit()**

如果字符串中的所有字符都是数字(需至少包含一个字符，即为非空字符串)，则返回 True
，否则返回false。

数字包括十进制字符和需要特殊处理的数字，如兼容性上标数字。这包括不能用于以10为基
数形成数字的数字，比如Kharosthi数字。在形式上，数字是具有属性值Numeric_Type=
digit或Numeric_Type=Decimal的字符。

**isnumeric()**

如果字符串中的所有字符都是数字字符，并且至少有一个字符，则返回true，否则返回false。

数字字符包括数字字符，以及所有具有Unicode数值属性的字符，例如U+2155，粗俗分数1 /
5。在形式上，数字字符是那些具有属性值Numeric_Type=Digit、Numeric_Type=Decimal或
Numeric_Type= numeric的字符。

```python
>>> "四".isdecimal()      # 中文数字
False
>>> "四".isdigit()
False
>>> "四".isnumeric()
True


>>> "12345½".isdecimal()   # 分数
False
>>> "12345½".isdigit()
False
>>> "12345½".isnumeric()
True

>>> "Ⅳ".isdecimal()        # 注意这里不是字母 I 和 V，而是罗马数字 4
False
>>> "Ⅳ".isdigit()
False
>>> "Ⅳ".isnumeric()
True
```

**isalpha()**

如果字符串所有字符都是字母(至少包含一个字符，非空字符串)，则返回 True：
```python
>>> 'Ilivein24'.isalpha()
False
>>> 'Ilivein '.isalpha()
False
>>> 'Ilivein'.isalpha()
True
```

**isalnum()**

如果字符串所有字符都是字母或小数(至少包含一个字符，非空字符串)，则返回 True：
```python
>>> 'I live in 24'.isalnum()
False

>>> 'Ilivein24'.isalnum()
True
```

### Padding Functions

**ljust(width[,fillchar]), rjust(width[,fillchar]), center(width[,fillchar])**

使用指定的字符 fillchar，把字符串填充的指定的长度 width，原字符串可位于左侧、中
间、右侧：
```python
>>> 'chapter 13.1'.ljust(40, '-')
'chapter 13.1----------------------------'

>>> 'chapter 13.1'.rjust(40, '-')
'----------------------------chapter 13.1'

>>> 'chapter 13.1'.center(40, '-')
'--------------chapter 13.1--------------'
```

若指定的填充长度还没有原字符串长，则返回源字符串：
```python
>>> 'chapter 13.1'.center(5, '-')
'chapter 13.1'
>>> 'chapter 13.1'.rjust(5, '-')
'chapter 13.1'
```

**zfill(width)**

左侧用 0 填充字符串到指定长度，若字符串以 + 或 - 号开头，则填充的 0 位于 + 或 -
号后面：
```python
>>> 'chapter 13.1'.zfill(15)
'000chapter 13.1'

>>> '+chapter 13.1'.zfill(15)
'+00chapter 13.1'
>>> '-chapter 13.1'.zfill(15)
'-00chapter 13.1'
```

### Search Functions

**find(sub[, start[, end]]), rfind(sub[, start[, end]])**

find 返回子字符串的第一次出现处的索引，rfind 返回子字符串最后一次出现处的索引，
没有找到时返回 -1：
```python
>>> "so what who where?".find("w")
3
>>> "so what who where?".rfind("w")
12
>>> "so what who where?".find("m")
-1
>>> "so what who where?".rfind("m")
-1
```

**index(sub[, start[, end]]), rindex(sub[, start[, end]])**

和 find、rfind 一样，不同的是：如果没有找到子字符串，不是返回 -1，而是会抛出一个
 ValueError异常：
```python
>>> "so what who where?".index("w")
3
>>> "so what who where?".rindex("w")
12
>>> "so what who where?".index("m")
ValueError: substring not found
>>> "so what who where?".rindex("m")
ValueError: substring not found
```

### String Substitution Functions

**replace(old,new[,count])**

对字符串进行指定次数替换或全部替换
```python
>>> "This is a exmaple, isn't it?".replace('is', 'was')
"Thwas was a exmaple, wasn't it?"

>>> "This is a exmaple, isn't it?".replace('is', 'was', 2)
"Thwas was a exmaple, isn't it?"
```

**split([sep[,maxsplit]])**

对字符串使用指定分隔符按指定最大分隔次数分隔，或全部分隔，返回一个 List
默认分隔符是空格，分隔符不能为空，如 ''
若指定了最大分隔次数 maxsplit，则元素个数 = maxsplit + 1
```python
>>> "This is a exmaple, isn't it?".split()
['This', 'is', 'a', 'exmaple,', "isn't", 'it?']

>>> "This is a exmaple, isn't it?".split(' ', 3)
['This', 'is', 'a', "exmaple, isn't it?"]

>>> "This is a exmaple, isn't it?".split(' ', 100)
['This', 'is', 'a', 'exmaple,', "isn't", 'it?']

>>> "This is a exmaple, isn't it?".split('', 3)
ValueError: empty separator
```

**splitlines([num])**

对字符串按换行符进行分隔，若 num 值为正数，则换行符包含在返回的 List 中：
```python
>>> str1 = '''hello,
... everyone,
... I am a software living in china'''

>>> str1.splitlines()
['hello,', 'everyone,', 'I am a software living in china']

>>> str1.splitlines(1)
['hello,\n', 'everyone,\n', 'I am a software living in china']
```

**join(iterable)**

使用指定分隔符把可迭代对象连接为一个字符串：
```python
>>> '--'.join(('a', 'b', 'c', 'd'))
'a--b--c--d'

>>> '--'.join(['a', 'b', 'c', 'd'])
'a--b--c--d'

>>> '--'.join({'a', 'b', 'c', 'd'})
'd--b--a--c'

>>> '--'.join({'a':1, 'b':2, 'c':3, 'd':4})
'a--b--c--d'
```

### Misc String Functions

**strip([chars]), lstrip([chars]), rstrip([chars])**

从字符串开头、结尾或两头移除指定的字符，不指定字符或指定 None 则默认移除空格：
```python
>>> " abc abc abc abc ".lstrip()
'abc abc abc abc '
>>> " abc abc abc abc ".rstrip()
' abc abc abc abc'
>>> " abc abc abc abc ".strip()
'abc abc abc abc'

>>> "abcabcabcab".strip("ab")
'cabcabc'
>>> "abcabcabcab".lstrip("ab")
'cabcabcab'
>>> "abcabcabcab".rstrip("ab")
'abcabcabc'
```

**len(string)**

返回字符串长度：
```python
>>> len("Hello, world!")
13
```

