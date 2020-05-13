---
title: Interest-批量删除微博-weibo
categories:
- Interest
tags:
- Interest
- weibo
date: 2020-05-03 16:46:00
---

批量删除微博

<!--more-->

Chrome 内核浏览器，登录微博帐号，按F12打开浏览器控制台，选择Console窗口，粘贴如下代码，直接回车运行：

```Javascript
// ==UserScript==

// @name Weibored.js

// @namespace http://vito.sdf.org

// @version 0.2.0

// @description 删除所有微博

// @author Vito Van

// @match http://weibo.com/p/*

// @grant none

// ==/UserScript==

'use strict';

var s = document.createElement('script');

s.setAttribute(

'src',

'https://lib.sinaapp.com/js/jquery/2.0.3/jquery-2.0.3.min.js'

);

s.onload = function() {

setInterval(function() {

if (!$('a[action-type="feed_list_delete"]')) {

$('a.next').click();

} else {

$('a[action-type="feed_list_delete"]')[0].click();

$('a[action-type="ok"]')[0].click();

}

// scroll bottom let auto load

$('html, body').animate({ scrollTop: $(document).height() }, 'slow');

}, 800);

};

document.head.appendChild(s);
```
