---
title: 搭建Sencha Touch开发环境
author: Tower He
date_created: 2011/04/24
categories: [programming]
tags: [sencha touch, javascript]
---

### 工具列表

* IDE - [Vim](http://www.vim.org) + [Janus](https://github.com/carlhuda/janus)
* JS interpreter - [Node.js](http://www.nodejs.org)

### 目录结构

```
├── config.ru            # Rack配置文件
├── Gemfile              # Bundler配置文件
└── public               # 项目主目录
    ├── app              # 项目JS源代码目录
    │   ├── controllers  # 控制器目录
    │   ├── models       # 模型目录
    │   ├── stores       # 仓库目录
    │   └── views        # 视图目录
    ├── favicon.ico      # 项目ico
    ├── index.html       # 项目主页
    └── lib              # 库文件目录
        ├── sencha-touch.css      # Sencha Touch样式文件
        ├── sencha-touch-debug.js # Sencha Touch调试版JS
        └── sencha-touch.js       # Sencha Touch应用版JS
```

READMORE

### Rack配置

<pre>
  require 'rubygems'
  require 'rack/rewrite'

  use Rack::Rewrite do
    rewrite '/', '/index.html'
  end
  use Rack::Static, :urls => ['/'], :root => "public"

  run Rack::Directory.new('public')
</pre>

### Bundler配置

<pre>
  source 'http://rubygems.org'

  gem "rack"
  gem "rack-rewrite"
  gem "serve"
  gem "thin"
</pre>

### 启动Rack Server预览项目
- 进入项目目录：

```:::bash
cd /path/to/project
```

- 启动Rack Server：

```:::bash
rackup
```

- 打开浏览器，预览项目：[http://localhost:9292](http://localhost:9292)
