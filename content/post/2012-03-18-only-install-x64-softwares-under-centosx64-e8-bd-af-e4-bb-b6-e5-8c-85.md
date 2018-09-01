---
title: 在64位的 CentOS 上只安装64位的软件包
date: 2012-03-18T08:07:08+00:00
layout: post
categories:
  - 我的世界
tags:
  - CentOS
---

在 64位版本的 CentOS 5.4 上使用 yum 安装软件包的时候如果不小心的话会同时安装 i386 和 x86_64 版本的软件，如下面安装的 httpd-devel 就有 i386 和 x86_64 两个版本：
```
[root@localhost /]# yum install httpd-devel
... Dependencies Resolved
================================================================================
Package Arch Version Repository Size
================================================================================
Installing:
httpd-devel i386 2.2.3-31.el5.centos.2 updates 147 k
httpd-devel x86_64 2.2.3-31.el5.centos.2 updates 147 k Installing for dependencies:
 ...
```
<!--more-->
同时安装32位和64位版本的软件虽然不是什么错，也不会对系统造成什么问题，但是这样会浪费硬盘空间，而且显得系统臃肿、不干净。如果想要保持一个纯的64位系统、在64位 CentOS 上避免安装32位软件包的话很容易，只需要在 `yum.conf`加上一行过滤掉 i386, i686 的软件包就可以了：
```
[root@localhost /]# vi /etc/yum.conf
[main]
 ...
exclude=*.i?86
```

然后删除系统上已经安装的 i386/i686 包：

```
[root@localhost /]# yum remove \*.i\?86
```
