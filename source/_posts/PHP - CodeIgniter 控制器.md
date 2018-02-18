---
title: PHP - CodeIgniter 控制器
categories:
  - PHP
  - CodeIgniter
tags:
  - CodeIgniter
---

一个控制器就是一个类文件，是以一种能够和 URI 关联在一起的方式来命名的

<!--more-->

## 名称

当控制器的名称和 URI 的第一段匹配上时，它将会被加载。

    example.com/index.php/blog/

控制器的文件名必须是大写字母开头，即上面的控制器文件应该是 `Blog.php`

控制器的类名也必须以大写字母开头，且继承了父控制器类，这样它才能使用父类的方法

```php
class Blog extends CI_Controller {

}
```

## 默认控制器

当 URI 没有分段时将加载默认控制器，在文件 `application/config/routes.php` 中设置

```php
$route['default_controller'] = 'blog';
```

## 子目录

控制器文件也可放入子目录，即在 `application/controllers/` 下建立子目录，这时 URI 的

第一段必须指定目录，如控制器目录如下：

    application/controllers/products/Shoes.php

则 URI 应该像下面这样:

    example.com/index.php/products/shoes/show/123

每个子目录都应该包含有一个和 `$route['default_controller'` 设置匹配的默认控制器文件

因为会有这样的 URI:

    example.com/index.php/products/

## 方法

URI 中的第二段用于决定调用控制器中的哪个方法，为空时则总是调用 "index" 方法。

被声明为 private 或 protected 的方法是不能被 URL 访问到的。

为了向后兼容原有的功能，在方法名前加上一个下划线前缀也可以让方法无法访问。

如果 URI 多于两个段，则多余的段将作为参数传递到你的方法中

例如 ：example.com/index.php/products/shoes/sandals/123

```php
class Products extends CI_Controller {

    public function shoes($sandals, $id)
    {
        echo $sandals;
        echo $id;
    }
}
```
* 如果使用了 URI 路由，传递到方法的参数将是路由后的参数。 ???

如果要改变这种使用 URI 第二段来决定调用哪个方法的规则 CodeIgniter 也是允许的，

可以使用 `_remap()` 方法来重写该规则 : 

    如果控制包含一个 `_remap()` 方法， 那么无论 URI 中包含什么参数时都会调用该方法。 

它允许定义自己的路由规则，重写默认的这种行为。

被重写的方法（通常是 URI 的第二段）将被作为参数传递到 `_remap()` 方法:

```php
public function _remap($method)
{
    if ($method === 'some_method')
    {
        $this->$method();
    }
    else
    {
        $this->default_method();
    }
}
```

方法名之后的所有其他段将作为 `_remap()` 方法的第二个参数，它是可选的。

这个参数可以使用 PHP 的 `call_user_func_array()` 函数来模拟 CodeIgniter 的默认行为。

```php
public function _remap($method, $params = array())
{
    $method = 'process_'.$method;
    if (method_exists($this, $method))
    {
        return call_user_func_array(array($this, $method), $params);
    }
    show_404();
}
```

## 构造函数

在控制器构造函数中必须调用父类的构造函数
```
class Blog extends CI_Controller {

    public function __construct()
    {
        parent::__construct();
        // Your own constructor code
    }
}
```

## 保留名称

因为你的控制器类继承了 CI 的控制器，所以你的方法命名一定不能和 CI 控制器类中的

方法名相同，否则你的方法将会覆盖他们。 已经保留的名称有：

控制器：

    CI_Controller
    Default
    index

函数

    is_php()
    is_really_writable()
    load_class()
    is_loaded()
    get_config()
    config_item()
    show_error()
    show_404()
    log_message()
    set_status_header()
    get_mimes()
    html_escape()
    remove_invisible_characters()
    is_https()
    function_usable()
    get_instance()
    _error_handler()
    _exception_handler()
    _stringify_attributes()

变量

    $config
    $db
    $lang

常量

    ENVIRONMENT
    FCPATH
    SELF
    BASEPATH
    APPPATH
    VIEWPATH
    CI_VERSION
    MB_ENABLED
    ICONV_ENABLED
    UTF8_ENABLED
    FILE_READ_MODE
    FILE_WRITE_MODE
    DIR_READ_MODE
    DIR_WRITE_MODE
    FOPEN_READ
    FOPEN_READ_WRITE
    FOPEN_WRITE_CREATE_DESTRUCTIVE
    FOPEN_READ_WRITE_CREATE_DESTRUCTIVE
    FOPEN_WRITE_CREATE
    FOPEN_READ_WRITE_CREATE
    FOPEN_WRITE_CREATE_STRICT
    FOPEN_READ_WRITE_CREATE_STRICT
    SHOW_DEBUG_BACKTRACE
    EXIT_SUCCESS
    EXIT_ERROR
    EXIT_CONFIG
    EXIT_UNKNOWN_FILE
    EXIT_UNKNOWN_CLASS
    EXIT_UNKNOWN_METHOD
    EXIT_USER_INPUT
    EXIT_DATABASE
    EXIT__AUTO_MIN
    EXIT__AUTO_MAX


