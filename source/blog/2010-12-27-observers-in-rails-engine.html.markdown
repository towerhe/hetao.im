---
title: 如何在Rails Engine中实现Observer
author: Tower He
date_created: 2010/12/27
categories: [programming]
tags: [rails, observer, engine, ruby]
---

Rails::Engine使我们可以将一个完整的Rails应用打包，并在不同的应用中使用。在Rails
3中，每一个Rails::Application都是一个Engine，这使得我们可以非常容易的实现将我们的应用模块化。如何开发Rails::Engine已经有许多现有的开发教程，下面几个地址可以为我们开发Engine提供帮助：

READMORE

* [Rails::Engine API](http://edgeapi.rubyonrails.org/classes/Rails/Engine.html)
* [Rails 3 Plugins - Part 1 - The Big Picture](http://www.themodestrubyist.com/2010/03/01/rails-3-plugins---part-1---the-big-picture/)
* [Rails 3 Plugins - Part 2 - Writing an Engine](http://www.themodestrubyist.com/2010/03/05/rails-3-plugins---part-2---writing-an-engine/)
* [Rails 3 Plugins - Part 3 - Rake Tasks, Generators, Initializers, Oh-My!](http://www.themodestrubyist.com/2010/03/16/rails-3-plugins---part-3---rake-tasks-generators-initializers-oh-my/)
* [Rails 3 Plugins - Part 4 - More on Generators](http://www.themodestrubyist.com/2010/03/22/rails-3-plugins---part-4---more-on-generators/)

### ActiveRecord::Observer
[ActiveRecord::Observer](http://api.rubyonrails.org/classes/ActiveRecord/Observer.html)顾名思义，是采用的观察者模式进行设计。它对ActiveRecord模型的callbacks做出响应，就好像触发器一样。这样使得我们可以将一些不应在模型中存在的行为移出模型类，为它安置恰当的位置。例如我们可能需要在用户注册成功后发送邮件到用户的邮箱这样的行为。
在Rails Engine中实现Observer，包括一下两个步骤：

* *定义Observer*

在你的项目中创建app/observers目录，用于保存observers。如果你希望将你的observers定义在自己的module中，你也可以修改你的目录为app/observers/{module_name}，来存放observers。

<pre>
  class ContactObserver &lt; ActiveRecord::Observer
    def after_create(contact)
      contact.logger.info('New contact added!')
    end

    def after_destroy(contact)
      contact.logger.warn("Contact with an id of #{contact.id} was destroyed!")
    end
  end
</pre>

* *激活Observer*

在Engine中定义需要激活的Observer。此步非常重要，因为observers必须被显式的激活才会生效。

<pre>
  class Engine &lt; Rails::Engine
    # 如果你的Observer被包裹在一个module中
    # 那么用字符串来替代symbol即可
    # 如：“MyModule::ContactObserver”
    config.active_record.observers = :contact_observer
  end
</pre>
