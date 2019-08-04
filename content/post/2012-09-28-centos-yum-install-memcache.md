---
title: CentOS 6.3YUM安装MEMCACHE
date: 2012-09-28T19:12:00+00:00
layout: post
categories:
  - Linux
tags:
  - memcache
---
Memcache是一个高性能的分布式的内存对象缓存系统，通过在内存里维护一个统一的巨大的hash表，它能够用来存储各种格式的数据，包括图像、视频、文件以及数据库检索的结果等。简单的说就是将数据调用到内存中，然后从内存中读取，从而大大提高读取速度。Memcache是danga的一个项目，最早是LiveJournal 服务的，最初为了加速 LiveJournal 访问速度而开发的，后来被很多大型的网站采用。

Memcached是以守护程序方式运行于一个或多个服务器中，随时会接收客户端的连接和操作。

安装步骤：

1. 由于CentOS系统默认源没有memcache安装包，因此需要导入第三方的源。请确认已经导入remi源，epel源。此过程请自行搜索。
2. yum安装Memcache服务器与php扩展。
```
yum install memcached php-pecl-memcache
```
<!--more-->
3. 设置memcached服务开机自动启动.
```
chkconfig --level 2345 memcached on
```

4. 启动memcached服务
```
service memcached start
```

5. web服务重启，加载memcache的php模块。
```
service httpd restart
```

若使用nginx+php-fpm，请重启php-fpm
```
servcie php-fpm restart
```

6. 检查memcache是否成功加载。

使用`phpinfo ();`

Memcached的默认端口为`11211`，因此在php中使用此端口即可。下面顺便给出个清除memcache所有缓存内容的方法：
```
nc localhost 11211
```

然后输入：
```
flush_all quit
```

即可。

配置位置的位置在`/etc/sysconfig/memcached`，可以在里面设置监听端口，并发数，缓存大小，运行用户等。