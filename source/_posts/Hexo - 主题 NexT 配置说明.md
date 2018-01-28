---
title: Hexo 主题 NexT 配置说明
categories:
  - Hexo
  - NexT
tags:
  - Hexo
  - NexT
---

NexT 是 github 上最多星星的 Hexo 主题。

Hexo 有两份主要的配置文件，名称都是 `_config.yml`。一份位于站点根目录下，主要包含 Hexo 本身的配置；另一份位于主题目录下，主要用于配置主题相关的选项，由主题作者提供。

<!-- more -->

为了描述方便，在以下说明中，将前者称为`站点配置文件`， 后者称为 `主题配置文件`。

## 安装主题

安装最新版本
```
$ cd your-hexo-site
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```

安装最稳定版本
1. 访问发布页面：https://github.com/iissnan/hexo-theme-next/releases
2. 下载需要的版本包`Source Code (zip)`
3. 解压到站点 themes 目录下，并改名为 next

## 启用主题

修改站点配置文件 `_config.yml`
```
theme: next
```

## 验证主题

验证主题之前最好先清除 hexo 缓存
```
$ hexo clean
```

验证主题
```
$ hexo s --debug
```
* 开启 hexo 服务器的调试模式，查看输出是否有错误异常信息
* 无异常则可以使用 http://localhost:4000/ 访问

## 更改宽度

NexT 对于内容的宽度的设定如下：
* 700px，当屏幕宽度 < 1600px
* 900px，当屏幕宽度 >= 1600px
* 移动设备下，宽度自适应

编辑文件：`source/css/_variables/custom.styl`
```
// 当屏幕宽度小于 1600px 
$content-desktop = 700px

// 当屏幕宽度大于 1600px
$content-desktop-large = 900px
```

## 主题设置

下列设置都是在主题配置文件`source/themes/next/_config.yml` 中进行

### Scheme

有3个主题

    scheme: Muse
    #scheme: Mist
    #scheme: Pisces
    #scheme: Gemini

### 语言

    language: zh-Hans
    
* 注意：中文简体不是常用的zh_CN，而是zh-Hans，中间是连字符不是下划线
* 各语言文件位于 languages 目录下

### 菜单

#### 菜单链接和文字

    menu:
      home: /
      archives: /archives
      about: /about
      categories: /categories
      tags: /tags
      #commonweal: /404.html
    
* home, about 等是菜单项的名称，不是显示在菜单上的文字，菜单显示的文字是在 languages/zh-Hans.yml 这样的文件里根据语言设定的
* 如果这里增加一项菜单，则对应的语言文件里也要增加相应的翻译
* 只有 home 不需要手动创建，其余目录文件都需要手动创建

#### 菜单图标

    menu_icons:
      enable: true
      # Icon Mapping.
      home: home
      about: user
      categories: th
      tags: tags
      archives: archive
      commonweal: heartbeat
      
* enable 表示是否显示图标
* 大小写敏感
* 以 about: user 举例，前面的还是菜单项名，后面的是图标名，图标名不能匹配则显示问号图标。
* 使用的图标是 `Font Awesome`，矢量可变形，
    * http://fontawesome.dashgame.com/
    * http://fontawesome.io/

### 侧栏

侧栏位置

    sidebar:
      position: left

* 取值left,right
* 仅 Pisces Scheme 支持

侧栏显示时机

    sidebar:
      display: post
      
* 取值：
    * post      默认，在文章详细页面（拥有目录列表）时显示
    * always    在所有页面中都显示
    * hide      在所有页面中都隐藏（可以手动展开）
    * remove    完全移除
* use motion: false 时不显示

### 头像

    avatar: /images/avatar.jpg
    avatar: /uploads/avatar.jpg
    avatar: http://exmaple.com/avatar.jpg
    
分别对应头像位置：

    主题目录/source/images/avatar.jpg
    主题目录/source/uploads/avatar.jpg
    网络全路径
    
注意是主题目录下，不是网站根目录下

### 标签

#### 文章中的标签

    title: 标签测试文章
    tags:
      - Testing
      - Another Tag
    ---

#### 标签页面

「标签」页面将展示站点的所有标签，若所有文章都未包含标签，则此页面是空的，否则主题会自动为标签页面显示标签云。

新建页面，命名为 tags
```
$ hexo new page tags
INFO  Created: D:\git\shenlin.ltd\source\tags\index.md
```

还必须将页面的类型设置为 tags
```
$ vi tags/index.md

//增加一行
type: "tags"

//标签页面还应禁止评论，再增加一行
comments: false
```

### 代码高亮

NexT 使用 [Tomorrow Theme](https://github.com/chriskempson/tomorrow-theme) 作为代码高亮，有5款主题：normal，night， night blue， night bright， night eighties

    highlight_theme: normal
    
### 404页面

站点 source 目录下创建 404.html，可以放腾讯公益页面，代码如下：
```
<!DOCTYPE HTML>
<html>
<head>
  <meta http-equiv="content-type" content="text/html;charset=utf-8;"/>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
  <meta name="robots" content="all" />
  <meta name="robots" content="index,follow"/>
  <link rel="stylesheet" type="text/css" href="https://qzone.qq.com/gy/404/style/404style.css">
</head>
<body>
  <script type="text/plain" src="http://www.qq.com/404/search_children.js"
          charset="utf-8" homePageUrl="/"
          homePageName="回到我的主页">
  </script>
  <script src="https://qzone.qq.com/gy/404/data.js" charset="utf-8"></script>
  <script src="https://qzone.qq.com/gy/404/page.js" charset="utf-8"></script>
</body>
</html>
```

### 动画效果

    use_motion: true  # 开启动画效果
    use_motion: false # 关闭动画效果
    
动画效果使用需要等待 JavaScript 脚本完全加载完毕后才会显示内容，比较在乎速度则可以关闭动画。

### 背景动画

有两种，但同时只能开启一种

    # canvas_nest
    canvas_nest: true //开启动画
    canvas_nest: false //关闭动画
    
    # three_waves
    three_waves: true //开启动画
    three_waves: false //关闭动画
    
### 设置阅读全文

阅读全文即在首页显示一篇文章的部分内容，并提供一个链接跳转到全文页面。
3 实现种方法：

1. 在文章中使用 <!-- more --> 手动进行截断，这是 Hexo 提供的方式，推荐使用，除了可以精确控制需要显示的摘录内容外，也可以让 Hexo 中的插件更好的识别。
2. 在文章 front-matter 中添加 description，并提供文章摘录
3. 自动形成摘要，在 主题配置文件 中添加：

        auto_excerpt:
          enable: true
          length: 150
    默认截取的长度为 150 字符
    
