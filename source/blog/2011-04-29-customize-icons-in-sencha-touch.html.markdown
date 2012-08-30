---
title: 自定义Sencha Touch中的icons
author: Tower He
date_created: 2011/04/29
categories: [programming]
tags: [sass, sencha touch, compass, css]
---

在前面一篇文章里面介绍了如何搭建Sencha
Touch项目Compass的开发环境。这篇文章将以自定义Sencha
Touch中icons为例子，进一步的介绍如何使用Compass来进行css的开发。

READMORE

### 加载Sencha Touch的Compass框架

要自定义Sencha Touch默认的CSS代码，首先需要导入Sencha
Touch的Compass框架，打开resources/config.rb文件，添加如下内容。

<pre>
  # 指定Sencha Touch目录
  SENCHA_TOUCH_PATH = '/path/to/sencha-touch'

  # 加载Sencha Touch
  load File.join(SENCHA_TOUCH_PATH, 'resources', 'themes')
</pre>

### 建立自定义的scss文件
在目录resources/sass目录下新建一个sencha-touch.scss的文件，文件内容如下：

<pre>
// 1. 修改Sencha Touch配置参数
// 不加载默认的icons
$include-default-icons: false;
 
// 2. 导入Sencha Touch的样式
@import 'sencha-touch/default/all';
@include sencha-panel;
@include sencha-buttons;
@include sencha-sheet;
@include sencha-picker;
@include sencha-toolbar-forms;
@include sencha-tabs;
@include sencha-toolbar;
@include sencha-carousel;
@include sencha-indexbar;
@include sencha-list;
@include sencha-layout;
@include sencha-form;
@include sencha-loading-spinner;
 
// 3. 定义自己的样式
// 加载需要的icons
@include pictos-iconmask('shop1');
@include pictos-iconmask('maps');
@include pictos-iconmask('docs2');
</pre>

### 编译
```:::bash
compass compile resources
```

新的Sencha Touch样式文件生成在resources/stylesheets目录中
