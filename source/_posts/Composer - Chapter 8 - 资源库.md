title: Composer - Chapter 8 - 资源库
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---


    
<!--more-->

## 资源库类型

资源库有 4 种类型：composer, vcs, pear, package

### composer

composer 类型是资源库的主要类型，默认资源库 packagist.org 就是 composer 类型的资源库

#### packages.json

composer 类型资源库使用一个 `packages.json` 文件来包括包的元数据，

而引用 Composer 资源库时使用 `packages.json` 文件所在目录的地址即可，

如 packagist 的 `packages.json` 文件地址为 https://packagist.org/packages.json

则引用 packagist 使用地址：https://packagist.org/

https://packagist.org/packages.json
```
{
  "packages": [],
  "notify": "/downloads/%package%",
  "notify-batch": "/downloads/",
  "search": "/search.json?q=%query%&type=%type%",
  "providers-url": "/p/%package%$%hash%.json",
  "provider-includes": {
    "p/provider-2013$%hash%.json": {
      "sha256": "3bfa7d41fff0ccb5d41613a4f7eda39cfb34829c4348459a60b843591c879398"
    },
    "p/provider-2014$%hash%.json": {
      "sha256": "b767368f88248a7d1ee5a58f543cea15a5a680ca950c8a43f32bc2d3efd02d52"
    },
    "p/provider-2015$%hash%.json": {
      "sha256": "f360883f238d4b4364c5340ba41ad9bec8c36dae8053e1d876d7721590c46821"
    },
    "p/provider-2016$%hash%.json": {
      "sha256": "522db3c8e06ed2ebeaa6879acd47c394296e93f7533ff975f586e5699bc790d3"
    },
    "p/provider-2016-10$%hash%.json": {
      "sha256": "97b9892040374a279779e941d343b2295d5fc3da02860dddaddd6295f6bab365"
    },
    "p/provider-2017-01$%hash%.json": {
      "sha256": "20088f8505c185b66b0f439d90f1e948db85a350f79bac4311299a030a6ea742"
    },
    "p/provider-2017-04$%hash%.json": {
      "sha256": "5d92cd0a963500f6f7b08b0819e1ae720b9fb9bfe5e3b224355297ffd883f9c7"
    },
    "p/provider-2017-07$%hash%.json": {
      "sha256": "7fb643b9978ea69980f678da197ab15c1ce4f5dc561008d7e6ae439413d6f026"
    },
    "p/provider-archived$%hash%.json": {
      "sha256": "8b9dacfc128a4639496a9928de0698e397730884ebe32fc1c0b89f848e423341"
    },
    "p/provider-latest$%hash%.json": {
      "sha256": "1e3c2a42c3faf518d1ac452c69c347f83b91612fcdc8a3f310fc00f6afe9f196"
    }
  }
}
```
* `provider-includes` 

https://packagist.org/p/provider-2013$3bfa7d41fff0ccb5d41613a4f7eda39cfb34829c4348459a60b843591c879398.json
```
{
  "providers": {
    "0s1r1s/dev-shortcuts-bundle": {
      "sha256": "6c7710a1ca26d3c0f9dfc4c34bc3d6e71ed88d8783847ed82079601401e29b18"
    },
    "11ya/excelbundle": {
      "sha256": "65dccb7f2d57c09c19519c1b3cdf7cbace1dfbf46f43736c2afcb95658d9c0f1"
    },

    ............

    "zz/zz": {
      "sha256": "7fd780b8e8995f01adfc0b178202df43f85381ca8915d43ce950c5f24659f8ae"
    },
    "zzal/cakephp-hash": {
      "sha256": "e9ddb1a3e412f5ebfc88c2ad32a97c3956c176ae4e654390294dcf83ae45446b"
    }
  }
}
```

https://packagist.org/p/zz/zz$7fd780b8e8995f01adfc0b178202df43f85381ca8915d43ce950c5f24659f8ae.json
```
{
  "packages": {
    "zz/zz": {
      "1.0.0": {
        "name": "zz/zz",
        "description": "library for japanese",
        "keywords": [
          "japanese library"
        ],
        "homepage": "https://github.com/zaininnari/zz",
        "version": "1.0.0",
        "version_normalized": "1.0.0.0",
        "license": [
          "MIT"
        ],
        "authors": [
          {
            "name": "zaininnari",
            "email": "zaininnari@gmail.com",
            "homepage": "http://www.zay.jp"
          }
        ],
        "source": {
          "type": "git",
          "url": "https://github.com/zaininnari/zz.git",
          "reference": "945abeaa5c310bfc69e49d41bdcec742859ccde2"
        },
        "dist": {
          "type": "zip",
          "url": "https://api.github.com/repos/zaininnari/zz/zipball/945abeaa5c310bfc69e49d41bdcec742859ccde2",
          "reference": "945abeaa5c310bfc69e49d41bdcec742859ccde2",
          "shasum": ""
        },
        "type": "library",
        "time": "2012-08-14T16:27:25+00:00",
        "autoload": {
          "psr-0": {
            "zz": "src/"
          }
        },
        "require": {
          "php": ">=5.3.0"
        },
        "uid": 16980
      },
      "dev-develop": {
        ........
      },
      "dev-master": {
        ........
      },
      "dev-travis": {
        ........{
    "packages": {
        "vendor/package-name": {
            "dev-master": { @composer.json },
            "1.0.x-dev": { @composer.json },
            "0.0.1": { @composer.json },
            "1.0.0": { @composer.json }
        }
    }
}
      }
    }
  }
}
```

上面的格式概括：
```
{
    "packages": {
        "vendor/package-name": {
            "dev-master": { @composer.json },
            "1.0.x-dev": { @composer.json },
            "0.0.1": { @composer.json },
            "1.0.0": { @composer.json }
        }
    }
}
```

### vcs

### pear

### package

## 创建自己的资源库

## 禁用 Packagist

通过命令全局禁用
```
$ composer config -g repo.packagist false
```

通过配置文件禁用
```
{
    "repositories": [
        {
            "packagist.org": false
        }
    ]
}
```
