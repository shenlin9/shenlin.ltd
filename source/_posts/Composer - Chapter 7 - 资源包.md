title: Composer - Chapter 7 - 资源包
categories:
  - PHP
  - Composer
tags:
  - PHP
  - Composer

---

Composer 是如何确定一个资源包的版本号列表的，获取依赖的资源包时又是如何限定其版本号的？
    
<!--more-->

## 包名格式

    waza-ari/monolog-mysql
    
* 格式为：供应商名/库名
* 供应商名称和库名都是必填项
* 供应商名称的存在很好的解决了命名冲突的问题
* 使用 github 上的用户名作为供应商名称是不错的选择
* 包名不区分大小写，但惯例是使用小写字母，并用连字符作为单词的分隔

## 资源包的版本号确定方式

### 手工管理的代码包

没有使用VCS，手工管理的代码发布为资源包时，版本号也需要手工指定，即再增加"version"属性：

```
{
    "name":"vendor/package",
    "description","lalalalalalaaa",
    "version":"1.0",
}
```
注：版本号前**不要**加v，即"v1.0"是**不**对的

### 使用 VCS 管理的代码包

使用 VCS 管理的代码，发布为资源包时 **不要** 指定版本号，否则会和 tag 名冲突，因为 composer 将查找 VCS 所有的 tag 和 branch，然后把它们转化成一个内部的版本号列表供`require`属性选择。

#### 根据 tag 标签创建版本号

composer 对于 VCS 每一个看起来像版本号的 tag 都会创建一个包的版本，版本号的格式如下：

|格式|~~v~~ X.Y.Z[-\|alpha,beta,patch,p,RC][n]|
|-|-|
|v|VCS 标签可能带 v，但 require 约束不允许 v 字符，所以 v 会被去除|
|X.Y.Z|主版本.次版本.修订版本 的格式|
|-alpha...|可选的连字符和后缀|
|n|可选的数字|

查看 tag 列表：
```
$ git tag
1.0.0
v1.0.0
1.10.5-RC1
v4.4.4beta2
v2.0.0-alpha
v2.0.4-p1
```
composer 将去除前面的 v 字符，并基于这些 tag 创建一个版本列表。

    安装依赖包时:
    composer 根据`require`约束找到匹配的所有版本中的最高版本
    
#### 根据 branch 分支创建版本号

composer 对于 VCS 的每一个分支，都会创建相应的版本号，并根据分支名是否像版本号使用不同的规则来创建版本号。

##### 分支名像版本号
将创建一个格式 `{分支名}.x-dev` 的版本号
.x是为了技术上识别特意加入的字符，表示是分支而不是tag
例如一个分支 2.0 将产生一个 2.0.x-dev 包版本
如果分支名本身就是2.0.x，产生的版本号还是2.0.x-dev
其实.x可以为随机的字符
    
##### 分支名不像版本号
将创建一个格式`dev-{分支名}`的版本号
例如 master 将产生 dev-master 版本号
        
查看 branch 列表：
```
~/my-library$ git branch
v1
v2
my-feature
nother-feature
```
    安装依赖包时：
    对应的 `require` 的版本约束应分别写为这样的形式： `2.0.x-dev` `dev-master`

#### tag 和 branch 的默认检出方式不同

composer 对于稳定版本的包，默认使用--prefer-dist选项

检出 tag 版本时，composer 只是下载对应的 zip 归档文件并解压到 vendor 文件夹的正确位置。

检出 branch 版本时，composer 会假定你要在这个分支上工作，会把 branch 所属的整个资源库都真正的克隆到你的 vendor 文件夹。

下面的命令选项可以更改这种默认行为：

* --prefer-source
强制克隆源代码
Forces installation from package sources when possible, including VCS information.

* --prefer-dist
强制使用压缩包
Forces installation from package dist even for dev versions.

## 对依赖包的版本号限定方式

composer.json 文件中：
```
{
    "require":{
        "vendor/package":"version" 
    }
}
```

### 确切的版本号

    1.0.2
    
让 composer 只安装这个版本，如果其他依赖使用的版本不同，则 composer 的 install 或 update 命令将中止运行。

### 指定版本范围

* 连字符：`-`
* 运算符：`>、>=、<、<=、!=`
* 逻辑与：逗号`,`或空格`  `
* 逻辑或：双管道符号 `||`
* 优先级: 逻辑与 高于 逻辑或

```
1.0-2.0      表示>=1.0.0 <2.1，因为2.0表示2.0.*
1.0.0-2.1.0  表示>=1.0.0 <=2.1.0
>=1.0
>=1.0 <2.0
>=1.0,<2.0
>=1.0,<1.1|>=1.2 
```

### 使用通配符 *

    1.0.*   表示>=1.0,<1.1

注意：不要混用比较符和通配符，即 `>=2.*` 这样的版本约束不明确是无效的，因为：
`>=2` 表示 `>=2.0.0`，包含 3.0 及以上版本
`2.*` 表示 `>=2.0.0` 且 `<3.0`，不包含 3.0 及以上版本

### 使用语义化版本 2.0.0 

