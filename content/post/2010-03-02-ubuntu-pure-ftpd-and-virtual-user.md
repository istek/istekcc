---
title: Ubuntu Pure-FTPd服务安装配置+虚拟用户
date: 2010-03-02T21:05:08+00:00
layout: post
categories:
  - 我的世界
tags:
  - pure-ftpd
  - Ubuntu
---
**_Pure-FTPd FTP server：_**

[Pure-FTPd](http://www.pureftpd.org/project/pure-ftpd) is a fast, production-quality, standards-conformant FTP server based upon Troll-FTPd. Features include chrooted home directories, virtual domains, built-in ‘ls’, anti-warez system, configurable ports for passive downloads, FXP protocol, bandwidth throttling, ratios, fortune files, Apache-like log files, fast standalone mode, atomic uploads, text / HTML / XML real-time status report, virtual users, virtual quotas, privilege separation, SSL/TLS and more.

### 一、安装Pure-FTPd
```
$ sudo apt-get install pure-ftpd
$ sudo apt-get install pure-ftpd-mysql
```
<!--more-->
### 二、配置Pure-FTPd

#### 1．添加用户和组
```
$ sudo groupadd -g 2001 ftpgroup
$ sudo useradd -u 2001 -s /bin/false -d /dev/null -c "Pure-FTPd User" -g ftpgroup ftpuser
```

在上面创建用户的命令中，指定将`/bin/false`作为shell，使得该用户无法登录到服务器的shell环境，从而避免了很多安全问题；而将其HOME目录设置为`/dev/null`，也提高操作系统的安全。

#### 2．Chroot 设置

为了安全起见，我们要将每个FTP用户限制在Chroot环境中。为此，我们可以在其配置目录`/etc/pure-ftpd/conf`下面创建一个名为`ChrootEveryone`的文件，并将内容设置为`yes`：
```
$ sudo sh -c "echo 'yes' > /etc/pure-ftpd/conf/ChrootEveryone"
```

这样设置后，每个FTP用户自己的HOME目录就成了他的root目录，他无法达到上层目录中，也就无法浏览其它目录下的文件了。

#### 3．手工创建用户目录

下面的设置，告诉Pure-FTPd不要为用户自动创建HOME目录，我们将手工创建，后面我将会介绍web管理工具创建用户和HOME目录：
```
$ sudo sh -c "echo 'No' > /etc/pure-ftpd/conf/CreateHomeDir"
```

### 4．为Pure-FTPd 创建MySQL 数据库
```
$ mysql -u root -p

mysql> CREATE DATABASE ftpusers;
```

然后，创建一个名为`ftpadmin`的MySQL用户，密码为`ftpadminPassword`，并将数据库ftpusers的相关权限赋给该用户：
```
mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP ON ftpusers.* TO
 'ftpadmin'@'localhost' IDENTIFIED BY 'ftpadminPassword';
mysql> FLUSH PRIVILEGES;
```

然后，我们在该数据库中，创建一个users表和admin表，其SQL语句如下：
```
mysql> USE ftpusers;
mysql> CREATE TABLE IF NOT EXISTS `users` (
 `User` varchar(16) NOT NULL default '',
 `Password` varchar(32) NOT NULL default '',
 `Uid` int(11) NOT NULL,
 `Gid` int(11) NOT NULL,
 `Dir` varchar(128) NOT NULL default '',
 `QuotaFiles` int(10) NOT NULL default '500',
 `QuotaSize` int(10) NOT NULL default '30',
 `ULBandwidth` int(10) NOT NULL default '80',
 `DLBandwidth` int(10) NOT NULL default '80',
 `Ipaddress` varchar(15) NOT NULL default '*',
 `Comment` tinytext,
 `Status` enum('0','1') NOT NULL default '1',
 `ULRatio` smallint(5) NOT NULL default '1',
 `DLRatio` smallint(5) NOT NULL default '1',
 PRIMARY KEY (`User`),
 UNIQUE KEY `User` (`User`)
 ) ENGINE=MyISAM DEFAULT CHARSET=latin1;

mysql> CREATE TABLE IF NOT EXISTS `admin` (
 `Username` varchar(35) NOT NULL default '',
 `Password` char(32) NOT NULL default '',
 PRIMARY KEY (`Username`)
 ) ENGINE=MyISAM DEFAULT CHARSET=latin1;

mysql> quit
```

### 5．配置Pure-FTPd 的mysql.conf
```
$ sudo cp /etc/pure-ftpd/db/mysql.conf /etc/pure-ftpd/db/mysql.conf_orig
$ sudo vi /etc/pure-ftpd/db/mysql.conf
```

内容为：
```
MYSQLServer 127.0.0.1
MYSQLSocket /var/run/mysqld/mysqld.sock
MYSQLUser ftpadmin
MYSQLPassword ftpadminPassword
MYSQLDatabase ftpusers
MYSQLCrypt md5
MYSQLGetPW SELECT Password FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MYSQLGetUID SELECT Uid FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MYSQLGetGID SELECT Gid FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MYSQLGetDir SELECT Dir FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MySQLGetQTAFS SELECT QuotaFiles FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MySQLGetQTASZ SELECT QuotaSize FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MySQLGetRatioUL SELECT ULRatio FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MySQLGetRatioDL SELECT DLRatio FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MySQLGetBandwidthUL SELECT ULBandwidth FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
MySQLGetBandwidthDL SELECT DLBandwidth FROM users WHERE User="\L" AND Status="1" AND (Ipaddress = "*" OR Ipaddress LIKE "\R")
```

```
$ sudo chmod g=o= /etc/pure-ftpd/db/mysql.conf
$ sudo /etc/init.d/pure-ftpd-mysql restart
```

## 三、实现FTP用户的Web管理

### 1、安装User manager for PureFTPd
```
$ cd /var/www
$ sudo wget http://machiel.generaal.net/files/pureftpd/ftp_v2.1.tar.gz
$ sudo tar xvf ftp_v2.1.tar.gz
```

### 2、配置User manager for PureFTPd

我们来修改`/var/www/ftp/config.php`：
```
$ sudo vi /var/www/ftp/config.php
```

```
$LANG = “Chinese”;
$LocationImages = “images”;
$DBHost = “127.0.0.1″;
$DBLogin = “ftpadmin”;
$DBPassword = “ftpadminPassword”;
$DBDatabase = “ftpusers”;
$FTPAddress = “ubox.mytest.com:21″;
$DEFUserID = “2001″;
$DEFGroupID = “2001″;
$UsersFile = “/etc/passwd”;
$GroupFile = “/etc/group”;
$StyleSheet = “style/default.css.php”;
$EnableQuota = 1;
$EnableRatio = 1;
[…]
```

上述配置中，MySQL数据库的用户名、密码、数据库名及FTPAddress，请按自己的实际情况修改。最后两行设置，默认启用Quota和速率限制。

### 3、设置User manager for PureFTPd 管理员
```
$ mysql -u root -p
```

```
mysql> USE ftpusers;
mysql> INSERT INTO `admin` (`Username`, `Password`) VALUES
 ('ftpadmin', MD5('ftpadminPassword'));
mysql> quit
```

### 4、登录user manager进行用户管理

打开`http://localhost/ftp/index.php`输入ftpadmin的密码，进行管理。