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

## Create Strings

Python 中使用单引号、双引号、三个单引号或三个双引号包含起来的一个或多个字符序列
称为字符串。

Python 中单引号、双引号的作用一样：
```python
>>> str = 'abc\ndef'
>>> str
'abc\ndef'

>>> str = "abc\ndef"
>>> str
'abc\ndef'

>>> print('a\nb')
a
b
>>> print("a\nb")
a
b
```

Python 使用三个引号支持换行字符串：
```python
>>> str = '''
... abc
... def
... '''
>>> str
'\nabc\ndef\n'

>>> str = """
... abc
... def
... """
>>> str
'\nabc\ndef\n'
```

可以在单引号中使用双引号，双引号中使用单引号，三引号中使用单、双引号：
```python
>>> s = """
... abc"def
... """
>>> d = '''
... mno'nno
... '''
>>>
>>> s = """
... abc'ddd
... """
```

## Immutable

Python 中的字符串是不可变的，这表示将分配一次内存并在此后重复使用：
```python
>>> s1 = 'abc'
>>> s2 = s1
>>> id(s1)
1939387111384
>>> id(s2)
1939387111384

>>> s2 = s1 + 'd'
>>> id(s2)
1939417032048
>>> id(s1)
1939387111384
```

若要对字符串删除字符、修改字符等任何修改都会导致错误：
```python
>>> str = 'i am super man'
>>> str[0] = 'I'
TypeError: 'str' object does not support item assignment

>>> del str[0]
TypeError: 'str' object doesn't support item deletion
```

只可以移除整个字符串：
```python
>>> str1 = 'hello, world!'
>>> del str1
>>> print(str1)
NameError: name 'str1' is not defined
```

## Unicode

Python 的两个流行版本：2.7 和 3.4           （??? 3.4 需确认）

Python 2 中的字符串默认是 8 位 ASCII 编码，字母 'u' 作前缀表示 Unicode 编码字符
串：
```python
>>> type("python string in Python 2")
<type 'str'>

>>> type(u"python string in Python 2")          # 字符串前加 u 表示 Unicode
<type 'unicode'>
```

Python 3 中的字符串默认就是 Unicode 的 UTF-8 编码：
```python
>>> type("python string in Python 3")
<class 'str'>

>>> type(u"python string in Python 3")
<class 'str'>
```

## Escape Characters

Python 中无论是单引号还是双引号，转义字符都会被解释器解释：
```python
>>> print('a\nb')
a
b
>>> print("a\nb")
a
b

>>> print("""a\n
... b""")
a

b
>>> print('''a\n
... b''')
a

b
```

转义字符列表：

    \\	        Backslash (\)
    \”	        Double-quote (“)

    \b	        ASCII backspace (BS)
    \r	        Carriage Return (CR)
    \n	        ASCII linefeed (LF)
    \t	        Horizontal Tab (TAB)
    \v	        ASCII vertical tab (VT)

    \a	        ASCII bell (BEL)
    \f	        ASCII Form feed (FF)
    \cx or \Cx	Control-x

    \N{name}	Character named name in the Unicode database (Unicode only)
    \ooo	    Characters with octal value ooo

    \uxxxx	    A character with 16-bit hex value xxxx (Unicode only)
    \Uxxxxxxxx	A character with 32-bit hex value xxxxxxxx (Unicode only)
    \xnn	    A character with hex value nn where n can be anything from the
                range 0-9, a-f or A-F.

```python
>>> print('ab\bc')
ac
>>> print("\xab")
«
>>> print("\xac")
¬
```

## Format Characters

`%` 操作符用于格式化字符串，常和 print 搭配：
```python
>>> print('He is %s, %d years old' % ('jack', 21))
He is jack, 21 years old

>>> print('%s--%d','abc',123)
%s--%d abc 123
>>> print('%s--%d' %('abc',123))
abc--123
```

可使用的格式化字符列表：

    %c	character
    %s	string conversion via str() before formatting
    %i	signed decimal integer
    %d	signed decimal integer
    %u	unsigned decimal integer
    %o	octal integer
    %x	hexadecimal integer (lowercase letters)
    %X	hexadecimal integer (UPPER-case letters)
    %e	exponential notation (with lowercase ‘e’)
    %E	exponential notation (with UPPER-case ‘E’)
    %f	floating point real number
    %g	the shorter of %f and %e
    %G	the shorter of %f and %E

```python
>>> print('%s %o' %('abc',123))
abc 173
>>> print('%s %x' %('abc',123))
abc 7b
>>> print('%s %X' %('abc',123))
abc 7B
>>> print('%s %E' %('abc',123))
abc 1.230000E+02
>>> print('%s %e' %('abc',123))
abc 1.230000e+02
```

## String Operators

### Concatenation +

使用 `+` 号连接字符串：
```python
>>> "this" + "world"
thisworld
```

## Repetition *

