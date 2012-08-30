---
title: 使用ConVirt管理基于KVM的虚拟机 - 安装篇
author: Tower He
date_created: 2011/04/04
categories: [system]
tags: [convirt, virtual machine, cloud, linux, ubuntu, kvm]
---

### 名词解释：

[ConVirt](http://www.convirture.com/index.php) -
是一个直观的、图形化的虚拟机管理工具，可以对虚拟机的整个生命周期进行管理。

[KVM（Kernel-based
Virtual Machine）](http://www.linux-kvm.org/page/Main_Page) -
是使用于Linux内核中的虚拟化基础设施。KVM目前支持Intel
VT及AMD-V的原生虚拟技术。KVM在2007年2月被导入Linux
2.6.20内核中。它也被引入FreeBSD。在Mac OS X中，也可以看见KVM。

READMORE

### 资源：

*三台计算机（CPU - Intel
VT或AMD-V）*，一台作为管理终端，一台作为被管理的虚拟资源服务器（虚拟机安装在机上），另外一台用来安装ConVirt。

*操作系统安装盘*，Ubuntu Server 10.04.2 (Lucid Lynx)
x86_64和Ubuntu Desktop 10.04.2 (Lucid Lynx) x86_64。

### 安装步骤：

* *为三台计算机安装操作系统*

用于管理的计算机安装Ubuntu Desktop 10.04.2 (Lucid Lynx)
x86_64，而用于安装ConVirt和用于安装虚拟机的计算机则安装Ubuntu Server
10.04.2 (Lucid Lynx) x86_64。

* *配置第三方源*

编辑软件包源列表文件/etc/apt/sources.list，追加http://archive.canonical.com/ubuntu
lucid partner，并更新软件索引：

```bash
sudo apt-get update
```
* *安装被管理的虚拟资源服务器（使用两台UbuntuServer中的一台）*

  * 安装KVM 

```bash
sudo apt-get install ssh kvm socat dnsmasq uml-utilities lvm2 expect
```

  * 配置虚拟资源服务器：

  通过安装convirture-tools来帮助你配置虚拟资源服务器，使得其可以通过ConVirt来进行方便的管理。该命令将创建一个公有的网桥，相关的脚本并将操作摘要写入/var/cache/convirt/server_info。

```bash
sudo apt-get install convirture-tools
```

  安装相关依赖：

```bash
sudo convirt-tool install_dependencies
```

  配置网络：

```bash
sudo convirt-tool setup
```

* *安装和配置ConVirt*

安装：

```bash
sudo apt-get install convirt2
```

配置防火墙，使得可以通过VNC来连接虚拟机控制台。

```bash
iptables -I INPUT -m state --state NEW -p tcp --dport 6900:6999 -j ACCEPT
```

配置VNC：
添加SSH
Key，使得从ConVirt到被管理的虚拟资源服务器的SSH连接采用Key的方式进行认证。

```bash
cp /var/lib/convirt/identity/cms_id_rsa.pub /root/.ssh/id_rsa.pub
cp /var/lib/convirt/identity/cms_id_rsa /root/.ssh/id_rsa
scp /var/lib/convirt/identity/cms_id_rsa.pub
root@managed-server/root/.ssh/cms_id_rsa.pub
ssh root@managed-server
cat ~/.ssh/cms_id_rsa.pub >> ~/.ssh/authorized_keys
```

### 登录ConVirt管理系统

启动Convirt：

```bash
sudo convirt-ctl start
```

登录Convirt（用户名/密码 - admin/admin）：http://localhost:8081

### 参考资料：

* [http://www.convirture.com/wiki/index.php?title=Convirt2_Installation](http://www.convirture.com/wiki/index.php?title=Convirt2_Installation)
* [https://help.ubuntu.com/community/Repositories/Ubuntu#Third-Party Software Tab](https://help.ubuntu.com/community/Repositories/Ubuntu#Third-Party Software Tab)
