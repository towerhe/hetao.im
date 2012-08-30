---
title: 虚拟化你的开发环境
author: Tower He
date_created: 2010/12/09
categories: [system]
tags: [vagrant, virtual machine]
---

<br/>
在开发过程中，开发环境的搭建常常要耗费大量的时间和人力。如何避免不同项目，开发环境之间的污染这更是一个让人头疼的问题。不少人应该都遇到过由于不同项目依赖同一库文件不同版本而导致的烦人问题。

另外，在一个团队中，也经常出现由于团队成员各自开发机操作系统、开发环境等的不同而导致的很多难于溯源的问题。由于存在这些问题，将开发环境虚拟化的思想很自然的就被引入到了开发过程中。相应的辅助虚拟化开发环境的工具也应运而生。<a
href="http://vagrantup.com" target="_blank">Vagrant</a>就是一个与<a
href="http://www.virtualbox.org"
target="_blank">VirtualBox</a>结合的虚拟化辅助工具。

通过使用虚拟化技术，我们可以很好的实现开发环境的管理。另外，借助虚拟化技术，我们能够
<ul>
<li>做到一次安装配置，多次、多处重复使用；</li>
<li>方便的隔离项目之间的开发环境；</li>
<li>确保开发团队开发环境的一致性。</li>
</ul>
下面简单说明一下如何使用Vagrant来将Ruby on Rails的开发环境进行虚拟化。
<h2>虚拟开发环境说明</h2>
本机操作系统Ubuntu Desktop 10.04.1 (Lucid Lynx)

虚拟开发环境Ubuntu Server 10.04.1 (Lucid Lynx)
<ul>
<li>liVirtualBox Addition 3.2.12 installed</li>
<li>RVM 1.1.3 installed</li>li<li>Ruby 1.9.2-p0 installed</li>
<li>Ruby 1.8.7-p249 installed</li>
    <li>JRuby 1.5.6 installed</li>
<li>Ruby Enterprice Edition 2010.02 indexstalled</li>
<li>Rails 3.0.3 installed</li>
<li>MySQL Server 5.1.47 indexstallednstalled</li>
<li>Nginx 0.8.53 installed</li>
<li>Passenger 3.0.1 installtalled</li>
<li>libmysqlclient-dev 5.1.47 installed</li>
<li>vim 7.2.li330 installed</li>
</ul>
<h2>本机安装VirtualBox 3.2.12</h2>
由于提供的虚拟机镜像使用的VirtualBox版本是3.2.12。如果本机已经安装了VirtualBox，请确认你的版本是否一致，如果较低，请下载安装文件更新；如果本机没有安装VirtualBox，请下载安装。
<ul>
<li>下载<a
href="http://www.VirtualBoxalbox.org/wiki/Linux_Downloads">VirtualBox
3.2.12</a>，并安装</li>
</ul>
<h2>本机安装Ruby</h2>
如果本机已经安装了ruby环境，可以跳过此步，但请确认你的版本：Ruby
1.8.7、Rubygems 1.3.7。
<ul>
<li> 安装ruby、rubygems
<pre class="brush:bash">$sudo apt-get installed ruby ruby-dev
rubygems</pre>
</li>
<li> 安装libopenssl-ruby
<pre classass="brush:bash">$sudo apt-get install libopenssl-ruby</pre>
</li>
<li> 更新rubygems
<pre class="brush:bash">$sudo gem install rubygems-update
$sudo /var/lib/gems/1.8/bin/update_rubygems</pre>
</li>
</ul>
<h2>安装Vagrant</h2>
<ul>
<li> 安装vagrant
是否使用sudo进行安装，取决于本机环境，及自己偏好。
<pre class="brush:bash">$sudo gem install vagrant</pre>
</li>
<li> 导入初始化虚拟开发环境
<pre class="brush:bash">
$vagrant box add rails-dev http://ftp.hetao.im/rails-dev-env-0.1.4.box

# 由于虚拟文件比较大，可以使用下载工具下载
# http://ftp.hetao.im/rails-dev-env-0.1.4.box
# 然后使用如下命令从本地导入初始化虚拟开发环境
$vagrant box add rails-dev /path/to/box
</pre>
</li>
</ul>
<h2>使用虚拟开发环境</h2>
<ul>
<li>创建用于存放Rails项目的目录
<pre class="brush:bash"bash>$mkdir /path/to/workspace/rails</pre>
</li>
<li>初始化虚拟开发环境
<pre class="brush:bash">$cd /path/to/workspace/rails
$vagrant init rails-dev</pre>
</li>
<li>启动虚拟开发环境
<pre class="brush:bash">$vagrant up</pre>
</li>
<li>登录虚拟开发环境并创建Rails项目
<pre  class="brush:bash">$vagrant ssh
# 虚拟开发环境中/vagrant目录
# 对应本机的/path/to/workspace/rails目录
$cd /vagrant
$rails new sample</pre>
</li>
<li>é<SNR>67_SuperTab('n')
置虚拟开发环境中的Nginx
<pre class="brush:bash"># 编辑/home/vagrant/nginx/conf/nginx.conf
# 在其中加入以下片段
server {
   listen 80;
   server_name sample.vm.local;
   root /vagrant/sample/public;
   passenger_enabled on;
   rails_env development;
}</pre>
</li>
<li>重启虚拟开发环境中的Nginx
<pre class="brush:brushash"># nginx默认随虚拟开发环境启动
$sudo /etc/init.d/nginx restart

# 以下是手动启动和关闭nginx的命令
$sudo /etc/init.d/nginx start
$sudo /etc/init.d/nginx stop</pre>
</li>
<li>访问虚拟开发环境中的Railsails应用
<ul>
<li>修改本机/etc/hosts文件，创建名称地址æ<SNR>67_SuperTab('n')
 射
<pre class="brush:bash">127.0.0.1    sample.vm.local</pre>
</li>
<li>启动浏览器，通过http://sample.vm.local:33000访问Rails应用</li>
</ul>
</li>

<listen>访问虚拟开发环境下的mysql数据库
<pre class="brush:bash">$mysql -uroot -P33306</pre>
</li>
</ul>
