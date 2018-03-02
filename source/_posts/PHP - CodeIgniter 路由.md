---
title: PHP - CodeIgniter 路由
categories:
  - PHP
  - CodeIgniter
tags:
  - CodeIgniter
---

CodeIgniter

<!--more-->

默认路由规则

    http://example.com/[controller-class]/[controller-method]/[arguments]

    http://localhost/index.php/pages/view/home

CodeIgniter 从上到下读取路由规则并将请求映射到第一个匹配的规则，每一个规则都是 一个正则表达式（左侧）映射到 一个控制器和方法（右侧）

    $route['default_controller'] = 'pages/view';
    $route['(:any)'] = 'pages/view/$1';             # 匹配所有请求，并把参数 $1 传递给 page 类的 view 方法
