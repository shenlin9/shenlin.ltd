---
title: Python - Class and OOP
categories:
- Python
tags:
- Python
- Class
- OOP
date: 2019-05-15 14:42:09
---

Python - Class and OOP

<!--more-->

## Python and OOP

Python 支持面向对象编程(OOP)。

OOP是一种开发模型，它让程序员专注于生成可重用代码。它不同于遵循顺序方法的过程模型。

当您要处理一个大型且复杂的项目时，OOP非常有用。将有多个程序员创建可重用的代码，
共享和集成他们的源代码。可重用性带来更好的可读性，并在长期内减少维护。

## Class

类是将变量和函数排列成一个逻辑实体。

Python 有一个名为 class 的保留关键字，您可以使用它来定义一个新类。

类作为创建对象的模板。对象是在运行时创建的类的工作实例。每个对象都可以使用类变量
和函数作为其成员。

## The “class” keyword

使用 class 关键字定义类，注意是小写：
```python
class Class_Name:
    Class_Body
```

## The “self” keyword

`self` 关键字表示类的实例，可以从类的方法中访问类成员(如类的属性)。

另外，请注意，它隐式地是每个 Python 类中 `__init__` 方法的第一个参数。

## The “__init__” method

`__init__` 是与每个 Python 类关联的唯一方法。

当通过类创建的对象，Python 将内存分配给一个新的类对象时，会自动调用此方法。

此方法的作用是用用户提供的值初始化类属性。

在面向对象编程中通常称为构造函数。

```python
>>> class Dogs:
...     def __init__(self):
...             print('constructor get called')
...
>>> d = Dogs()
constructor get called
```

`__init__` 方法的目的是为了初始化对象属性，不要滥用，此方法不能返回值，只能返回
None：
```python
>>> class Dogs:
...     def __init__(self, name):
...             self.name = name
...             return name
...
>>> d = Dogs('wangwang')
TypeError: __init__() should return None, not 'str'

>>> class Cats:
...     def __init__(self, color):
...             self.color = color
...             return None
>>> c = Cats('white')
```

## 指定程序的入口

```python
def main():
    print('hello')

if __name__ == '__main__':
    main()
```

## 继承和子类调用父类的构造函数

```python
class Animal:
    def __init__(self, name):
        this.name = name

class Cat(Animal):
    def __init__(self, name, color):
        super().__init__(name)
```

## 抽象方法

Python 中包含了一个或多个抽象方法的类就是抽象类，抽象方法就是只对方法进行了定义
但没有对方法进行实现的方法，继承了抽象类的子类必须实现抽象方法，否则就出错，可以
使用这种方式强制子类必须实现某些方法。

Python 中使用 ABCMeta 和 abstractmethod 关键字分别定义抽象类和抽象方法
```python
from abc import ABCMeta, abstractmethod

class Animal(metaclass = ABCMeta):

	def  __init__(self, name = 'unknown'):
		self .visitorName = name

	def welcome(self):
		print('Hello %s, Welcome to Animals Park' % self.visitorName)

	@abstractmethod
	def scarySound(self):
		pass

class Dogs(Animal):
	def scarySound(self):
		print('wangwangwang...')

>>> d = Dogs()
>>> d.scarySound()
wangwangwang...
```

## 私有属性

Python 中没有私有属性的概念，所有的属性和方法都可以被最终用户访问到，但有个约定
：
* 如果属性或方法以一个下划线开头，则用户不应该去直接访问它，而是通过类提供的其他
  方法来访问此属性或方法。
* 如果属性以两个下划线开头，如 `__attributeName`，则会被自动命名为
  `_className__attributeName`，这是为了避免不同类中相同属性名导致的冲突，在继承
  时，若父类和子类有相同的属性名时则更有用。

隐藏属性的类内类外访问：
```python
>>> class Jungle:
	def __init__(self):
		self._name = 'shen'
		self.__color = 'blue'
	def test(self):
		print(self._name)
		print(self.__color)

>>> j = Jungle()
>>> j._name
'shen'
>>> j.__color
AttributeError: 'Jungle' object has no attribute '__color'
>>> j._Jungle__color
'blue'
>>> j.test()
shen
blue
```

## 类属性

注意类属性在类内是通过 `类名.类属性` 的方式调用，不使用 self 关键字，因为访问类
属性时不一定创建了对象实例：
```python
>>> class Dogs:
	name = 'DaHuang'
	_ID = 123
	__Task = 'Catch Bad Guy'
	def __init__(self):
		print(Dogs.name, Dogs._ID, Dogs._Dogs__Task)

>>> d = Dogs()
DaHuang 123 Catch Bad Guy

>>> print(d.name, d._ID, d._Dogs__Task)
DaHuang 123 Catch Bad Guy

>>> print(Dogs.name, Dogs._ID, Dogs._Dogs__Task)
DaHuang 123 Catch Bad Guy
```

