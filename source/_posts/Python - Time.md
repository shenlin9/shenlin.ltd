---
title: Python - Time
categories:
- Python
tags:
- Python
- Time
date: 2019-05-25 15:39:08
---

Python - Time

<!--more-->

## Time & DateTime

Python 中的 time 和 datetime 都是对象，不是纯字符串，也不是一个时间戳。因此要处
理它们时，需要导入对应的模块或模块中的类。

此外，在使用 time 和 datetime 类编程时，可能还需要设置适当的时区。

每个计算机系统都有一个预先编程的时钟，用于特定的日期、时间和时区。Python 时间模
块提供了以多种方式读取、表示和重置时间信息的能力。

## Essential facts about time module

* time 模块封装了 C 运行时库，它有一组函数，您可以调用这些函数来进行时间操作。
* time 模块遵循指定了时间开始点的 EPOCH 约定。它支持的日期和时间从Unix纪元元年到
  2038 年。
* Unix 纪元(EPOCH) 是 1970-01-01 00:00:00。
* 术语 “Seconds since the Epoch”或 “No. of Ticks since epoch” 表示的是自
  Unix 纪元经过的时间(以秒为单位)
* 一些时间函数以 DST 格式返回时间。DST是夏令时(Daylight Saving Time)。这是一种在
  夏天把时钟拨快 1 小时，然后在秋天再拨回来的装置。
* What is a tick in Python? A tick refers to a time interval which is a
  floating-point number measured as units of seconds.

使用以下代码确定系统上的 EPOCH 值：
```python
>>> import time
>>> time.gmtime(0)
time.struct_time(tm_year=1970, tm_mon=1, tm_mday=1, tm_hour=0, tm_min=0,
tm_sec=0, tm_wday=3, tm_yday=1, tm_isdst=0)
```
* 返回的是 `time.struct_time` 对象

## time functions

Python 中最常用的时间模块的函数和数据结构：
* time.time()
* time.clock()
* time.ctime()
* time.sleep()
* time.struct_time class
* time.strftime()

### time.time()

time() 是 time 模块的核心函数，它以浮点数返回自 Unix 纪元开始至今的秒数：
```python
>>> import time
>>> time.time()
1558834933.1059873
>>> time.time()
1558834933.2665493
>>> time.time()
1558834933.4081695
```
可以用于存储或比较日期，但不适合用于报告显示。

还可以用于计算完成一个任务所用时间：
```python
import time
start = time.time()
print('program is doing something....')
time.sleep(0.3)
end = time.time()
print('done')
print('elapsed time:', end - start)

# output
program is doing something....
done
elapsed time: 0.3001973628997803
```

### time.clock()

time.clock() 返回处理器时钟时间，可以用来进行性能测试和基准测试，
```python
>>> time.clock()
5362.054958
>>> time.clock()
5362.796411
>>> time.clock()
5363.5485967
```
time.clock() 会受到您使用的操作系统的影响：它可以返回忽略了睡眠时间的进程时间，
在某些情况下，即使进程处于非活动状态，它也可以计算经过的时间。
```python
import time

template = 'time(): {:0.2f}, clock(): {:0.2f}'

print(template.format(time.time(), time.clock()))

for i in range(3, 0, -1):
    print('>Sleeping for', i, 'sec.')
    time.sleep(i)
    print(template.format(time.time(), time.clock()))

# output
time(): 1558858639.88, clock(): 0.03
>Sleeping for 3 sec.
time(): 1558858642.88, clock(): 3.03
>Sleeping for 2 sec.
time(): 1558858644.88, clock(): 5.03
>Sleeping for 1 sec.
time(): 1558858645.89, clock(): 6.03
```

注意:如果程序处于休眠状态，不会计入处理器时钟。

time.clock() 在 Python 3.3 中已被弃用，将在 Python 3.8 中移除。

推荐使用 time.perf_counter 或 time.process_time 代替。

perf_counter 表示 performance counter for benchmarking，它使用精度最高的时钟来测
量睡眠过程中的短时间和因素。(It uses a clock with the highest precision to
measure a short duration and factors in the process sleep time.)
```python
>>> time.perf_counter()
5321.6535311
>>> time.perf_counter()
5323.4404745
>>> time.perf_counter()
5324.6274943
```

time.process_time 输出当前进程的系统CPU时间和用户CPU时间的总和。它忽略了睡眠时间。
```python
>>> time.process_time()
0.21875
>>> time.process_time()
0.21875
>>> time.process_time()
0.21875
```

### time.ctime()

```python
time.ctime()
time.ctime(seconds)
```
没有参数时，它调用 `time()` 函数并返回当前时间。
有参数时，参数是“从纪元开始的秒数”，输出结果为根据本地时间转换为的人类可读的字
符串值，长度是 24 个字符。

例如
```python
>>> time.ctime()
'Mon May 27 13:47:01 2019'

>>> time.ctime(time.time() + 10)
'Mon May 27 13:47:11 2019'
```

### time.sleep()

```python
time.sleep(seconds)
```
将当前线程的执行暂停指定的秒数。

参数可以是浮点数，以注册更精确的睡眠时间。

然而因为任何捕获的信号将结束睡眠，程序可能不会很精确的暂停指定的时间。此外，由于
系统中其他活动的调度，睡眠时间还可能会延长。

当需要等待一个文件完成关闭操作或让一个数据库提交时，可以用到此函数。

