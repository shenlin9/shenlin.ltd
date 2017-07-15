# php composer command

标签（空格分隔）： AAA php composer command

---

### composer show [option] [vendor/package]

默认只显示本地已安装包

不加 --all 选项，则必须在 composer.json
所在目录执行，因为这时只显示本地已安装包的信息，要读取 composer.json 文件

加上 --all 选项，则先查找当前目录下有无 composer.json 文件，
有则使用 composer.json 文件里定义的 repository 去联网查找包信息
没有 composer.json 文件则从默认注册的 packagist 站点联网查找包信息。

```
composer show -h
```
显示帮助


```
composer show
```
列出已安装的包


```
composer show --all
```
联网查找列出所有可安装包


```
composer show monolog/monologo
```
列出本地已安装的monolog包的信息，版本号只显示当前版本


```
composer show --all monolog/monolog
```
会联网到 repository 去查找
输出结果和不加选项 --all 的唯一不同之处是版本号会显示所有的版本

---

### composer create-project [vendor/package] [install path] [which version]

```
composer create-project laravel/laravel
```
默认创建 vendor/package 中的 package 文件夹

```
composer create-project slim/slim slim-2.6 2.6.2
```
指定安装文件夹 和 要安装的版本号

---

### require-dev 属性和 --no-dev选项

```
"require-dev": {
    "fzaninotto/faker": "~1.4",
    "mockery/mockery": "0.9.*",
    "phpunit/phpunit": "~5.7"
}
```

```
$ composer install --no-dev
```

一些儿开发中需要用到的包，可能在生产环境中无需部署，例如调试包，这些儿包可以在 `composer.json` 文件中用属性 `require-dev` 指明，在实际部署到生产环境中时，配合`--no-dev`指明不部署这些儿包即可。

