---
title: Basis - 并发, 吞吐量, TPS, QPS, RPS, 响应时间 RT
categories:
  - Basis
tags:
  - Basis
---

TPS

<!--more-->

吞吐量

    Throughtput

    指 1 秒内系统可以处理的请求的数量，说明了系统快速处理请求的速度

并发数

    concurrently connection 

    在有限的时间内（不一定是固定时间），系统可以对多少个请求连接返回一个响应，

    即大量的并发连接需要的是高效的连接调度，高并发并不意味是一个快速的系统。

TPS

    Transactions Per Second 每秒事务数

    指的是由某个实体每秒执行的原子操作的数量，

    通常用于数据库系统，指每秒执行的数据库事务数量。

QPS

    Queries Per Second 每秒查询数

    常用语信息检索系统，如搜索引擎或数据库

    这个术语被广泛地使用在"请求-响应"一类系统钟，更准确地名称为 RPS (requests per second 每秒请求数)

RT

    Response Time 响应时间

    系统对请求作出响应的时间
