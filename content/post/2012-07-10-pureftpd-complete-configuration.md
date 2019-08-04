---
title: Pureftpd完全配置
date: 2012-07-10T07:52:00+00:00
layout: post
categories:
  - Linux
tags:
  - pureftpd
---
这篇文档描述了在CentOS 6.2下安装Pureftpd服务器，包括使用MySQL的虚拟用户，磁盘配额，带宽管理，TLS加密会话和集成病毒检查功能。

在文档开始之前，假设你已经安装好了基本的CentOS 6.2操作系统，且网络正常，安装并配置好了MySQL数据库。如果没有，下面简单说明一下。<!--more-->

# 安装MySQL数据库服务器
```
yum install mysql mysql-server
```

使MySQL随系统启动，并启动MySQL。
```
chkconfig --levels 235 mysqld on
service mysqld start
```
配置MySQL
```
mysql_secure_installation
```
<!--more-->
# 安装具有MySQL支持的PureFTPd
```
yum install pure-ftpd
```

之后我们创建一个所有虚拟用户映射的FTP用户组（ftpgroup）和用户（ftpuser）。用你系统中空余的号替代groupid和userid 2001。
```
groupadd -g 2001 ftpgroup
useradd -u 2001 -s /bin/false -d /bin/null -c "pureftpd user" -g ftpgroup ftpuser
```

# 创建pureftpd使用的mysql库

现在我们创建一个pureftpd数据库和一个pureftpd守护进程用于连接pureftpd数据库的mysql用户。
```
mysql -u root -p
```
可以更改ftpdpass，这是mysql用户的密码。
```sql
mysql> CREATE DATABASE pureftpd;
mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, DROP ON pureftpd.* TO 'pureftpd'@'localhost' IDENTIFIED BY 'ftpdpass';
mysql> FLUSH PRIVILEGES;

mysql> USE pureftpd;

mysql> CREATE TABLE ftpd (
	User varchar(16) NOT NULL default '',
	status enum('0','1') NOT NULL default '0',
	Password varchar(64) NOT NULL default '',
	Uid varchar(11) NOT NULL default '-1',
	Gid varchar(11) NOT NULL default '-1',
	Dir varchar(128) NOT NULL default '',
	ULBandwidth smallint(5) NOT NULL default '0',
	DLBandwidth smallint(5) NOT NULL default '0',
	comment tinytext NOT NULL,
	ipaccess varchar(15) NOT NULL default '*',
	QuotaSize smallint(5) NOT NULL default '0',
	QuotaFiles int(11) NOT NULL default 0,
	PRIMARY KEY (User),
	UNIQUE KEY User (User) ) ENGINE=MyISAM;
mysql> quit;
```

# 配置PureFTPD
```
vi /etc/pure-ftpd/pure-ftpd.conf
```
修改如下配置小节
```
[...]
ChrootEveryone yes
MySQLConfigFile /etc/pure-ftpd/pureftpd-mysql.conf
CreateHomeDir yes
[...]
```

ChrotEveryone设置可以使Pureftpd将每一个虚拟用户锁定在自己的home目录里，这样的话，虚拟用户就不能浏览自己的home目录以外的目录和文件了。CreateHomeDir的作用是当虚拟用户的home目录不存在时，它使PureFTPD创建虚拟用户的home目录。

现在编辑`/etc/pure-ftpd/pureftpd-mysql.conf`
```
cp /etc/pure-ftpd/pureftpd-mysql.conf /etc/pure-ftpd/pureftpd-mysql.conf_orig
cat /dev/null > /etc/pure-ftpd/pureftpd-mysql.conf
vi /etc/pure-ftpd/pureftpd-mysql.conf
```
加入下面的内容：
```
MYSQLSocket      /var/lib/mysql/mysql.sock
#MYSQLServer     localhost
#MYSQLPort       3306
MYSQLUser       pureftpd
MYSQLPassword   ftpdpass
MYSQLDatabase   pureftpd
#MYSQLCrypt md5, cleartext, crypt() or password() - md5 is VERY RECOMMENDABLE uppon cleartext
MYSQLCrypt      md5
MYSQLGetPW      SELECT Password FROM ftpd WHERE User="\L" AND status="1" AND (ipaccess = "*" OR ipaccess LIKE "\R")
MYSQLGetUID     SELECT Uid FROM ftpd WHERE User="\L" AND status="1" AND (ipaccess = "*" OR ipaccess LIKE "\R")
MYSQLGetGID     SELECT Gid FROM ftpd WHERE User="\L"AND status="1" AND (ipaccess = "*" OR ipaccess LIKE "\R")
MYSQLGetDir     SELECT Dir FROM ftpd WHERE User="\L"AND status="1" AND (ipaccess = "*" OR ipaccess LIKE "\R")
MySQLGetBandwidthUL SELECT ULBandwidth FROM ftpd WHERE User="\L"AND status="1" AND (ipaccess = "*" OR ipaccess LIKE "\R")
MySQLGetBandwidthDL SELECT DLBandwidth FROM ftpd WHERE User="\L"AND status="1" AND (ipaccess = "*" OR ipaccess LIKE "\R")
MySQLGetQTASZ   SELECT QuotaSize FROM ftpd WHERE User="\L"AND status="1" AND (ipaccess = "*" OR ipaccess LIKE "\R")
MySQLGetQTAFS   SELECT QuotaFiles FROM ftpd WHERE User="\L"AND status="1" AND (ipaccess = "*" OR ipaccess LIKE "\R")
```

