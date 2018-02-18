---
title: PHP - CodeIgniter 视图
categories:
  - PHP
  - CodeIgniter
tags:
  - CodeIgniter
---

一个视图其实就是一个 Web 页面，或者页面的一部分，像页头、页脚、侧边栏等。

<!--more-->

## 创建视图

在 `application/views/` 目录下建立视图文件，也可在其下建立子目录，再在子目录下

建立视图， 扩展名为 php

## 加载试图

视图不是直接被调用的，它必须通过 控制器 来加载。
```php
$this->load->view('view_name');
```
* view_name 为视图文件名，区分大小写
* 扩展名 .php 可以省略，除非使用了其他的扩展名。

子目录下的视图加载要包含子目录名称
```php
$this->load->view('directory_name/file_name');
```

## Unable to load the requested file

注意，加载视图时路径并不包括 views，例如

```php
public function common($page = 'home')
{
    if ( ! file_exists(APPPATH.'views/about/'.$page.'.php'))    // 这里的判断虽然有 views
    {
        // Whoops, we don't have a page for that!
        show_404();
    }

    $data['title'] = ucfirst($page); 

    $this->load->view('templates/header');
    $this->load->view('about/'.$page);                          // 但这里加载视图路径不需要 views
    $this->load->view('templates/footer');
}
```
如果写成这样
```php
$this->load->view('views/about/'.$page);
```
只会提示

    Unable to load the requested file: views/about/contact-us.php

不会提示 file not found

