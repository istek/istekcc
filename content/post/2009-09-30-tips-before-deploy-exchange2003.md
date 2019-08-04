---
title: 关于部署Exchange 2003的几个需要注意的地方
date: 2009-09-30T19:46:52+00:00
layout: post
categories:
  - windows
tags:
  - exchange2003
---
在昨天晚上部署Exchange 2003的实验失败之后，总结了下面几点。

1. 操作系统要求：Exchange需要部署在Windows 2000 sp3+或Windows 2003系列操作系统上。
2. 系统服务要求：要部署Exchange的服务器需要安装有IIS,NNTP,SMTP这些服务。
3. 软件要求：.net框架，asp.net
4. DNS要求：部署Exchange之前，要有可用的DNS服务，最好将DNS安装于单独的一台服务器上，至于安装手册以及设置部分，可从网络上获得很多相关资料。
5. 域要求：Exchange在部署之前，会扩展你的森林，添加对Exchange各个对象的支持；以及域扩展，给Exchange服务器分配一些特定的权限保证Exchange的正常运行。
6. 账户权限要求：扩展森林的部署权限必须是Enterprise和Schema Admin组的成员；域扩展的部署权限必须是Domain Admin 组的成员和本地计算机管理员；部署Exchange2003的账户必须是在组织级别具有 Exchange 管理员（完全控制）权限并且是计算机本地管理员的帐户。
7. 部署之前，一定要使用Server 2003自带的support tools，进行域，网络等各方面的检查，因为部署是否成功，依赖于此。
8. 部署完成之后，需要将Exchange 2003的SP2补丁打上，这个在安装的时候会显示不兼容。也就是说Windows 2003 SP2必须要安装对应的Exchange 2003 sp2。
<!--more-->
昨天晚上的实验，搞到12点多了，最终我失败了。因为我是第一次部署这种服务，背景知识和要求了解的不够多。之后用灾难恢复，却发现无法启用Information Store服务，可能就是某一步错了，搜索了相关资料，说是与路由组有关，可在系统管理器中却未找到该组。难道要启用路由协议？!

今天晚上继续这个实验，我要成功！