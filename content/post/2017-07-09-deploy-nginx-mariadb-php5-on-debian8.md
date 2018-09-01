---
title: Debian 8 VPS部署nmp环境
date: 2017-07-09T18:19:49+00:00
layout: post
categories:
  - 我的世界
tags:
  - Debian
  - 使用技巧
---

因为咱们一般购买的vps都是小内存，所以内存很重要，如果安装时不使用适合的调优参数，就会使得内存非常紧张，甚至，VPS也会无缘无故的出现各种问题。

因此可以使用一些其他的服务替代vps本身使用的软件，比如使用dropbear替代openssh，使用exim替代sendmail，mysql建议使用mariadb，这也是目前各个发行版都推荐的一个mysql分支了。本文也参考了古博的[这篇文章](https://www.gubo.org/debian-lemp-script/)。

<!--more-->

# 使用脚本替换系统软件

## 下载脚本

```
wget http://w3.gubo.org/pubfiles/tylemp/tylemp.sh
```

这个脚本主要是针对debian 8 32和64位系统，如果你是小内存，比如256以下，请使用下面的脚本

```
wget http://w3.gubo.org/pubfiles/tylemp/64m/tylemp.sh
```

授予权限

```
chmod a+x tylemp.sh
```

## 脚本的使用方法

这个脚本附带了一些参数，可以根据你的需求去使用，请看参数列表

| 命令                                    |   说明                                                         |
| --------------------------------------- | -------------------------------------------------------------- |
| tylemp.sh system                        | 优化系统，删除不需要组件，dropbear替代sshd                     |
| tylemp.sh exim4                         | 更轻量级邮件系统                                               |
| tylemp.sh mysql                         | 安装mysql(mariadb)                                             |
| tylemp.sh nginx                         | 安装nginx，默认一个进程，可调整                                |
| tylemp.sh php                           | 安装php5，包含php5-gd，可使用验证码                            |
| tylemp.sh stable                        | 安装上面所有，软件是debian官方stable源，版本较旧               |
| tylemp.sh wordpress www.yourdomain.com  | 一键安装wordpress, 数据库自动配置好。                          |
| tylemp.sh vhost www.yourdomain.com      | 一键安装静态虚拟主机                                           |
| tylemp.sh dhost www.yourdomain.com      | 一键安装动态虚拟主机，方便直接上传网站程序                     |
| tylemp.sh typecho www.yourdomain.com    | 安装typecho，提供数据库名，密码等自主添加完成安装              |
| tylemp.sh phpmyadmin www.yourdomain.com | 一键安装phpmyadmin 数据库管理软件，用http://www.yourdomain.com/phpMyAdmin访问 |
| tylemp.sh addnginx 2                    | 调整nginx进程，这里2表示调整后的进程数，请根据vps配置（cpu核心数）更改                      |
| tylemp.sh sshport 22022                 | 更改ssh端口号22022，建议更改10000以上端口。重启后生效。        |
| tylemp.sh rainloop www.yourdomain.com   | 增加Gmail的web客户端一键安装                                   |
| tylemp.sh carbon www.yourdomain.com     | 增加Carbon Forum的一键安装                                     |

## 配置文件位置

| 文件位置                  | 说明                                 |
| --------------------- | ---------------------------------- |
| /etc/nginx/nginx.conf | nginx配置文件，可根据vps的cpu核心数更改进程数最大限度利用 |
| /etc/php5/fpm/php.ini | php配置文件                            |
| ~/.my.cnf             | mysqlroot密码保存文件                    |
| /etc/nginx/conf.d/    | nginx下各个具体网站配置文件所在文件夹              |

## 脚本的项目地址

```
https://bitbucket.org/yylzcom/tylemp.sh
```

# PHP的补充部分

使用脚本安装php5，默认只是安装了`php5-fpm php5-mysqlnd php5-gd php5-mcrypt php5-tidy php5-curl` 这几个模块，可以安装再多一点。
```
apt install php5  php5-gd php5-json php5-fpm php5-mcrypt php5-mysqlnd php5-odbc php5-xmlrpc php5-xsl php5-sqlite php-pear
```

# Nginx的补充部分

可以使用官方stable源，首先下载签名，并且将签名文件加入秘钥链。
```
wget http://nginx.org/keys/nginx_signing.key && apt-key add nginx_signing.key
```

然后新增apt软件库list
```
vi /etc/apt/source.list.d/nginx.list
```

增加如下内容：
```
deb http://nginx.org/packages/debian/ jessie nginx
deb-src http://nginx.org/packages/debian/ jessie nginx
```

# 调优参数

可以参考我的另外一篇文章。
