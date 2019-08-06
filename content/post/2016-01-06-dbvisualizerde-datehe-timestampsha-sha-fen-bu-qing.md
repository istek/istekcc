---
title: DbVisualizer的date和timestamp傻傻分不清
date: 2016-01-06T06:25:45+00:00
layout: post
categories:
  - Windows
tags:
  - DbVisualizer
---
最近在使用DbVisualizer[^1]，发现这个工具比PL/SQL Developer好用多了，我们公司的产品主要是跑在oracle数据库的。

每当用DbVisualizer查询数据时，总是会把date类型的数据给我展现为timestamp格式的，很讨厌。于是google解决办法，终于在[dbvis](http://dbvis.org/forum/thread.jspa;jsessionid=424200BEDFF19E2E981A360168EC942F?messageID=12507&#12507)的论坛中发现了这个办法。

首先打开数据库链接对话框，

![1](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1451982644/hue08spsnepojkr86gbc.png)
<!--more-->
然后，点击`Properties`标签

![2](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1451982728/nmpyo4ushikrja3kkw1v.png)

点击左边列表的`driver properties`，然后设置`oracle.jdbc.mapDateToTimestamp`为`false`

![3](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1451982889/epc7zc61g4wsivea609f.png)

最后一步，不要勾选`data types`的`Handle DATE as TIMESTAMP`,最后重新连接，执行查询，oh，yeah，date终于是date了。

![4](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1451983040/vbbxodallwiwcb8rlpnw.png)


[^1]: DbVisualizer是一个完全基于JDBC的跨平台数据库管理工具，内置SQL语句编辑器（支持语法高亮），凡是具有JDBC数据库接口的数据库都可以管理，已经在Oracle, Sybase, DB2, Informix, MySQL, InstantDB, Cloudcape, HyperSonic ，Mimer SQL上通过测试。