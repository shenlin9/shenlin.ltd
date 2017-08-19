title: Composer - Chapter B - HTTP 基本身份认证

categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

有 3 种进行 HTTP 基本身份认证的方法。

<!--more-->

## 通过 composer.json 提供认证信息

在资源库的 URL 里加上认证信息
```
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://extremely:secret@repo.example.org"
        }
    ]
}
```
* 用户名和密码采用明码的方式写死在 composer.json 里，不安全
* 团队合作时使用的都是同一个身份访问资源库

## 通过 auth.json 提供认证信息

auth.json 需要放在 COMPOSER_HOME 目录或 composer.json 所在的目录，格式如下：
```
{
    "http-basic": {
        "repo.example1.org": {
            "username": "my-username1",
            "password": "my-secret-password1"
        },
        "repo.example2.org": {
            "username": "my-username2",
            "password": "my-secret-password2"
        }
    }
}
```
* 最大的好处是可以加入 `.gitignore` 文件，这样每个团队成员可以拥有自己的身份认证文件

## 互动式认证

访问一个需要身份认证的资源库时，不提供任何身份认证信息，则 composer 会自动询问你的用户名和密码

每次访问资源库时都需要输入稍微麻烦
