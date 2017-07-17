# Hexo 详解
标签: AAA Hexo

title: Hexo 详解
categories: Hexo
tags: Hexo

---

Hexo 是一个简洁高效的静态站点生成框架，可以基于 Markdown 文件生成纯静态博客。

<!-- more -->

## 安装软件

hexo 依赖于 Git 和 Node.js，需先安装这两个软件包，安装过程省略。

### 安装 hexo

使用 Node.js 自带的包管理工具 npm 把 hexo 作为 Node.js 的模块安装
```
$ npm install -g hexo-cli
```
* 将自动从 npm 服务器下载并安装
* -g 参数表示全局安装

### 安装 deployer

部署到 git 库则安装 hexo-deployer-git
```
$ npm install hexo-deployer-git --save
```

部署到 Heroku 则安装 hexo-deployer-heroku
```
$ npm install hexo-deployer-heroku --save
```

部署到 Rsync 则安装 hexo-deployer-rsync
```
$ npm install hexo-deployer-rsync --save
```

### 安装 hexo-server

Hexo 3.0 把服务器独立成了模块，必须先安装 hexo-server 才能使用。
```
$ npm install hexo-server --save
```

### 使用淘宝 NPM 镜像

NPM 国外服务器下载速度较慢，可以使用淘宝 NPM 镜像
http://npm.taobao.org/

先安装淘宝定制的 cnpm
```
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```

安装后就可以用 cnpm 代替 npm
```
$ cnpm install [name]
```

## 创建目录

### 初始化站点目录

```
$ hexo init <BlogFolder>
```
* &lt;BlogFolder&gt; 非空会提示错误
* 建立目录结构如下
    ├── .gitignore
    ├── package.json
    ├── _config.yml
    ├── node_modules
    ├── scaffolds
    ├── source
    |   ├── _drafts
    |   └── _posts
    └── themes
|文件夹|保存内容|
|---|---|
|package.json|应用程序和已安装模块的版本信息。EJS, Stylus 和 Markdown renderer 已默认安装|
|_config.yml|站点配置文件，每个主题也有对应得到主题配置文件，位于 themes/theme_name 目录下|
|scaffolds|模版 文件夹。<br/>新建文章时，Hexo 会根据 scaffold 来建立文件。|
|source|资源文件夹是存放用户资源的地方。<br/><br/><b>_posts 目录里存放的就是用户的文章，Hexo 会根据这里的文章生成静态网页，Markdown 和 HTML 文件会被解析并放到 public 文件夹，而其他文件会被拷贝过去。</b><br/><br/>_drafts 目录存放的是草稿文件。<br/><br/>除 _posts 文件夹之外，以下划线 _ 开头命名的文件或文件夹和隐藏的文件将会被忽略。|
|public|存放生成的页面|
|themes|主题 文件夹。<br/>Hexo 会根据主题来生成静态页面。|
|db.json|从 source 解析得到的缓存文件|
|node_modules|hexo 插件|


### 安装 hexo 插件

??? 这一步貌似可以不执行，git init 时已经安装过依赖包了

```
$ cd <BlogFolder>

$ npm install
npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@^1.0.0 (node_modules\chokidar\node_modules\fsevents):

npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@1.1.2: wanted `{"os":"darwin","arch":"any"}` (current: `{"os":"win32","arch":"ia32"}`)
```
* 在包目录中安装时无需指定参数
* 警告提示 `Unsupported platform` 是因为 fsevents 是用于 OS X 系统的，在 windowns 或 linux 下使用就有警告，忽略即可，或者加上 `--no-optional` 参数

### 创建其他目录 

常用目录还包括 tags、categories、archives、about 需要手动创建

新建页面，命名为 tags
```
$ hexo new page tags
INFO  Created: D:\git\shenlin.ltd\source\tags\index.md
```
* 其实是自动创建了 tags 目录 和 tags/index.md 文件

