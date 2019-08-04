---
title: 初试支付宝针对 Linux 下 Firefox 的安全控件
date: 2008-10-26T02:36:02+00:00
layout: post
categories:
  - WEB
tags:
  - firefox
---

看支付宝的官方博客上发布了支付宝针对 Linux 下 Firefox 的安全控件的消息便第一时间在 OwnLinux 上发布了相关的消息，然后就开始试用支付宝为我们 Linux 用户带来的这一礼物，同时和大家一起分享。先说一下我体验支付宝针对 Linux 下 Firefox 的安全控件的环境： Ubuntu 8.10 Intrepid Ibex (RC) ，FireFox 3.0.2。 在以前没有这个支付宝针对 Linux 下 Firefox 的安全控件的时候访问支付宝在登录框中不能输入密码进行登陆，点击“请点些输入密码”就会弹出一个对话框提示你下载 Firefox 的安全控件。不过，那个是针对 Window 下 Firefox 使用的，如果以后能够自动区分不同的系统并提供相应的软件下载就更好了。

### 安装支付宝针对 Linux 下 Firefox 的安全控件

下载: [支付宝安全控件 for Linux 平台 的 Firefox](http://blog.alipay.com/wp-content/2008/10/aliedit.tar.gz) ( MD5:**02240053d32688ed996e3a4788042801** )

使用方法：下载上述链接的文件，解压缩成两个文件:
```
$ tar -zxvf aliedit.tar.gz
```
推荐校验 MD5 值：
<!--more-->
```
$ md5sum aliedit.tar.gz
02240053d32688ed996e3a4788042801 aliedit.tar.gz
```
并将这两个文件复制到 `~/.mozilla/plugins` 目录：
```
$ cp aliedit.so aliedit.xpt ~/.mozilla/plugins
```

在终端中提示你没有 `~/.mozilla/plugins` 这个目录，你新建一个就可以了。
```
mkdir ~/.mozilla/plugins

cp aliedit.so aliedit.xpt ~/.mozilla/plugins
```

重新启动 Firefox 即可。

整个安装过程还是很简单的。

**安全提示**：下载前请做一下 MD5 校验。同时在官方站点发布正式的针对 Linux 下 Firefox 的安全控件之前，不要相信任何来自其他网站的安全控件。

以下是我在 Ubuntu Linux 中安装支付宝针对 Linux 下 Firefox 的安全控件完成后的一些截图：
