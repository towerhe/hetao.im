---
title: Install VirtualBox Guest Additions on Ubuntu Server
author: Tower He
date_created: 2010/12/02
categories: [system]
tags: [linux, ubuntu, virtual machine]
---

<ol>
<li><strong>Install build-essential and linux-Heeaders</strong>
$sudo apt-get update
$sudo apt-get install build-essential linux-headers-`uname -r`</li>
<li><strong>Install DKMS</strong>
$sudo apt-get install DKMSms</li>
<li><strong>Add VBoxGuestAdditions.iso</strong>
Menu -&gt; Devices -&gt; Install Guest Additions (Host+D)</li>
<li><strong>Mount CDROM</strong>
$sudo mkdir /media/cdrom
$sudo mount /dev/cdrom /media/cdrom</li>
<li><strong>Install VirtualBox Guest Additions</strong>
$sudo /media/cdrom/VBoxGuestAdditionsoxLinuxAdditions-x86.run</li>
</ol>

READMORE
