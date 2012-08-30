---
title: 搭建Sencha Touch开发环境 - css
author: Tower He
date_created: 2011/04/26
categories: [programming]
tags: [sass, sencha touch, compass, css, javascript]
---

在上一篇文章搭建Sencha Touch开发环境中，构建了一个Sencha
Touch项目的基本目录结构，接下来将介绍如何在一个Sench
Touch项目中使用Compass来开发css。

READMORE

### 安装compass

```
  # 详细的安装方法请参看
  # http://compass-style.org/install/
  gem install compass
```

### 创建Compass目录结构

```bash
compass create /path/to/project/public/resources/
```

### 配置Compass

默认的Compass配置文件为/path/to/project/public/resources/config.rb，打开该文件，并修改其配置如下：

<pre>
  # sass目录
  sass_dir = "sass"

  # css目录
  css_dir = "stylesheets"

  # 图片目录
  images_dir = "images"

  # 指定输出格式
  # 可选值:compressed, :nested, :expanded, :compact
  output_style = :compressed

  # 环境
  environment = :production
</pre>

### 编写scss

打开/path/to/project/public/resources/sass/application.scss文件，并在其中编写该项目要使用的scss。例如：

<pre>
$base-color: red;

.x-toolbar {
  color: $base-color;
}
</pre>

### 编译

```
compass compile /path/to/project/public/resources
```

编译后输出的css文件存放在/path/to/project/public/resources/stylesheets/，现在打开该目录中的application.css，其生成的代码如下：

```css
.x-toolbar{color:red}
```

### 参考资料：

* [Compass Configuration Reference](http://compass-style.org/help/tutorials/configuration-reference/)
* [Structuring Your Sencha Touch Application](http://www.slideshare.net/senchainc/structuring-your-sencha-touch-application)
* [An Introduction to Theming Sencha Touch](http://www.sencha.com/blog/an-introduction-to-theming-sencha-touch)
