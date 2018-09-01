---
title: 让WordPress支持中文用户名
date: 2009-08-11T07:37:25+00:00
layout: post
categories:
  - 我的世界
tags:
  - wordpress
---

今天朗清要求我添加一个中文名字的用户，记得好像加不了中文的，最后就找解决办法，终于在[这里](http://oibdc.cn/2008/12/%e5%a6%82%e4%bd%95%e8%ae%a9wordpress%e6%94%af%e6%8c%81%e4%b8%ad%e6%96%87%e7%94%a8%e6%88%b7%e5%90%8d/)发现了下面的这个办法，在WP 2.8.3上成功，其他版本应该没有问题。

打开`wp-includes/formatting.php`，找到
```
function sanitize_user( $username, $strict = false ) {
```

把`$strict`强制指定为`false`，即在`sanitize_user`函数的下一行加入：
```
$strict = false;
```