更改文件类型
```
//编辑 index.md
$ vi tags/index.md

//在最后一行 --- 上面加上这行
type: "tags"
```
* 这步是必须的，否则 index.md 就是个普通文件，但 about/index.md 不需要
* hexo 将自动为标签页面创建标签云

相同的步骤创建 categories 和 archives
```
$ hexo new page categories
INFO  Created: D:\git\shenlin.ltd\source\categories\index.md

$ vim source/categories/index.md
type: "categories"

$ hexo new page archives
INFO  Created: D:\git\shenlin.ltd\source\archives\index.md

$ vim source/archives/index.md
type: "archives"

$ hexo new page about
INFO  Created: D:\git\shenlin.ltd\source\about\index.md
```
* about 页面无需增加 `type: "about"` 这行

## 本地运行

经过上面的步骤，所需软件包已全部安装，目录也生成了，hexo 默认有篇名为 hello-world.md 位于 source/_posts 目录下，可以运行看效果了：

```
//生成静态文件
$ hexo g

//启动内置服务器
$ hexo s
```
浏览 http://localhost:4000 就可以看到效果

## 远程部署

### 修改配置

修改 _config.yml 配置，不同的 deployer 配置不同，git 库的配置修改如下：

    deploy:
      type: git
      repo: <repository url>
      branch: [branch]
      message: [message]