注意:在 Python 3.5 中，`sleep()` 函数将继续执行，而不考虑信号引起的中断。但是，
如果信号处理程序抛出异常，则中断。

### time.struct_time class

```python
time.struct_time
```
`struct_time` 是 time 模块中唯一的数据结构。

它有一个命名的元组接口，可以通过索引或属性名访问。

当您需要访问日期的特定字段(年、月等)时，这个类非常有用。

有许多函数(如localtime()、gmtime())返回 struct_time 对象。

```python
>>> st = time.localtime()
>>> st.tm_hour
14
>>> st.tm_min
49
>>> st.tm_sec
58
>>> st.tm_year
2019
>>> st.tm_mon
5
>>> st.tm_mday
27
>>> st.tm_yday
147
>>> st.tm_wday
0
>>> st.tm_zone
'?D1¨²¡À¨º¡Á?¨º¡À??'
>>> st.tm_isdst
0
>>> st.tm_gmtoff
28800
```

### time.strftime()

```python
time.strftime(formatString[, time])
```
这个函数在第二个参数中接受 tuple 或 struct_time，并按照第一个参数中指定的格式转
换为字符串。如果第二个参数不存在，那么它调用 `localtime()` 来使用当前日期。

```python
>>> now = time.localtime()
>>> time.strftime("%Y-%m-%d %H:%M:%S", now)
'2019-05-27 15:02:21'
>>> time.strftime("%Y-%m-%d %H:%M:%S")
'2019-05-27 15:04:00'
```

## timezones

时区是一个常与时间连用的术语。世界上有24个时区。
```python
>>>
```
* 

### UTC, GMT, IST

UTC是 Universal Time Coordinated 协调世界时的缩写。它是一个时间标准，不是时区。

GMT是指格林尼治标准时间，IST是一个用来表示印度标准时间的术语。两者都是时区。

IST 适用于印度，而 GMT/UTC 适用于世界。

注:格林尼治时间与标准时间之比为+5.30小时。

### check timezone

timezone    它以 UTC 格式返回本地(非 DST)时区的偏移量。
tzname      返回一个包含本地非 DST 和 DST 时区的元组。
```python
>>> time.timezone
-28800

>>> time.tzname
('ÖÐ¹ú±ê×¼Ê±¼ä', 'ÖÐ¹úÏÄÁîÊ±')

>>> ','.join(time.tzname).encode('latin-1').decode('gbk')
'中国标准时间,中国夏令时'
```

### set timezone

time.tzset() 函数——它为所有库函数重置时区，并使用环境变量TZ设置新值。它的限制是
只能在基于Unix的平台上工作。
```python
import os, time

print('Current timezone is', time.tzname)
os.environ['TZ'] = 'Europe/London'
time.tzset()
print('New timezone is', time.tzname)
```

## DateTime

这个模块及其函数对于用时间戳值记录事件和日志非常方便。

虽然我们可以使用 time 模块来找出当前的日期和时间，但是它缺乏为特定操作创建日期对
象等功能。

为了解决这个问题，Python有内置的datetime模块。因此，要对日期和时间进行任何对象操
作，都需要导入datetime模块。

datetime 模块用于以各种方式修改日期和时间对象。它包含五个类来操作日期和时间：
* Date: 处理日期对象
* Time: 处理时间对象
* datetime: 处理日期和时间对象组合。
* timedelta: 处理时间间隔。用于计算过去和将来的日期和时间对象。
* Info: 处理本地时间的时区信息

导入模块或个别类：
```python
import datetime

from datetime import date
from datetime import time
from datetime import datetime

import datetime
datetime.datetime(year_number, month_number, date_number, hours_number,
minutes_number, seconds_number)
```

我们可以创建具有不同日期和时间的新对象，以便使用 datetime 模块进行操作。
```python
import datetime
datetime.datetime(year_number, month_number, date_number, hours_number, minutes_number, seconds_number)
```

timedelta() 函数的作用是：允许对日期和时间对象执行计算。它可以用于时间规划和管理。
调用 `timedelta()` 时使用的语法如下:
```python
from datetime import timedelta
timedelta(days, hours, minutes, seconds, year,...)
```

## 应用举例

今天日期：
```python
import datetime
print (datetime.date.today())
# 或
from datetime import date
print (date.today())
```

日期独立部件：
```python
from datetime import date
print (datetime.date.today().day)
print (datetime.date.today().month)
print (datetime.date.today().year)
# 或
from datetime import date
now = date.today()
print (now.day)
print (now.month)
print (now.year)
# 或
from datetime import date
now = date.today()
print (now.day, now.month, now.year)
```

星期：
```python
from datetime import date
now = date.today()
print (now.weekday())
```

为了将日期格式化为人类可读的字符串，我们在 datetime 模块中使用 strftime()。
```python
Today = datetime.now()
print (Today.strftime(“%a, %B, %d, %y”))
```
* %a stands for the day
* %B stands for the month
* %d stands for date
* %y stands for the year.

使用 strftime 同时显示本地日期和时间：
```python
import datetime
Today = datetime.now()
print (“Today’s date is “ ,Today.strftime(“%c”))
```
* %c 替换为 %x 则只显示本地日期
* %c 替换为 %X 则只显示本地时间

timedelta
```python
from datetime import date,time,datetime,timedelta
Time_gap = timedelta(hours = 23, minutes = 34)
print (“Future time is ”, str(datetime.now() + Time_gap))
```
