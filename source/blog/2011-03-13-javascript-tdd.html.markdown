---
title: 采用TDD进行javascript开发
author: Tower He
date_created: 2011/03/13
categories: [programming]
tags: [javascript, qunit]
---

昨天与同事实现一个简单的地图定位功能，功能虽然简单，可是过程却是痛苦的，且程度是极度的。痛苦的主要原因源自于调试前端界面，也就是javascript部分。多年前也曾经写过许多javascript程序，那段历史可以追溯到ajax还未出现之前。以前写的时候因为自身、环境等各种原因，从来没有考虑过写javascript的单元测试。

READMORE

经过昨天，深刻地体会了一次如果没有对javascript做单元测试，调试的过程是多么的痛苦。由于时间问题，今天粗略的google了一下javascript单元测试库，发现主要有：
<ul>
<li>JSUnit - http://www.jsunit.net/</li>
<li>jspec - http://jspec.info/</li>
<li>jasmine - http://pivotal.github.com/jasmine/</li>
<li>QUnit - http://docs.jquery.com/Qunit</li>
<li>jsspec - http://code.google.com/p/jsspec/</li>
</ul>
在选择的时候原本准备选用jasmine，主要原因有三个：
<ol>
<li>jasmine（茉莉花）本人最喜欢的花；</li>
<li>jasmine的语法非常优美，非常适合BDD；</li>
<li>jasmine可以与rails集成。</li>
</ol>
但是最后由于使用rails g
jasmine:install时出错而打住了。最后选择了QUnit，因为他是jQuery提供的，jQuery有是那么的流行，定位地图很多也是基于jQuery写的，另外，有一个叫做<a
href="https://github.com/appendto/jquery-mockjax">mockjax</a>的扩展，可以方便的模拟ajax请求。

下面用一个简单的例子记录今日的学习过程，并展示用QUnit如何进行TDD。
<h3>需求定义</h3>
提供一个消息窗体函数，该函数可以将一个div渲染成一个消息窗口。函数需要知道将渲染的div是哪个；需要知道用什么内容去渲染该消息窗体。
<h3>TDD过程</h3>
<li>
<strong>实现如果没有找到div就什么都不干，返回false。</strong>
<p>
测试：
<pre class="brush:javascript">
module("notice-win");
test("检测是否有指定的div", function() {
  expect(1);
  var result = showNoticeWindow("#not_exists");
  ok(!result, "不存在时返回false");
});
</pre>
实现：
<pre class="brush:javascript">
function showNoticeWindow(div) {
  if($(div)) {
    // 渲染消息窗体
  } else {
    return false;
  }
}
</pre>
</p>
运行：
<iframe style="width: 100%; height: 300px"
src="http://jsfiddle.net/towerhe/yCkfW/17/embedded/"></iframe>
</li>
<li>
<strong>可以设置窗体标题</strong>
<p>
测试：
<pre class="brush:javascript">
module("通知窗体");

test("检测是否有指定的div", function() {
    expect(1);
    var result = showNoticeWindow("#not_exists", {});
    ok(!result, "不存在时返回false");
});

test("在通知窗体中显示标题", function() {
    expect(1);
    showNoticeWindow("#notice-window", {
        title: "我是标题"
    });
    equal($("#notice-window > h1.title").html(), "我是标题",
"设置标题");
});
</pre>
实现：
<pre class="brush:javascript">
function showNoticeWindow(div, options) {
    if ($(div)) {
        $.each($(div).children, function(idx, elmt) {
            elmt.remove();
        });
        if (options.title) {
            $(div).append('<h1 class="title">' + options.title +
'</h1>');
        }
    } else {
        return false;
    }
}
</pre>
</p>
运行：
<iframe style="width: 100%; height: 300px"
src="http://jsfiddle.net/towerhe/yCkfW/29/embedded/"></iframe>
</li>
<li>
<strong>完整的实现</strong>
<p>
测试：
<pre class="brush:javascript">
module("通知窗体");

test("检测是否有指定的div", function() {
    expect(1);
    var result = showNoticeWindow("#not_exists", {});
    ok(!result, "不存在时返回false");
});

test("在通知窗体中显示标题", function() {
    expect(1);
    showNoticeWindow("#notice-window", {
        title: "我是标题"
    });
    equal($("#notice-window > h1.title").html(), "我是标题",
"设置标题");
});

test("在通知窗体中显示消息", function() {
    expect(1);
    showNoticeWindow("#notice-window", {
        msg: "我是消息"
    });
    equal($("#notice-window > p.msg").html(), "我是消息", "设置消息");
});

test("如果没有detail，则不添加detail元素", function() {
    expect(1);
    showNoticeWindow("#notice-window", {});
    ok($("#notice-window > p.detail)").html() == null,
"没有detail元素");
});

test("在通知窗体中显示详细信息", function() {
    expect(1);
    showNoticeWindow("#notice-window", {
        detail: "我是详细信息"
    });
    equal($("#notice-window > p.detail").html(), "我是详细信息",
"设置详细信息");
});

test("如果没有操作，则不添加actions元素", function() {
    expect(1);
    showNoticeWindow("#notice-window", {});
    ok($("#notice-window > div.actions").html() == null,
"没有actions元素");
});

test("在通知窗体中显示操作", function() {
    expect(3);
    showNoticeWindow("#notice-window", {
        actions: [{
            href: "http://www.1.com",
            text: "www.1.com"}]
    });
    node = "#notice-window > div.actions";
    equal(1, $(node + " a").size(), "添加的操作数量");
    equal("http://www.1.com", $(node + " a").attr("href"),
"验证操作的链接是否正确");
    equal("www.1.com", $(node + " a").html(), "验证操作的文字是否正确");
});
</pre>
实现：
<pre class="brush:javascript">
function showNoticeWindow(div, options) {
    if ($(div)) {
        $.each($(div).children, function(idx, elmt) {
            elmt.remove();
        });
        if (options.title) {
            $(div).append('<h1 class="title">' + options.title +
'</h1>');
        }
        if (options.msg) {
            $(div).append('<p class="msg">' + options.msg + '</p>');
        }
        if (options.detail) {
            $(div).append('<p class="detail">' + options.detail +
'</p>');
        }
        if (options.actions) {
            $(div).append('<div class="actions"></div>');
            $.each(options.actions, function(idx, elmt) {
                $(div + ">div.actions").append('<a href="' + elmt.href +
'" >' + elmt.text + '</a>');
            });
        }
    } else {
        return false;
    }
}
</pre>
</p>
运行：
<iframe style="width: 100%; height: 300px"
src="http://jsfiddle.net/towerhe/yCkfW/30/embedded/"></iframe>
</li>
