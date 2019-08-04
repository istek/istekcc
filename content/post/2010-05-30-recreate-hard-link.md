---
title: 重建硬连接（hard link）
date: 2010-05-30T20:51:38+00:00
layout: post
categories:
  - Linux
tags:
  - hardlink
---
CentOS 5.5操作系统，在`/var/log/message`中出现

> Will not restore a file with more than one hard link (/etc/resolv.conf)
> No such file or directory

的错误。

可以看出是`/etc/resolv.conf`的硬连接，inode出错了，所以需要将其删除，重建硬连接即可。

其解决办法如下：
<!--more-->
```
$ ls -i /etc/resolv.conf #先得到inode信息
 16711694 /etc/resolv.conf
$ sudo find /etc -inum 16711694#查找硬连接
 Password:
/etc/sysconfig/networking/profiles/default/resolv.conf
/etc/resolv.conf
$ sudo /usr/sbin/lsof|grep resolv.conf #确认这个文件没有被打开或运行
$ sudo rm /etc/sysconfig/networking/profiles/default/resolv.conf #删除损坏的硬连接
$ sudo restorecon /etc/resolv.conf
$ sudo ln /etc/resolv.conf /etc/sysconfig/networking/profiles/default/resolv.conf #重建硬连接
```