> 
[语义化版本 2.0.0](http://semver.org/lang/zh-CN/)
> 
格式：x.y.z
表示：主版本号.次版本号.修订号
> 
* 主版本号为零（0.y.z）
软件处于开发初始阶段，一切都可能随时被改变。
这时的公共 API 不应该被视为稳定版。
* 1.0.0 版本号
用于界定公共 API 的形成。
这一版本之后所有的版本号更新都基于公共 API 及其修改内容。
* 主版本号>0（x.y.z|x>0）
修改了不向下兼容的公共API时更新主版本号，同时次版本号和修订号清零
增加了向下兼容的新功能时递增次版本号，同时修订号清零
没有修改公共API，只做了向下兼容的bug修正时递增修订号
    
#### 波浪号~

* **只允许最后一位递增，次版本号或修订版本号二者更新其一**
* 主版本号数字对~是例外，即~2不表示主版本号可以递增，而是被视为~2.0依旧保持向后兼容性
* 常用来标记依赖包的最小次要版本，如~1.2
    ~1.2.3  表示>=1.2.3 <1.3.0
    ~1.2    表示>=1.2 <2.0.0
    
        注意不包括2.0.0，也不包括2.0-beta.1，
        虽然2.0-beta.1确实在2.0.0之前，但此例中只允许 .2 递增，1. 部分是固定死的
    
#### 插入号^

* **允许向后兼容的更新：次版本号和修订版本号可同时更新**
* 特殊情况1.0之前的版本不更新次版本号
* 写库代码时为了最大的互通性(interoperability)推荐使用此操作符

^1.2.3  表示>=1.2.3 <2.0.0，即主版本没有更新，不影响向后兼容
^0.3    表示>=0.3.0 <0.4.0，

## 版本稳定性约束

```
~/my-library$ git tag
v1.1-BETA
v1.1-RC1
v1.1-RC2
v1.1
```
如上所见，在 v1.1 稳定版之前，有v1.1-BETA,v1.1-RC1,v1.1-RC2，要获得BETA，RC等版本，则需要明确的告诉 composer 这些包是可接受的。

    可用的稳定性标识（按稳定性排序）：dev、alpha、beta、RC、stable。
    
    Question:
    这里的设置是不是只针对tag无关branch？

### 版本号后直接加稳定约束

如果没有明确定义稳定性，composer 在**内部**会自动添加`-dev`或`-stable`到版本号后面，到底添加哪个由定义版本范围的操作符确定：

|约束|内部版本|
|-|-|
|1.2.3|=1.2.3.0-stable|
|\>=1.2-stable|\>=1.2.0.0-stable|
|\>1.2|\>1.2.0.0-stable|
|\>=1.2|\>=1.2.0.0-dev|
|<1.3|<1.3.0.0-dev|
|<=1.3|<=1.3.0.0-stable|
|1 - 2|\>=1.0.0.0-dev <3.0.0.0-dev|
|~1.3|\>=1.3.0.0-dev <2.0.0.0-dev|
|1.4.*|\>=1.4.0.0-dev <1.5.0.0-dev|


* 版本大小排序：

    1.2 < 1.3dev < 1.3alpha < 1.3beta < 1.3RC < 1.3[Stable]
    同一个版本1.3最大的是stable版，最小的是dev版

* 为保证兼容性，操作符规律：

        <   时取最小dev，即比最小版本还要小
        >   时取最大stable，即比最大版还要大
        =   时取最大stable，因1.3默认就是1.3Stable
        >=  时取最小dev，
        <=  时取最大stable

    Question
    minimum-stability没起作用吗，为什么还会后面加-stable或-dev
    这个后面加-dev不会和像版本号的branch冲突吗？
    https://getcomposer.org/doc/articles/versions.md#stability-constraints

### 设置全局最小稳定性对所有包进行过滤。

```
"minimum-stability": "stable"
```
* 仅针对“root 包”
* 默认为 stable（稳定），即只有稳定的发行版才会被考虑在内。
* 低于此设置值的包都会被忽略
* 

### 对于个别包的特殊稳定性使用稳定标志 @

```
{
    "require": {
        "monolog/monolog": "1.0.米@beta",
        "acme/foo": "@dev"
    }
}
```
* 仅针对“root 包”
* 在 require 或 require-dev 中设定 `稳定标志 @`
* 方法是在一个包的版本约束后使用它：`"1.0.米@beta"`（米实际为星号，vim里星号导致后面的颜色错误）
* 也可以在一个空的版本约束内使用它:`"@dev"`

### 对 dev 版本明确引用

```
{
    "require": {
        "monolog/monolog": "dev-master#2eb0c0978d290a1c45346a1955188929cb4e5db7",
        "acme/foo": "1.0.x-dev#abc123"
    }
}
```
* **应该只是在开发时切换不到tag版本时临时用一下**
* **compose小组对此特性不接受bug报告**
* 支持对 dev（开发）版本的明确引用
* 会确保依赖被锁定到一个给定的状态，即使运行了更新命令。
* 在 require 和 require-dev 中设定 `#<ref>` 的标识
* 方法是在一个开发版本号后面带上 `#<ref>` 的标识。
* `<ref>`可以是 commit 的 SHA 值

### prefer-stable
```
"prefer-stable":"true"
```
设为 true 后 composer 将优先选择更稳定的版本，例如当require一个dev版的包或者只有alpha版的包可用时，将授意minimum-stability接受它。

    Question:
    这里应该是尽可能选择更稳定的版本吧，优先选择更稳定版本应该就是默认的啊


### 5. 推荐的版本稳定性设定方案

全局设置为stable，个别包使用稳定标志设置特殊的稳定性

```
{
  "minimum-stability":"stable",
  "require":{
        "kartik-v/yii2-grid": "@dev" 
  }
}
```

## 版本约束测试网址

[semver.mwl.be](https://semver.mwl.be/)
    
        
