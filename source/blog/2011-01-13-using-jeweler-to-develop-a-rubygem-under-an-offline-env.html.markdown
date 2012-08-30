---
title: 在离线环境中使用Jeweler开发RubyGem
author: Tower He
date_created: 2011/01/13
categories: [programming]
tags: [ruby, gem, jeweler]
---

在Jeweler官方的[README.markdown](https://github.com/technicalpickles/jeweler/blob/master/README.markdown)详细的描述了如何利用Jeweler，结合[Github](http://github.com)和[Rubygems](http://rubygems.org)提供的服务来进行在线的RubyGem项目的开发、管理和发布。

如果所在的环境并不具备使用github.com和rubygems.org提供的服务，或者并不希望将的源码通过github.com来进行管理，也并不希望将开发好的RubyGem项目发布到rubygems.org，那么在这样的情况下又如何利用Jeweler来开发、管理和发布你的RubyGem项目呢？本文的下面部分将围绕这个假设来进行详细的叙述。

READMORE

### [Jeweler](https://github.com/technicalpickles/jeweler)简介

Jeweler是一个用于开发RubyGem项目的工具，主要为RubyGem的开发者提供了两个主要特性：

* 提供了一个便于管理和易于发布RubyGem的基础库；
* 为创建新的RubyGem项目提供了一个脚手架生成器。

### 开发、管理和发布项目

#### 安装Jeweler

```:::bash
gem install jeweler
```

#### 创建RubyGem项目

<pre>
  jeweler new_project
  # 如果使用rspec来做单元测试，那么在创建项目时需要指定
  jeweler --rspec new_project

  # 查看jeweler的所有选项
  jeweler -h
</pre>

#### 自定义RubyGem

在使用Jeweler的脚手架生成器创建了一个新的RubyGem项目后，首先需要修改该项目的一些元属性。这些属性将会用来生成.gemspec文件。属性信息在[http://docs.rubygems.org/read/chapter/20](http://docs.rubygems.org/read/chapter/20)中有详细的说明。Jeweler通过一个Rake
Task自动化的生成.gemspec文件，因此通过修改Rakefile文件中gem的相关属性来实现自定义RubyGem。下面是一个新生成项目的Rakefile文件：

```:::ruby
require 'jeweler'
Jeweler::Tasks.new do |gem|
  gem.name = "new_project"
  gem.homepage = "http://github.com/towerhe/new_project"
  gem.license = "MIT"
  gem.summary = %Q{TODO: one-line summary of your gem}
  gem.description = %Q{TODO: longer description of your gem}
  gem.email = "towerhe@gmail.com"
  gem.authors = ["Tower He"]
end
```

其中gem.name、gem.summary是必填属性，其余的均为可选属性。

*下面介绍一个重要的属性gem.files的配置*。

1) 使用git做版本控制

Jeweler默认假设使用git来进行版本控制，此时Jeweler会默认的将所有被添加到git中，并已经提交的，且不在.gitignore中的文件添加到gem.files中。也就是说，如果使用git来进行版本控制，那么就不需要去手动的定义gem.files属性，Jeweler会在打包的时候自动生成。

然而开发的项目，有时并不希望将所有提交到git中的内容全部打入包中，这时候该如何去处理呢？此时也需要通过修改Rakefile中的相关属性，来实现此类自定义打包的方案，例如：

* 不希望打包所有用于开发阶段的工具配置信息；
* 不希望打包测试文件；
* 不希望打包...

通过修改gem.files属性可以实现忽略这些不希望打包的文件/目录

<pre>
  # 忽略项目根目录下，所有以'.'打头的文件/目录
  # 这类文件通常是一些工具配置文件/目录，如：
  # .rvmrc, .rspec等等
  gem.files -= Dir.glob('\.*')

  # 忽略指定文件
  gem.files.exclude 'Gemfile'
  gem.files.exclude 'Gemfile.lock'

  # 忽略测试文件
  gem.files.exclude 'spec/**/*'
  # 必须同时设定该属性为空，才能忽略测试文件
  gem.test_files = []
</pre>

2) 使用subversion进行版本控制

这里指定用subversion来进行版本控制并没有其他什么特殊的含义，仅仅是为了说明不是采用git进行版本控制，即采用其他git外的版本控制方法与采用subversion类似。
因为采用了git外的版本控制工具，所以Jeweler无法自动在打包时添加需要打包的文件列表。故需要手动的去指定需要打包的文件列表。前面已经介绍了如何忽略不需要打包的文件，与此类似，通过修改gem.files属性，可以指定需要打包的文件列表。

<pre>
  # 添加项目根目录下所有以大些英文字母打头的文件
  gem.files.include '[A-Z]*'
  # 添加目录lib下的所有文件
  gem.files.include 'lib/**/*'
</pre>

最终新创建的项目的gem配置信息应该如下：

<pre>
Jeweler::Tasks.new do |gem|
  gem.name = "new_project"
  gem.homepage = "http://github.com/towerhe/new_project"
  gem.license = "MIT"
  gem.summary = %Q{a new RubyGem project}
  gem.description = %Q{Used to figure out how to use jeweler to develop a RubyGem project.}
  gem.email = "towerhe@gmail.com"
  gem.authors = ["Tower He"]

  gem.files.include '[A-Z]*'
  gem.files.include 'lib/**/*'
  
  gem.files -= Dir.glob('\.*')
  gem.files.exclude 'Rakefile'
  gem.files.exclude 'Gemfile'
  gem.files.exclude 'Gemfile.lock'
  gem.files.exclude 'spec/**/*'

  gem.test_files = []
end
</pre>

#### 发布项目

1) 确认所有以来的gem包已经全部安装

<pre>
  # 确认安装
  bundle install

  # 如果确定有gem包需要更新，则
  bundle update
</pre>

2) 重新生成.gemspec文件

<pre>
rake gemspec
</pre>

3) 提高版本

发布项目之前需要做的一件非常重要的事情就是版本管理，Jeweler提供了非常方便的Rake任务，来提高版本信息。

<pre class="brush:bash">
  # version:write 创建VERSION文件，并指定版本
  rake version:write MAJOR=0 MINOR=3 PATCH=0

  # 提高主版本号，如0.1.0 -> 1.0.0
  rake version:bump:major

  # 提高次版本号，如0.1.0 -> 0.2.0
  rake version:bump:minor

  # 提高次版本的发行次数，如0.1.0 -> 0.1.1
  rake version:bump:patch
</pre>

4) 将所有修改提交到版本库，并且打tag，以用subversion做版本控制为例

<pre class="brush:bash">
  # 查看当前状态
  svn status
  # 调整需要提交的内容
  ...
  # 提交所有修改
  svn commit -m 'some words about this action'
  # 打tag
  svn copy http://svn.server/new_project/trunk
  http://svn.server/new_project/tags/0.1.0 -m 'some words about this action'
</pre>

5) 打包

<pre class="brush:bash">
rake build
</pre>
