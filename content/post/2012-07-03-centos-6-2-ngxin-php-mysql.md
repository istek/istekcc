---
title: 在CentOS 6.2上安装Nginx+PHP5(PHP-FPM)+MySQL
date: 2012-07-03T08:01:34+00:00
layout: post
categories:
  - Linux
tags:
  - nginx
  - php
  - mysql
---

### 1.使用非官方软件源
```
[root@localhost /]# rpm --import https://fedoraproject.org/static/0608B895.txt
[root@localhost /]# rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-7.noarch.rpm
[root@localhost /]# rpm –import http://rpms.famillecollet.com/RPM-GPG-KEY-remi
[root@localhost /]# rpm -ivh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
[root@localhost /]# yum install yum-priorities
```

编辑`/etc/yum.repos.d/epel.repo`文件，启用这个源，使其优先级最高
```
[epel]
name=Extra Packages for Enterprise Linux 6 - $basearch
#baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
failovermethod=priority
enabled=1 p
riority=10
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
[...]
```
<!--more-->
同样的，也需要对remi源进行优先级编辑和启用
```
[remi] name=Les RPM de remi pour Enterprise Linux $releasever - $basearch
#baseurl=http://rpms.famillecollet.com/enterprise/$releasever/remi/$basearch/
mirrorlist=http://rpms.famillecollet.com/enterprise/$releasever/remi/mirror
enabled=1
priority=10
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
failovermethod=priority

[remi-test]
name=Les RPM de remi en test pour Enterprise Linux $releasever – $basearch
#baseurl=http://rpms.famillecollet.com/enterprise/$releasever/test/$basearch/
mirrorlist=http://rpms.famillecollet.com/enterprise/$releasever/test/mirror
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-remi
```

### 2.安装MYSQL
```
[root@localhost /]# yum install mysql mysql-server
```

设置系统启动时，在235的运行级运行mysqld守护进程
```
[root@localhost /]# chkconfig mysqld on
[root@localhost /]# /etc/init.d/mysqld start
```

需要按系统配置优化my.cnf文件，之后设置root的密码，否则任何人都能访问你的数据库。
```
[root@localhost /]# mysql_secure_installation
NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL SERVERS IN PRODUCTION USE! PLEASE READ EACH STEP CAREFULLY!
In order to log into MySQL to secure it, we'll need the current password for the root user. If you've just installed MySQL, and you haven't set the root password yet, the password will be blank, so you should just press enter here. Enter current password for root (enter for none):
[....]
```

### 3.安装nginx

nginx在epel源中有包，可以直接yum安装。
```
yum install nginx
```

之后创建nginx的系统启动连接并启动它
```
chkconfig nginx on
service nginx start
```

之后在浏览器中打开服务器的IP地址，应该能看到nginx的欢迎页面。

![`](https://static.howtoforge.com/images/nginx_php5_mysql5_centos_6.2/big/1.png)

### 4.安装php和php-fpm
```
[root@localhost /]# yum install php-fpm php-cli php-mysql php-gd php-imap php-ldap php-odbc php-pear php-xml php-xmlrpc php-eaccelerator php-magickwand php-magpierss php-mbstring php-mcrypt php-mssql php-shout php-snmp php-soap php-tidy
```

其中使用的php-x扩展，可以根据自己的需要添加。之后，编辑`php.ini`文件，修改`cgi.fix_pathinfo`为`0`。为什么要这么做？请查阅该[文档](http://wiki.nginx.org/Pitfalls)。
```
vi /etc/php.ini
```

```
cgi.fix_pathinfo = 0
date.timezone = "Asia/Shanghai"
```

### 5.配置nginx

关于配置nginx可以查阅官方的两个例子，[例子1](http://wiki.codemongers.com/NginxFullExample)，[例子2](http://wiki.codemongers.com/NginxFullExample2)。

### 6.让PHP-FPM使用unix套接字

默认情况下，php-fpm使用`127.0.0.1:9000`监听请求，当然也可以使用unix套接字，这样可以避免TCP的额外流量。编辑`/etc/php-fpm.d/www.conf`文件就可以了。
```
[...]
;listen = 127.0.0.1:9000
listen = /dev/shm/php5-fpm.sock
[...]
```

之后，重启php-fpm。
```
service php-fpm restart
```

之后编辑nginx的虚拟主机配置文件，将fastcgi_pass这一行内容改为unix套接字。
```
[...]
location ~ \.php$ {
	try_files $uri =404;
	root /usr/share/nginx/html;
	fastcgi_pass unix:/dev/shm/php5-fpm.sock;
	fastcgi_index index.php;
	fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name; include fastcgi_params;
}
[...]
```

最后，重启nginx。
```
service nginx reload
```
