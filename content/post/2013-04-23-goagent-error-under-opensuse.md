---
title: goagent在openSUSE 12.3下的问题总结
date: 2013-04-23T03:54:29+00:00
layout: post
categories:
  - 我的世界
tags:
  - opensuse
---

openSUSE 项目是由 Novell 发起的开源社区计划。 旨在推进 Linux 的广泛使用，提供了自由简单的方法来获得世界上最好用的 Linux 发行版之一openSUSE。该项目由SUSE等公司赞助，2011年Attachmate集团收购了Novell，并把Novell和SUSE做为两个独立的子公司运营。openSUSE 操作系统和相关的开源程序会被SUSE Linux Enterprise（比如 SLES 和 SLED）使用。openSUSE对个人来说是完全免费的，包括使用和在线更新。

GoAgent是google开发的一个基于Google App Engine的，全面兼容IE，FireFox，chrome的代理工具。

在运行goagent的时候，会出现下面的几个问题。
<!--more-->
1. py-gevent组件
```
sudo zypper install python-gevent
```
图形界面：yast-软件管理-搜索安装`python-gevent`
2. libnss-tools软件
```
sudo zypper install mozilla-nss-tools
```
图形界面：yast-软件管理-搜索安装`mozilla-nss-tools`
3. 安装根证书失败，显示`certutil: function failed: security library: bad database.`这个错误。因为没有初始化证书数据库，所以安装失败，重新初始化即可。
```
mkdir -p $HOME/.pki/nssdb
certutil -d $HOME/.pki/nssdb -N
```

