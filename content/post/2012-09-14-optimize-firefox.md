---
title: Firefox简单优化加速浏览
date: 2012-09-14T02:08:00+00:00
layout: post
categories:
  - WEB
tags:
  - firefox
---

目前firefox和google chrome都不错，但是我个人用firefox还是比较多，主要是扩展确实多，速度和内存消耗方面已经比以前好很多了。

ok，接下来我说一下简单的优化，让它快一点。打开你的FF，输入`about:config`，打开配置界面。

1.禁止IPv6
```
network.dns.disableIPv6 = true
```
2.Pipelining
```
network.http.pipelining = true network.http.pipelining.maxrequests= 150 network.http.proxy.pipelining = true
```
3.加速页面加载时间
```
nglayout.initialpaint.delay = 0 content.notify.interval = 0
```
4.Linux下缓存设置到`/dev/shm`中
```
browser.cache.disk.parent_directory = /dev/shm/firefox
```
比较简单的优化。
<!--more-->
FoxyProxy：

1.建立新的代理服务器;

2.添加模式订阅；

[GFWlist](https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt)，更新时间600分钟，AutoProxy，Base64编码

3.启用快速添加，增加刚才添加的代理服务器；

4.工作模式选择使用基于其预定义模板的代理服务器；

5.不显示Gmail图标和`https://ssl.gstatic.com`地址有关系。