现在我们创建启动连接并启动pureftpd
```
chkconfig --levels 235 pure-ftpd on
service pure-ftpd start
```

# 测试pureftpd

现在我们创建一个用户，使用ftp工具连接服务器实验一下。
```
USE pureftpd;
INSERT INTO `ftpd` (`User`, `status`, `Password`, `Uid`, `Gid`, `Dir`, `ULBandwidth`, `DLBandwidth`, `comment`, `ipaccess`, `QuotaSize`, `QuotaFiles`) VALUES ('exampleuser', '1', MD5('secret'), '2001', '2001', '/home/www.example.com', '100', '100', '', '*', '50', '0');
quit;
```

现在用工具连接看看情况。是不是连上了？

# 安装OPENSSL（TLS会话）
```
yum install openssl
```

# 配置PureFTPD
```
[...] # This option can accept three values :
# 0 : disable SSL/TLS encryption layer (default).
# 1 : accept both traditional and encrypted sessions.
# 2 : refuse connections that don't use SSL/TLS security mechanisms,
#     including anonymous sessions.
# Do _not_ uncomment this blindly. Be sure that :
# 1) Your server has been compiled with SSL/TLS support (--with-tls),
# 2) A valid certificate is in place,
# 3) Only compatible clients will log in.
TLS                      1
[...]
```

说明：0表示禁用SSL/TLS加密层，默认不加密；1表示服务器接受普通FTP会话和加密FTP会话；2表示只接受SSL/TLS会话。

# 创建TLS使用的SSL证书
```
mkdir -p /etc/ssl/private/
```
开始创建证书
```
openssl req -x509 -nodes -days 7300 -newkey rsa:2048 -keyout /etc/ssl/private/pure-ftpd.pem -out /etc/ssl/private/pure-ftpd.pem
```

```
Country Name (2 letter code) [XX]: &lt;– 输入国家名称简写 (例如, “CN”).
State or Province Name (full name) []: &lt;– 输入省份名称.
Locality Name (eg, city) [Default City]: &lt;– 输入市名
Organization Name (eg, company) [Default Company Ltd]: &lt;– 输入组织名称 (例如, the stanley’s private ftp).
Organizational Unit Name (eg, section) []: &lt;– 输入部名、科名 (例如. “IT 部”).
Common Name (eg, your name or your server’s hostname) []: &lt;– 输入你的域名 (例如. “server1.example.com”).
Email Address []: &lt;– 输入你的Email地址
```

修改SSL证书的权限

```
chmod 600 /etc/ssl/private/pure-ftpd.pem
```

之后重启PureFTPD使之生效

```
service pure-ftpd restart
```

# 安装ClamAV（杀毒软件）
```
yum install clamav clamd
```

创建系统启动连接并启动它
```
chkconfig --levels 235 clamd on
/usr/bin/freshclam
/etc/init.d/clamd start
```

# 配置PureFTPd

编辑`/etc/pure-ftpd/pure-ftpd.conf` 文件，修改 `CallUploadScript` 的值为`YES`。
```
vi /etc/pure-ftpd/pure-ftpd.conf
```
```
[...]
# If your pure-ftpd has been compiled with pure-uploadscript support,
# this will make pure-ftpd write info about new uploads to
# /var/run/pure-ftpd.upload.pipe so pure-uploadscript can read it and
# spawn a script to handle the upload.
# Don't enable this option if you don't actually use pure-uploadscript.
CallUploadScript yes
[...]
```

现在创建 `/etc/pure-ftpd/clamav_check.sh` 脚本，每当有文件上传时，它让clamdscan扫描上传的文件。

```
vi /etc/pure-ftpd/clamav_check.sh
```
```
#!/bin/sh
/usr/bin/clamdscan --remove --quiet --no-summary "$1"
```
修改脚本权限为可执行

```
chmod 755 /etc/pure-ftpd/clamav_check.sh
```

现在启动`pure-uploadscript`程序让它成为守护进程，当有文件上传完毕，它会呼叫`clamav_check.sh`脚本来处理。

将它加入`/etc/rc.local`，系统启动时可自行启动。

```
#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.
/usr/sbin/pure-uploadscript -B -r /etc/pure-ftpd/clamav_check.sh
touch /var/lock/subsys/local
```

注意：必须要先启动pure-uploadscript这个程序，然后再启动pure-ftpd守护进程，否则pure-ftpd启动不了。

