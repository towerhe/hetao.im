---
title: 使用tklpatch自定义Ubuntu系统安装盘
author: Tower He
date_created: 2011/07/07
categories: [system]
tags: [linux, cdrom, tklpatch, ubuntu]
---

###实验环境

*硬件配置*

<table>
  <tr>
    <td style="text-align:right"><strong>主机型号：</strong></td><td>Dell OptiPlex 380</td>
  </tr>
  <tr>
    <td style="text-align:right"><strong>CPU：</strong></td><td>Intel(R) Core(TM)2 Duo CPU  E7500  @ 2.93GHz</td>
  </tr>
  <tr>
    <td style="text-align:right"><strong>内存：</strong></td><td>DDR3 SDRAM，1066 Mhz</td>
  </tr>
  <tr>
    <td style="text-align:right"><strong>硬盘：</strong></td><td>500G 7200 RPM SATA</td>
  </tr>
</table>

*软件环境*

<table>
  <tr>
    <td style="text-align:right"><strong>操作系统：</strong></td><td>Ubuntu Desktop 10.04.2 (Lucid Lynx) x86_32</td>
  </tr>
</table>

READMORE

### 安装tklpatch

*将Turnkey仓库添加到源列表中*

```:::bash
deb http://archive.turnkeylinux.org/ubuntu lucid main universe
```

*安装turnkey-keyring*

```:::bash
wget http://code.turnkeylinux.org/turnkey-keyring/turnkey-release-keyring.gpg
sudo apt-key add turnkey-release-keyring.gpg
```

*安装tklpatch*

```:::bash
sudo apt-get update
sudo apt-get install tklpatch
```

### 目录结构

```
tklpatch-rails                       # http://github.com/towerhe/tklpatch-rails
.
├── conf                             # 应用patch时将自动执行的脚本文件
│   ├── post-debs                    # 安装完deb包后执行的脚本文件
│   ├── post-overlay                 # 安装完overlay文件后执行的脚本文件
│   ├── pre-debs                     # 安装deb包前执行的脚本文件
│   └── pre-overlay                  # 安装overlay前执行的脚本文件
├── debs                             # 该目录存放需要安装的deb包
│   └── needed-deb-pkg.deb           # 该deb包将使用dpkg -i进行自动安装
├── overlay                          # 该目录存放被直接复制到目标根目录的文件或目录，如果目标存在，则直接覆盖
│   └── etc                          # 将被复制到/etc
│       ├── apt                      # 将被复制到/etc/apt
│       │   └── sources.list         # 将被复制到/etc/apt/sources.list
│       ├── init.d                   # 将被复制到/etc/init.d/
│       │   └── nginx                # 将被复制到/etc/init.d/nginx
│       └── nginx                    # 将被复制到/etc/nginx
│           ├── nginx.conf           # 将被复制到/etc/nginx/nginx.conf
│           ├── sites-available      # 将被复制到/etc/nginx/sites-available
│           │   └── default          # 将被复制到/etc/nginx/default
│           └── sites-enabled        # 将被复制到/etc/sites-enabled
├── patches                          # 嵌套的tklpatch，被嵌套的patch将按照目录名字母排序执行
│   ├── 1_mysql
│   │   ├── conf
│   │   ├── overlay
│   │   │   └── etc
│   │   │       └── apt
│   │   │           └── sources.list
│   │   ├── README.textile
│   │   └── VERSION
│   └── 2_rvm
│       ├── conf
│       ├── overlay
│       │   └── etc
│       │       └── apt
│       │           └── sources.list
│       ├── README.textile
│       └── VERSION
├── README.textile
└── VERSION
```

* 该目录结构取自[tklpatch-rails](http://github.com/towerhe/tklpatch-rails)项目。
* tklpatch可以自我嵌套，而且可以多层次嵌套。
* 如果不需要细分脚本执行过程，可以使用conf脚本文件替代，该文件将在应用了overlay之用执行。
* 应用patch时的执行顺序： patches => pre-debs => debs => post-debs => pre-overlay => overlay => post-overlay

### 应用tklpatch

首先需要从[Turnkey Linux](http://www.turnkeylinux.com)下载需要应用patch的基础iso文件。如：[turnkey-core-11.1-lucid-x86.iso](http://downloads.sourceforge.net/project/turnkeylinux/turnkey-core/11.1-lucid-x86/turnkey-core-11.1-lucid-x86.iso)。
然后执行tklpatch命令应用patch。

```:::bash
  sudo tklpatch turnkey-core-11.1-lucid-x86.iso path/to/your/tklpatch
```

### 其他常用命令

* tklpatch： 将tklpatch应用到基础iso文件

```:::bash
  tklpatch path/to/turnkey-core.iso path/to/your/tklpatch
```

* tklpatch-apply： 将tklpatch应用到的解压的root文件系统

```:::bash
  tklpatch-apply rootfs-dir patch-dir|patch.tar.gz
```

* tklpatch-apply-conf： 在解压的root文件系统中，执行conf脚本

```:::bash
  tklpatch-apply-conf rootfs-dir conf
```

* tklpatch-apply-debs： 在解压的root文件系统中，安装debs

```:::bash
  tklpatch-apply-debs rootfs-dir debs-dir
```

* tklpatch-apply-overlay： 复制overlay下的文件和目录到解压的root文件系统

```:::bash
  tklpatch-apply-overlay rootfs-dir overlay-dir
```

* tklpatch-bundle： 将创建的tklpatch项目打包

```:::bash
  tklpatch-bundle patch-dir
```

* tklpatch-chroot： 将当前root文件系统切换到解压的root文件系统

```:::bash
  tklpatch-chroot root-dir [ command ...]
```

* tklpatch-example： 创建tklpatch项目基础目录结构

```:::bash
  tklpatch-example [options] new-patch-dir
```

* tklpatch-extract-iso： 解压基础iso文件

```:::bash
  tklpatch-extract-iso isofile
```

* tklpatch-geniso： 根据准备好的cdroot创建iso文件

```:::bash
  tklpatch-geniso cdroot-dir [newimage.iso]
```

* tklpatch-prepare-cdroot： 根据解压的root文件系统创建cdroot

```:::bash
  tklpatch-prepare-cdroot rootfs-dir [cdroot-dir]
```

### 参考资料

* [TKLPatch - a simple appliance customization mechanism](http://www.turnkeylinux.org/docs/tklpatch)
* [tklpatch-rails](http://github.com/towerhe/tklpatch-rails)

