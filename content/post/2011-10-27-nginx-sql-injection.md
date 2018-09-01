---
title: nginx预防SQL注入的方法
date: 2011-10-27T02:19:11+00:00
layout: post
categories:
  - 我的世界
tags:
  - nginx
---

nginx作为一款轻量级的web服务器，以其高并发，高性能被多数IT公司采用作为替换apache服务器的首选程序。

前几天客户这边要求在nginx端做SQL注入防护的措施，可按nginx的功能来说，nginx不具备应用防火墙的功能。现在只能求助于伟大的google了，好在发现了两种办法。第一种办法就是过滤url基本的注入关键字，达到初步的预防措施。第二种办法，使用数据库防火墙，目前的可选软件可以考虑GreenSQL，相当的便捷，它提供Windows，Debian，RH/CentOS安装包，包括32位和64位两种版本，其作为数据库防火墙，接受web端发起的所有数据库连接请求，过滤SQL，假如属于注入等其他非法请求，GreenSQL会返回一个空数据集，不会影响到前端WEB应用，我在一台测试服务器上安装使用了GreenSQL Express,从速度先说，还是很不错的，以Wordpress为例，打开页面没有感觉到慢，感觉不到数据库防火墙的存在。

第一种方法：（来自google）
```
error_page 519 /519.html;
if ($request_uri ~* "(cost\()|(concat\()") {
	return 519;
}
if ($request_uri ~* "[+|(%20)]union[+|(%20)]") {
	return 519;
}
if ($request_uri ~* "[+|(%20)]and[+|(%20)]") {
	return 519;
}
if ($request_uri ~* "[+|(%20)]select[+|(%20)]") {
	return 519;
}
```
<!--more-->
将此段加入server块中即可。

第二种方法： GreenSQL 数据库防火墙，目前支持MySQL，MS SQL,PostgreSQL。

首先，打开[GreenSQL](http://www.greensql.net)官方网站，注册并下载GreenSQL Express版。之后，你的注册邮箱会收到serial key，安装时会有用。接下来开始安装工作：
```
rpm -ivh greensql.rpm
```

默认安装到`/opt/greensql`。接下来开始配置防火墙，使用浏览器打开`https://192.168.1.2:5000`,192.168.1.2的地址是安装GreenSQL的服务器地址。以用户名admin，默认密码pwd登录。之后设置本地Mysql的IP地址，端口和服务名。然后设置防火墙端配置，只选择要过滤的mysql服务名即可，这个服务名就是第一步设置的服务名。到这里配置完成。默认情况下，所有web前端应用要连接数据库的话，要先连接数据库防火墙比如127.0.0.1，端口默认是3305。配置和使用还是相当简单的。大家可以研究一下。

**重点：greensql 不支持 [Unix Domain Soket](http://en.wikipedia.org/wiki/Unix_domain_socket)，所以web前端应用的配置中要注意连接的服务器是IP地址，而不应该是localhost。**

_小技巧：可以修改Mysql的端口至其他端口，比如3308。让数据库防火墙使用3306,这样的话，前端应用只需要注意数据库服务器地址即可，不在担心端口问题。