使用 `*` 号重复字符串：
```python
>>> str1 = 'xyz'
>>> str1 * 3
'xyzxyzxyz'

>>> 'a' * 3
'aaa'
```

### Range []

使用 `[]` 提取单个字符：
```python
>>> "hello"[1]
'e'

>>> "hello"[-1]
'o'
```

字符串的字符下标从 0 开始，但负数下标，则是从 -1 开始，即：

    P   Y   T   H   O   N
    0   1   2   3   4   5
    -6  -5  -4  -3  -2  -1

### Range Slicing [x:y]

使用 `[x:y]` 语法从字符串中提取子字符串：
```python
>>> str = "0123456789"

>>> print(str[1:4])
123
>>> print(str[4:])
456789
>>> print(str[:4])
0123

>>> print(str[-3:])
789
>>> print(str[:-3])
0123456
>>> print(str[-3:-1])
78

>>> print(str[:])
0123456789
```

### Membership (in & not in)

in 和 not in 判断是否包含子字符串：
```python
>>> 'he' in 'hello'
True

>>> 'he' not in 'hello'
False
```

### Interating (for)

使用 for 逐个字符迭代一个字符串：
```python
>>> for s in 'hello':
...     s
...
'h'
'e'
'l'
'l'
'o'
```

#### Raw String (r/R)

字符串前加 `r` 或 `R` 表示原生字符串，会忽略字符串中转义字符的含义：
```python
>>> 'a\nb'
'a\nb'
>>> print("a\nb")
a
b

>>> print(r"a\nb")
a\nb
>>> print(R"a\nb")
a\nb
```

## String Functions

### Conversion Functions

**str.capitalize()**

最大化首字母，其他字母全部转换为小写字母：
```python
>>> "HELLO, WORLD!".capitalize()
'Hello, world!'
```

**str.lower(), str.upper()**

全部转换为小写或大写：
```python
>>> "Hello, world!".lower()
'hello, world!'

>>> "Hello, world!".upper()
'HELLO, WORLD!'
```

**str.swapcase()**

大小写调换，即大写字母转为小写，小写字母转为大写：
```python
>>> "Hello, world!".swapcase()
'hELLO, WORLD!'
```

**str.title()**

按标题样式返回字符串，即每个单词的首字母大写：
```python
>>> "You are welcome in China at any time!".title()
'You Are Welcome In China At Any Time!'
```

**str.count( str[, beg [, end]])**

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

**str.islower(), str.isupper()**

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

**str.isdecimal()**

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

**str.isdigit()**

如果字符串中的所有字符都是数字(需至少包含一个字符，即为非空字符串)，则返回 True
，否则返回 False。

数字包括十进制字符和需要特殊处理的数字，如兼容性上标数字。这包括不能用于以10为基
数形成数字的数字，比如Kharosthi数字。在形式上，数字是具有属性值Numeric_Type=
digit或Numeric_Type=Decimal的字符。

**isdecimal(), isdigit(), isnumeric()**

如果字符串中的所有字符都是数字字符，并且至少有一个字符，则返回 True，否则返回
False。

数字字符包括数字字符，以及所有具有Unicode数值属性的字符，例如U+2155，粗俗分数1 /
5。在形式上，数字字符是那些具有属性值Numeric_Type=Digit、Numeric_Type=Decimal或
Numeric_Type= numeric的字符。

三个函数的不同在于对待特定的 Unicode 字符时：
```python
>>> "3.14".isdecimal()
False
>>> "3.14".isdigit()
False
>>> "3.14".isnumeric()
False
>>> u'2.15'.isdigit()
False

# 中文数字
>>> "四".isnumeric()
True
>>> "四".isdecimal()
False
>>> "四".isdigit()
False

# 分数
>>> "12345½".isdecimal()
False
>>> "12345½".isdigit()
False
>>> "12345½".isnumeric()
True

## 下标
>>> '3₇'.isnumeric()
True
>>> '3₇'.isdigit()
True
>>> '3₇'.isdecimal()
False

# 注意这里不是字母 I 和 V，而是罗马数字 4
>>> "Ⅳ".isnumeric()
True
>>> "Ⅳ".isdecimal()
False
>>> "Ⅳ".isdigit()
False

# 圆圈数字 22
>>> '㉒'.isnumeric()
True
>>> '㉒'.isdigit()
False
>>> '㉒'.isdecimal()
False

## 反色的圆圈数字 60
>>> '㉍'.isnumeric()
True
>>> '㉍'.isdigit()
False
>>> '㉍'.isdecimal()
False

## 苏州码子，在 Unicode 中标记为 HANGZHOU NUMERAL
>>> '〥'.isnumeric()
True
>>> '〥'.isdigit()
False
>>> '〥'.isdecimal()
False
```

**str.isalpha()**