* YAML依靠缩进来确定元素间的从属关系。因此，请确保每个同级元素的缩进长度相同，并且使用空格缩进。
* message	自定义提交信息，默认为 `Site updated: { { now('YYYY-MM-DD HH:mm:ss') }}`
* 使用的时间脚本是：[Moment.js](http://momentjs.com/) 

### 远程部署

//部署到远程服务器如 github
```
$ hexo d
```

可以使用远程地址查看效果，如 http://username.github.io/project_name

## 更换主题

浏览并找到喜欢的 theme ：https://hexo.io/themes/

git clone 到本地
```
$ git clone https://github.com/ppoffice/hexo-theme-hueman.git themes/hueman
```

修改根目录下的`站点配置文件` _config.yml
```
theme: hueman
```
* theme/hueman 下的`主题配置文件`原名是 _config.yml.example 改名为 _config.yml

## 站点地图

http://www.jianshu.com/p/ab44b916a8b6

## 评论系统

http://www.uyan.cc/
http://www.jianshu.com/p/4729e92fddbe

## 更多配置

### 布局 Layout

Hexo 有三种默认布局，使用不同布局的文章默认保存的位置也不同：

|布局|默认保存路径|
|---|---|
|page|source|
|draft|source/_drafts|
|post|source/_posts|
|自定义|source/_posts|
|false|文章将不被处理|

### 模板 Scaffold

使用 Scaffold 目录的 phpto 模板建立文件
```
$ hexo new photo "My Gallery"
```
* 模板中仅可使用下列变量：layout, title, date

### Front-matter

文件最上方以 --- 分割的区域，用于定义当前文件的参数，可用参数：

|参数|描述|默认值|
|---|---|---|
|layout|布局||
|title	|标题	||
|date	|建立日期	|文件建立日期|
|updated	|更新日期	|文件更新日期|
|comments	|开启文章的评论功能	|true|
|tags	|标签（不适用于分页）||	
|categories	|分类（不适用于分页）||	
|permalink	|覆盖文章网址||


* Hexo 中分类(categories)和标签(tags)有着明显的差别：分类具有顺序性和层次性，也就是说 Foo, Bar 不等于 Bar, Foo；而标签没有顺序和层次。
* Hexo不支持指定多个同级分类，下面的分类 Life 是 Diary 的子类
 
        categories:
        - Diary
        - Life
        tags:
        - PS3
        - Games

* 除了 YAML 外，也可以使用 JSON 来编写 Front-matter，只要将 --- 代换成 ;;; 即可

        "title": "Hello World",
        "date": "2013/7/13 20:46:25"
        ;;;
* 如果不想文章被处理，可以将 Front-Matter 中的 layout: 设为 false 。

### Asset 文件夹

资源（Asset）代表 source 文件夹中除了文章以外的所有文件，例如图片、CSS、JS 文件等。

Asset 提供了在文章中使用图片、JS等资源的另一种方法，特点是简单方便且更规律更组织化。

比方说，如果Hexo项目中只有少量图片，那最简单的方法就是将它们放在 source/images 文件夹中。然后通过类似于 `![](/images/image.jpg)` 的方法访问它们。

Asset 的方法是：

修改 _config.yml
```
post_asset_folder: true
```
上述命令启用了 Asset 文件夹，Hexo 将会在你每一次通过`hexo new [layout] <title>`命令创建新文章时自动创建一个文件夹。这个资源文件夹将会有与这个 markdown 文件一样的名字。将所有与你的文章有关的资源放在这个关联文件夹中之后，你可以通过相对路径来引用它们，这样你就得到了一个更简单而且方便得多的工作流。

### _config.yml 配置

_config.yml 使用 YAML 语言

#### YAML

YAML: YAML Ain't Markup Language

What It Is: YAML is a human friendly data serialization standard for all programming languages.
对用户友好且支持所有编程语言的数据序列化标准

http://www.yaml.org/

YAML依靠缩进来确定元素间的从属关系。因此，请确保每个同级元素的缩进长度相同，并且使用空格缩进。

如果 YAML 字符串中包含冒号（:）的话，要加上引号。

_config.yml 文件中所有冒号后面的空格，格式很严格，必须是只有一个半角空格。不管是多了还是少了都会报错，这是 yml 解释器所定义的语法，并且不能使用Tab。

一些人显而易见的配置项如 title, author 等未在下面列出。

#### 配置列表

|配置项|含义|
|---|---|
|**网站**||
|description|主要用于SEO，告诉搜索引擎一个关于您站点的简单描述，通常建议在其中包含您网站的关键词|
|**网址**||
|url|网址，如果网站存放在子目录中，例如 http://yoursite.com/blog，则请将 url 设为 http://yoursite.com/blog 并把 root 设为 /blog/|
|root|网站根目录|
|permalink|文章的 永久链接 格式	:year/:month/:day/:title/，注意不要漏掉最后的/|
|permalink_defaults|永久链接中各部分的默认值|	
|**目录**||
|code_dir|Include code 文件夹，默认值 downloads/code|
|i18n_dir|国际化（i18n）文件夹，默认值 :lang|
|skip_render|跳过指定文件的渲染，您可使用 glob 表达式来匹配路径。|
|**文章**||
|new_post_name|新文章的文件名称，默认值 	:title.md，表示以标题作为文件名称，设为 :year-:month-:day-:title.md 则可以方便的通过日期来管理文章，:i_month,:i_day表示没有前导0|
|default_layout|预设布局，默认值 	post|
|auto_spacing|在中文和英文之间加入空格，默认值 	false|
|titlecase|把标题转换为 title case，默认值 	false|
|external_link|在新标签中打开链接，默认值 	true|
|filename_case|把文件名称转换为 (1) 小写或 (2) 大写，默认值 	0|
|render_drafts|显示草稿，默认值 	false|
|post_asset_folder|启动 Asset 文件夹，默认值 	false，开启后 Hexo 将会在你每一次通过`hexo new [layout] <title>`命令创建新文章时自动创建一个文件夹。这个资源文件夹将会有与这个 markdown 文件一样的名字。将所有与你的文章有关的资源放在这个关联文件夹中，你可以通过相对路径来引用它们|
|relative_link|把链接改为与根目录的相对位址，默认值 	false<br/>绝对链接是包含完整域名的，如http://shenlin.ltd/hello.html，通常建议绝对路径，但也限制死了域名，而相对路径如/index.html则可使用多个域名访问|
|future|显示未来的文章，默认值 	true|
|highlight|代码块的设置	|
|**其他**||
|default_category|默认分类，默认值 uncategorized|
|category_map|分类别名|	
|tag_map|标签别名|	
|per_page|每页显示的文章量 (0 = 关闭分页功能)，默认值10|
|pagination_dir|分页目录，默认值page|
|theme|当前主题名称。值为false时禁用主题|

deploy部分因为格式关系单独写这里
```
deploy: 
    type: git
    repo: git@github.com:shenlin9/shenlin.ltd
    branch: gh-pages
```
可同时使用多个 deployer，Hexo 会依照顺序执行每个 deployer。
```
deploy:
- type: git
  repo:
- type: heroku
  repo:
```


## 更多命令

新建网站
```
$ hexo init [folder]
```

新建文章
```
$ hexo new [layout] <title>
```
* 文章有空格则使用引号括起来

生成静态文件
```
$ hexo g
$ hexo g -d
$ hexo generate
```
* -d,--deploy 文件生成后立即部署
* -w,--watch 监视文件变动
    * Hexo 能够监视文件变动并立即重新生成静态文件，在生成时会比对文件的 SHA1 checksum，只有变动的文件才会写入。

部署网站
```
$ hexo d
$ hexo d -g
$ hexo deploy
```
* -g, --generate	部署之前先生成静态文件
* 部署时根目录下的 CNAME 会被删除，移动到 source 目录即可

显示草稿
```
$ hexo --draft
```
* 显示 source/_drafts 文件夹中的草稿文章。

发表草稿
```
$ hexo publish [layout] <filename>
```
* 会把文件从草稿目录 `source/_drafts` 移动到文章目录 `source/_posts`

启动服务器
```
$ hexo s
$ hexo server
```
* 默认网址 http://localhost:4000/
* 服务器启动期间，Hexo 会监视文件变动并自动更新，无须重启服务器
* -p, --port	重设端口
* -l, --log     启动日记记录，使用覆盖记录格式
* -i            自定义IP，只能通过指定的IP访问
* -s, --static	静态模式，只处理 public 文件夹内的文件，不处理文件变动，常用于生产环境

清除文件
```
$ hexo clean
```
* 会清除缓存文件 (db.json) 和已生成的静态文件 (public)。
* 如果对站点的更改无论如何也不生效（如更换主题后），可以运行该命令。

列出站点信息
```
$ hexo list <type>
```
* &lt;type&gt;取值：page, post, route, tag, category

显示版本
```
$ hexo version
```

自定义配置文件的路径
```
$ hexo --config custom.yml
```
* 执行后将不再使用 _config.yml。

自定义 CWD
```
$ hexo --cwd /path/to/cwd
```
* 自定义当前工作目录（Current working directory）的路径。

渲染文件
```
$ hexo render <file1> [file2] ...
```
* -o, --output	设置输出路径

从其他博客系统 迁移内容
```
$ hexo migrate <type>
```

模式选项
|模式选项|说明|
|---|---|
|安全模式<br/>`$ hexo --safe`|在安全模式下，不会载入插件和脚本。当您在安装新插件遭遇问题时，可以尝试以安全模式重新执行。|
|调试模式<br/>`$ hexo --debug`|在终端中显示调试信息并记录到 debug.log。当您碰到问题时，可以尝试用调试模式重新执行一次，并 提交调试信息到 GitHub。|
|简洁模式<br/>`$ hexo --silent`|隐藏终端信息|

## Q&amp;A

1. Template render error
```
$ hexo g
INFO  Start processing
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
Template render error: (unknown path) [Line 13, Column 46]
```

hexo转义时候发生的错误，文章中可能出现了连续两个`{`或一个`{`后紧跟一个`%`，经测试用反引号转义也不行，在`{`后加一个空格通过，即写成`{ {}}`，`{ %%}`的样式。