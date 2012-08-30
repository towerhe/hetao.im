---
title: 使用ConVirt2管理KVM虚拟机 - 单一主机篇
author: Tower He
date_created: 2011/05/06
categories: [system]
tags: [linux, convirt, cloud, kvm, ubuntu, virtual machine]
---

在[使用ConVirt管理基于KVM的虚拟机](http://hetao.im/2011/04/04/%E4%BD%BF%E7%94%A8convirt%E7%AE%A1%E7%90%86%E5%9F%BA%E4%BA%8Ekvm%E7%9A%84%E8%99%9A%E6%8B%9F%E6%9C%BA-%E5%AE%89%E8%A3%85%E7%AF%87)一文中，介绍了如何使用ConVirt2来搭建KVM环境，文中采用的是管理服务器ConVirt2与KVM服务器分离安装的方式。本文将介绍如何在一台主机上安装ConVirt2
+ KVM的环境。

READMORE

### 检测KVM支持

KVM目前支持Intel VT及AMD-V的原生虚拟技术。运行以下命令检测是否支持。

```:::bash
kvm-ok
```

### 主机配置

*主机型号：* Dell OptiPlex 980
*CPU：* Intel(R) Core(TM) i5 CPU 650 @ 3.20GHz, 1197 MHz
*内存：* 8G 非ECC双通道1333 MHz DDR3 SDRAM 
*硬盘：* 1T x 2 7200 RPM SATA 3.0 GB/秒 RAID 1

### 操作系统

Ubuntu Server 10.04.2 (Lucid Lynx) x86_64

### 配置第三方源

编辑软件包源列表文件/etc/apt/sources.list，追加http://archive.canonical.com/ubuntu
lucid partner，并更新软件索引：

```:::bash
sudo apt-get update
```

### 安装KVM、ConVirt2

* 安装KVM 

```:::bash
sudo apt-get install ssh kvm socat dnsmasq uml-utilities lvm2 expect
```

* 安装与配置ConVirt2：

通过安装convirture-tools来帮助你配置虚拟资源服务器，使得其可以通过ConVirt来进行方便的管理。该命令将创建一个公有的网桥，相关的脚本并将操作摘要写入/var/cache/convirt/server_info。

```:::bash
sudo apt-get install convirture-tools
```

安装相关依赖：

```:::bash
sudo convirt-tool install_dependencies
```

配置网络：

```:::bash
sudo convirt-tool setup
```

### 配置

运行sudo convirt-tool
setup后，默认创建的网桥是通过dhcp服务来配置的，因此需要手动修改网络配置文件，请参考下面的配置，来配置/etc/networks/interfaces

<pre>
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet manual

  auto br0
  iface br0 inet static
    bridge_ports eth0
    bridge_fd 0
    bridge_stp off
    bridge_maxwait 0
    address 192.168.1.240
    netmask 255.255.255.0
    gateway 192.168.1.1
</pre>

网络配置完成后，重启服务：

```:::bash
sudo /etc/init.d/networking restart
```

### 配置ssh

由于ConVirt使用VNC来连接虚拟机，且使用root用户，因此需要配置本机使其使用root用户连接本机ssh服务时，不需要输入口令。配置方法如下：

```:::bash
sudo cp /var/lib/convirt/identity/cms_id_rsa /root/.ssh/id_rsa
sudo cp /var/lib/convirt/identity/cms_id_rsa.pub
/root/.ssh/authorized_keys
```

尝试用root用户连接本机ssh服务：

```:::bash
ssh root@192.168.1.240
```

如果不需要口令验证，则说明已经配置成功，否则使用如下命令查看失败原因：

```:::bash
ssh root@192.168.1.240 -v
```

### 访问ConVirt2管理系统

打开浏览器，访问http://192.168.1.240:8081，即可看到ConVirt2管理系统登录界面，默认的用户名：admin，密码：admin。
