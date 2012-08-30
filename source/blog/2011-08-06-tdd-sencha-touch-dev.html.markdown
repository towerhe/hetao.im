---
title: 测试驱动开发Sencha Touch环境搭建
author: Tower He
categories: [programming]
tags: [javascript, sencha touch]
date_created: 2011/08/06

---

*前篇：*

* [搭建SENCHA TOUCH开发环境](http://hetao.im/2011/04/24/setup-sencha-touch-dev-env)
* [搭建SENCHA TOUCH开发环境 – CSS](http://hetao.im/2011/04/26/setup-sencha-touch-dev-env-css)

在前面两篇文章中介绍了如何手动的构建一个Sencha
Touch开发环境，本文将着重介绍如何使用工具搭建一个测试驱动的Sencha
Touch项目。

READMORE

### Sencha Touch辅助工具

在Sencha Touch中有一个鲜为人知的工具Sencha
Command，位于sencha-touch-1.1.0/jsbuilder目录中，名为sencha.sh。相关资料表明该工具是用来自动化生成Sencha
Touch项目目录结构，并包含一个代码生成器。使用了一下发现其明显处于试验阶段，因为代码中存在非常多的错误。通过sencha.sh生成的代码无法正常运行。不过不用担心，我们可以使用后面介绍的[sencha-command](https://github.com/towerhe/sencha-command)来完成这些操作。下面简单的介绍sencha.sh的几个用法：

* *生成Sencha Touch项目*

  ```:::bash
    ./sencha.sh generate app YourSenchaTouchApp /path/to/save/YourSenchaTouchApp
  ```

*参数解释：*

*generate* - 指定使用generator模块，该模块负责代码的自动生成；

*app* - 指定将要生成的是Sencha Touch项目；

*YourSenchaTouchApp* - 指定待生成的Sencha Touch项目名称，该名称将作为Ext.Application的名称，因此命名必须是一个合法的javascript的一个合法标识符；

*/path/to/save/YourSenchaTouchApp* - 指定待生成的项目将被保存在何处。

* *生成Sencha Touch Model*

  本命令需要在生成的Sencha Touch目录中执行，该命令会在Sencha
Touch项目中生成三个文件并修改两个文件。但是很遗憾，目前版本不经过修改将无法正常执行。

```
  ├── app
  │   ├── models
  │   │   └── User.js      - 新生成的User模型
  ├── index.html           - 引入app/models/User.js
  └── test
      ├── fixtures
      │   └── User.js      - 新生成的User模型的fixture
      └── unit
          ├── index.html   - 引入models/User.js
          └─── models
               └── User.js - 新生成的User模型的spec
```

```:::bash
  ./sencha.sh generate model User name:string password:string age:int
```

  *参数解释：*
  *generate* - 指定使用generator模块，该模块负责代码的自动生成；
  *model* - 指定将要生成的是Sencha Touch模型
  *User* - 指定待生成的模型的名称
  *name:string password:string age:int* - 指定待生成的属性定义，属性定义由两部分组成，<属性名>:<类型>。

* *生成Sencha Touch Controller*

  本命令需要在生成的Sencha Touch目录中执行，该命令会在Sencha
Touch项目中生成两个文件并修改两个文件。同样很遗憾，目前版本不经过修改将无法正常执行。

```
  ├── app
  │   ├── controllers
  │   │   └── users.js            - 新生成的users控制器
  ├── index.html                  - 引入app/controllers/users.js
  └── test
      └── unit
          ├── index.html          - 引入controllers/users.js
          └── controllers
               └── controllers.js - 新生成的users控制器的spec
```

```:::bash
  ./sencha.sh generate controller users index new create show update destroy
```

*参数解释：*
*generate* - 指定使用generator模块，该模块负责代码的自动生成；
*controller* - 指定将要生成的是Sencha Touch控制器
*users* - 指定待生成的控制器的名称
*index new create show update destroy* - 指定待生成的控制器方法。

### Sencha Command

Sencha Command - 是[muc](https://github.com/muc)"修正后发布的工具，该工具修正了Sencha Touch自带的工具存在的大部分问题。但是单元测试，以及提示信息依然存在一些小的问题。我在github上fork了这个项目[https://github.com/towerhe/sencha-command](https://github.com/towerhe/sencha-command)，并修正了一些目前发现的问题：

* test/unit/index.html中引入的sencha-touch-debug.js目录不正确
* test/unit/index.html中默认不引入app.js
* 调用generator时提示信息错误

Sencha Command与Sencha
Touch自带的工具完成相同的工作，但有一些细小的改动：

* 目录结构不同
* 原脚本sencha.sh改名为sencha
* 生成的项目中包含了Sencha Touch的资源文件
* 修复了sencha touch资源引用的错误
* 修复了无法使用模型和控制器生成器的错误

*如何使用Sencha Command*

```
  # 1. 从github中签出sencha-command
  #    git clone https://github.com/towerhe/sencha-command.git
  # 2. 参照前面介绍的Sencha自带工具的用法生成项目、模型、控制器
  # 3. 将项目部署到Web服务器
  # 4. 浏览http://{your_server}:{port}/{any_prefix}/index.html来访问项目
  # 5. 浏览http://{your_server}:{port}/{any_prefix}/test/unit/index.html来访问项目的单元测试
```
