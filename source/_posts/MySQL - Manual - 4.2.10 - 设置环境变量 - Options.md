---
title: MySQL - Manual - 4.2.10 - 设置环境变量 - Options
categories: 
 - MySQL
 - Manual
tags: 
 - MySQL
---

设置环境变量

<!--more-->

Environment variables can be set at the command prompt to affect the current
invocation of your command processor, or set permanently to affect future
invocations. 

To set a variable permanently, you can set it in a startup file or by using the interface provided by your system for this purpose.

Consult the documentation for your command interpreter for specific details.

Section 4.9, “MySQL Program Environment Variables”, lists all environment variables that affect MySQL program operation.

```sql
SET USER=your_name
```

```bash
MYSQL_TCP_PORT=3306
export MYSQL_TCP_PORT
```

```bash
setenv MYSQL_TCP_PORT 3306
```

```bash
PATH=${PATH}:/usr/local/mysql/bin
```

```bash
setenv PATH ${PATH}:/usr/local/mysql/bin
```