如果字符串所有字符都是字母(至少包含一个字符，非空字符串)，则返回 True：
```python
>>> 'Ilivein24'.isalpha()
False
>>> 'Ilivein '.isalpha()
False
>>> 'Ilivein'.isalpha()
True
```

**str.isalnum()**

如果字符串所有字符都是字母或小数(至少包含一个字符，非空字符串)，则返回 True：
```python
>>> 'I live in 24'.isalnum()
False

>>> 'Ilivein24'.isalnum()
True
```

### Padding Functions

**str.ljust(width[,fillchar=' ']), str.rjust(width[,fillchar=' ']), str.center(width[,fillchar=' '])**

使用指定的字符 fillchar，把字符串填充到指定的长度 width，原字符串可位于左侧、中
间、右侧，默认填充字符是空格：
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

**str.zfill(width)**

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

不会截断字符串：
```python
>>> 'abcdef'.zfill(3)
'abcdef'
```

### Search Functions

**str.find(sub[, start[, end]]), str.rfind(sub[, start[, end]])**

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

**str.index(sub[, start[, end]]), str.rindex(sub[, start[, end]])**

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

**str.replace(old,new[,count=-1])**

对字符串进行指定次数替换或全部替换，count 默认值 -1 表示全部替换：
```python
>>> "This is a exmaple, isn't it?".replace('is', 'was')
"Thwas was a exmaple, wasn't it?"

>>> "This is a exmaple, isn't it?".replace('is', 'was', 2)
"Thwas was a exmaple, isn't it?"
```

**str.split([sep[,maxsplit]])**

对字符串使用指定分隔符按指定最大分隔次数分隔，或全部分隔，返回一个列表对象。
默认分隔符是空格，分隔符不能为空，如 ''
若指定了最大分隔次数 maxsplit，则元素个数 = maxsplit + 1
```python
>>> 'abc abc abc'.split()
['abc', 'abc', 'abc']

>>> 'abc abc abc'.split(' ')
['abc', 'abc', 'abc']

>>> 'abcabcabc'.split('c')
['ab', 'ab', 'ab', '']

>>> 'abcabcabc'.split('c',2)
['ab', 'ab', 'abc']

>>> 'abcabcabc'.split('c',1)
['ab', 'abcabc']

>>> 'abcabcabc'.split('c',100)
['ab', 'ab', 'ab', '']

>>> 'abcabcabc'.split('',100)
ValueError: empty separator
```

**str.splitlines(keepends=False)**

对字符串按换行符进行分隔，若 keepends 为 True，则换行符包含在返回的 List 中：
```python
>>> 'abc\ndef\nghi'.splitlines(True)
['abc\n', 'def\n', 'ghi']

>>> 'abc\ndef\nghi'.splitlines(1)
['abc\n', 'def\n', 'ghi']

>>> 'abc\ndef\nghi'.splitlines(False)
['abc', 'def', 'ghi']

>>> 'abc\ndef\nghi'.splitlines(0)
['abc', 'def', 'ghi']
```

**str.join(iterable)**

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

**len(string)**

返回容器内元素的数量：
```python
>>> len("Hello, world!")
13

>>> len('abc')
3
>>> len(('a','b','c'))
3
>>> len(['a','b','c'])
3
>>> len({'a','b','c'})
3
>>> len({'a':1,'b':2,'c':3})
3
```

**str.strip(chars=None), str.lstrip(chars=None), str.rstrip(chars=None)**

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

**str.expandtabs(tabsize=8)**

使用 tabsize 个空格替换 Tab 键，tabsize 的默认值是 8，返回的是字符串的副本，因字
符串对象不可修改：
```python
>>> print('a\tb\tc')
a       b       c

>>> print('a\tb\tc'.expandtabs(2))
a b c
>>> print('a\tb\tc'.expandtabs(3))
a  b  c
>>> print('a\tb\tc'.expandtabs(4))
a   b   c
>>> print('a\tb\tc'.expandtabs(8))
a       b       c
```

**str.partition(s)**

使用指定的分隔符 s 把字符串 str 分隔成 3 部分，并始终返回一个包含 3 个元素的元组
：分隔符前面的部分、分隔符本身和分隔符后面的部分。如果没找到分隔符，则返回的元组
包含原字符串和两个空元素。
```python
>>> 'ab12cd12ef12gh'.partition('12')
('ab', '12', 'cd12ef12gh')

>>> 'abcd'.partition('a')
('', 'a', 'bcd')
>>> 'abcd'.partition('b')
('a', 'b', 'cd')
>>> 'abcd'.partition('c')
('ab', 'c', 'd')
>>> 'abcd'.partition('d')
('abc', 'd', '')
>>> 'abcd'.partition('e')
('abcd', '', '')
```

**str.encode(encoding="utf-8", errors="strict")**

```python
>>> 'abc  我我我'.encode()
b'abc  \xe6\x88\x91\xe6\x88\x91\xe6\x88\x91'
```