## The instance attributes

特定于对象的属性被定义为 `__init__` 方法的参数，每个对象可以有自己不同的值：
```python
>>> class Dogs:
...     def __init__(self,name,color):
...             self.name = name
...             self.color = color
...
>>> d = Dogs('wangwang', 'black')
>>> e = Dogs('aoaoao', 'white')
```

## The class attributes

与对象级可见的实例属性不同，类属性对于所有对象都是相同的，可以直接使用类名访问：
```python
>>> class Dogs:
...     legs = 4
...     number = 0
...
...     def __init__(this, name, color):
...             this.name = name
...             this.color = color
...             Dogs.number += 1
...
>>> d = Dogs('aoaoao', 'white')
>>> e = Dogs('wangwang', 'black')
>>> Dogs.number
2
```

## 类的特殊方法

`__init__` 和 `__del__` 分别在创建对象和释放对象时调用：
```python
>>> class Dogs:
	Total = 0
	def __init__(self):
		Dogs.Total += 1
		print("Add dog number to", Dogs.Total)
	def __del__(self):
		Dogs.Total -= 1
		print("Sub dog number to", Dogs.Total)

>>> Dogs.Total
0

>>> d = Dogs()
Add dog number to 1
>>> e = Dogs()
Add dog number to 2
>>> f = Dogs()
Add dog number to 3
>>> del d
Sub dog number to 2
>>> del e
Sub dog number to 1
>>> del f
Sub dog number to 0
```

在输出对象实例时默认是显示对象的类型和内存地址，`__str__` 方法可以更改其显示内容：
```python
>>> d = Dogs()
>>> print(d)
<__main__.Dogs object at 0x000001F5080CDC88>

>>> class Cats:
	def __str__(self):
		return 'this class is Cat'

>>> c = Cats()
>>> print(c)
this class is Cat
>>> print(Cats)
<class '__main__.Cats'>
```

`__call__` 当对象实例被当作函数调用时，调用此方法，无此方法则按函数调用对象会出
错：
```python
>>> d()
TypeError: 'Cats' object is not callable

>>> class Cats:
	def __call__(self, name):
		print('you call me and input:', name)

>>> c = Cats()
>>> c('lin')
you call me and input: lin
```

`__doc__` 为显示类的文档，`__dict__` 显示类的信息，包含了类的文档：
```python
>>> class Jungle:
	'''List of animal and pet information
	   animal = string
	   isPet = string
	'''
	def __init__(self, animal = 'Elephant', isPet = "yes"):
		self.animal = animal
		self.isPet = isPet

>>> Jungle.__doc__
'List of animal and pet information\n\t   animal = string\n\t   isPet = string\n\t'

>>> Jungle.__dict__
mappingproxy({'__module__': '__main__', '__doc__': 'List of animal and pet information\n\t   animal = string\n\t   isPet = string\n\t', '__init__': <function Jungle.__init__ at 0x0000018E25E5C268>, '__dict__': <attribute '__dict__' of 'Jungle' objects>, '__weakref__': <attribute '__weakref__' of 'Jungle' objects>})

>>> j = Jungle()
>>> j.__doc__
'List of animal and pet information\n\t   animal = string\n\t   isPet = string\n\t'
>>> j.__dict__
{'animal': 'Elephant', 'isPet': 'yes'}
```

`__setattr__` 用于为属性赋值时调用，可以用来检查赋值的合法性，注意是所有属性赋值
时都调用此方法。`__getattr__` 用于访问不存在的属性时调用，即不存在于 `__dict__`
里：
```python
>>> class Student:
	def __init__(self, id, name, age):
		self.id = id
		self.name = name
		self.age = age
	def __setattr__(self, name, value):
		if name == 'id':
			if isinstance(value, int) and value > 0:
				self.__dict__['id'] = value
			else:
				raise TypeError('id must positive integer')
		elif name == 'name':
			self.__dict__['name'] = value
		else:
			self.__dict__[name] = value
	def __getattr__(self, name):
		raise AttributeError('Attribute'  + name +  'does not exists')

>>> s = Student(1, 'shen', 30)

>>> s.height = 18
>>> s.height
18

>>> s.weight
Traceback (most recent call last):
  File "<pyshell#33>", line 1, in <module>
    s.weight
  File "<pyshell#28>", line 17, in __getattr__
    raise AttributeError('Attribute'  + name +  'does not exists')
AttributeError: Attributeweightdoes not exists

>>> s.id = -1
Traceback (most recent call last):
  File "<pyshell#34>", line 1, in <module>
    s.id = -1
  File "<pyshell#28>", line 11, in __setattr__
    raise TypeError('id must positive integer')
TypeError: id must positive integer

>>> s.id = 1
```
