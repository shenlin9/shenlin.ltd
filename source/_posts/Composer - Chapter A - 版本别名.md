title: Composer - Chapter A - 版本别名

categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

Composer 根据 VCS 的 branch 生成版本号时，会分为两类，一类是数字形式的可比较版本号如1.2, 1.3等，另一类是非数字形式的不可比较的版本号如 master 会生成 dev-master，bugfix 生成 dev-bugfix。

如果使用 master 分支产生 tag，如 1.0.1, 1.0.2, 1.0.3，而版本约束又是 `1.0.*` ，则要想获取最新版本的 dev-master 时，会因为和约束冲突而无法获取。

这时可以通过给分支设置别名来解决约束冲突的问题。

<!--more-->

## 通过 branch-alias 设置别名

在 extra 下增加 branch-alias 给 dev-master 设置别名 1.0.x-dev：
```
{
    "extra": {
        "branch-alias": {
            "dev-master": "1.0.x-dev"
        }
    }
}
```
* 不可比较的版本设置别名时，前面需要加 `dev-`
* 可比较的版本设置别名时， `.x-dev` 结束的版本号还可以再设置别名，但版本约束必须更严格，如 dev-1.x-dev 可以设置别名 dev-1.2.x-dev

## 通过 require,require-dev 设置行内别名

上述的方式实现别名要求必须对资源包对应的版本库有提交权限，对于没有权限的第三方版本库可以使用行内别名来实现

```
{
    "require": {
        "monolog/monolog": "dev-master as 1.0.x-dev"
    }
}
```

行内别名应该避免使用，特别是对于发行包。

行内别名的应该是用于临时修复错误，即发行一个bug，在分支上修复它，然后使用别名合并最新的修复
