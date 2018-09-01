---
title: CentOS 5.5安装Pure-ftpd-mysqlless
date: 2011-01-01T09:55:51+00:00
layout: post
categories:
  - 我的世界
tags:
  - CentOS
  - pure-ftpd
---
最近想更换一个FTP软件，看到Pure-FTPd不错，对客户端编码支持比较好，尤其是现在我同事大多使用WINDOWS，如果使用VSFTPD，上传上去的文档之类的全部是乱码，而且Pure-Ftpd还可以很方便的打开TLS认证，于是乎先卸载了原本安装的VSFTPD软件，先`yum search pure`，发现有`Pure-FTPD`，嘿嘿，直接YUM安装即可。

## 安装pure-ftpd
```
yum install pure-ftpd
```
<!--more-->
## 配置
安装完成之后，就可以进行配置了。下面是我的配置文件，提供给大家看看。
```
 vi /etc/pure-ftpd/pure-ftpd.conf
```

```
############################################################
# # # Configuration file for pure-ftpd wrappers # # # ######
# If you want to run Pure-FTPd with this configuration
# instead of command-line options, please run the
# following command :
# # /usr/sbin/pure-config.pl /etc/pure-ftpd/pure-ftpd.conf
# # Please don't forget to have a look at documentation at
# http://www.pureftpd.org/documentation.shtml for a complete list of
# options.
# Cage in every user in his home directory 将所有用户锁定于其home目录
ChrootEveryone yes

# If the previous option is set to "no", members of the following group
# won't be caged. Others will be. If you don't want chroot()ing anyone,
# just comment out ChrootEveryone and TrustedGID.设置一个信任的组ID，这样就可以不被锁定于home目录了。
# TrustedGID 100
# Turn on compatibility hacks for broken clients 可以兼容IE浏览器
BrokenClientsCompatibility no

# Maximum number of simultaneous users 服务器可以同时登录的最大客户端数目
MaxClientsNumber 50

# Fork in background 是否以守护进程运行
Daemonize yes

# Maximum number of sim clients with the same IP address同一IP最大连接数，默认是8
MaxClientsPerIP 4

# If you want to log all client commands, set this to "yes".
# This directive can be duplicated to also log server responses.记录所有的客户端命令到日志里面。
VerboseLog no

# List dot-files even when the client doesn't send "-a". 显示隐藏文件。
DisplayDotFiles yes
# Don't allow authenticated users - have a public anonymous FTP only. 禁止匿名登录
AnonymousOnly no

# Disallow anonymous connections. Only allow authenticated users. 禁止匿名连接，只允许认证用户。
NoAnonymous yes

# Syslog facility (auth, authpriv, daemon, ftp, security, user, local*)
# The default facility is "ftp". "none" disables logging. SyslogFacility ftp
# Display fortune cookies
# FortunesFile /usr/share/fortune/zippy
# Don't resolve host names in log files. Logs are less verbose, but
# it uses less bandwidth. Set this to "yes" on very busy servers or
# if you don't have a working DNS.
DontResolve yes

# Maximum idle time in minutes (default = 15 minutes) 最大空闲时间
MaxIdleTime 5

# LDAP configuration file (see README.LDAP)
# LDAPConfigFile /etc/pure-ftpd/pureftpd-ldap.conf
# MySQL configuration file (see README.MySQL)
# MySQLConfigFile /etc/pure-ftpd/pureftpd-mysql.conf
# Postgres configuration file (see README.PGSQL)
# PGSQLConfigFile /etc/pure-ftpd/pureftpd-pgsql.conf
# PureDB user database (see README.Virtual-Users)
# PureDB /etc/pure-ftpd/pureftpd.pdb # Path to pure-authd socket (see README.Authentication-Modules)
# ExtAuth /var/run/ftpd.sock
# If you want to enable PAM authentication, uncomment the following line
PAMAuthentication yes

# If you want simple Unix (/etc/passwd) authentication, uncomment this
UnixAuthentication yes

# Please note that LDAPConfigFile, MySQLConfigFile, PAMAuthentication and
# UnixAuthentication can be used only once, but they can be combined
# together. For instance, if you use MySQLConfigFile, then UnixAuthentication,
# the SQL server will be asked. If the SQL authentication fails because the
# user wasn't found, another try
# will be done with /etc/passwd and
# /etc/shadow. If the SQL authentication fails because the password was wrong,
# the authentication chain stops here. Authentication methods are chained in
# the order they are given.

# 'ls' recursion limits. The first argument is the maximum number of
# files to be displayed. The second one is the max subdirectories depth
LimitRecursion 2000 8

#Are anonymous users allowed to create new directories ?
AnonymousCanCreateDirs no

# If the system is more loaded than the following value,
# anonymous users aren't allowed to download.
MaxLoad 4

# Port range for passive connections replies. - for firewalling. PASSIVE端口范围
PassivePortRange 45000 50000

# Force an IP address in PASV/EPSV/SPSV replies. - for NAT.
# Symbolic host names are also accepted for gateways with dynamic IP
# addresses.
#如果FTP服务器在内网，而PASSIVE的模式是被动连接，所以要指定一个公网IP。如果不是通过NAT出公网，就不需要了。
ForcePassiveIP x.x.x.x

# Upload/download ratio for anonymous users.
# AnonymousRatio 1 10

# Upload/download ratio for all users.
# This directive superscedes the previous one.
# UserRatio 1 10

# Disallow downloading of files owned by "ftp", ie.
# files that were uploaded but not validated by a local admin.
AntiWarez yes

# IP address/port to listen to (default=all IP and port 21).
# FTP服务绑定的IP和端口，我发现设置为127.0.0.1后，不能连接，设置为0.0.0.0后就可以，这个大家注意一下。
# Bind 127.0.0.1,21

# Maximum bandwidth for anonymous users in KB/s
# AnonymousBandwidth 8

# Maximum bandwidth for *all* users (including anonymous) in KB/s
# Use AnonymousBandwidth *or* UserBandwidth, both makes no sense.
# UserBandwidth 8

# File creation mask. <umask files="" for="">:</umask><umask dirs="" for=""> .
# 177:077 if you feel paranoid.
Umask 133:022

# Minimum UID for an authenticated user to log in. 登录用户的UID如果低于500，是无法登录的。
MinUID 500

# Do not use the /etc/ftpusers file to disable accounts. We're already
# using MinUID to block users with uid :<max anonymous="" sessions="">
# For instance, 3:20 means that the same authenticated user can have 3 active
# sessions max. And there are 20 anonymous sessions max.
# PerUserLimits 3:20

# When a file is uploaded and there is already a previous version of the file
# with the same name, the old file will neither get removed nor truncated.
# Upload will take place in a temporary file and once the upload is complete,
# the switch to the new version will be atomic. For instance, when a large PHP
# script is being uploaded, the web server will still serve the old version and
# immediatly switch to the new one as soon as the full file will have been
# transfered. This option is incompatible with virtual quotas.
# NoTruncate yes

# This option can accept three values :
# 0 : disable SSL/TLS encryption layer (default). 关闭SSL/TLS加密层，也就是只支持普通的明文连接。
# 1 : accept both traditional and encrypted sessions. 支持明文连接和加密连接。
# 2 : refuse connections that don't use SSL/TLS security mechanisms,
# including anonymous sessions.只允许SSL/TLS加密连接。
# Do _not_ uncomment this blindly. Be sure that :
# 1) Your server has been compiled with SSL/TLS support (--with-tls),
# 2) A valid certificate is in place,
# 3) Only compatible clients will log in. TLS 2

# Listen only to IPv4 addresses in standalone mode (ie. disable IPv6)
# By default, both IPv4 and IPv6 are enabled. 只监听IPV4地址。
IPV4Only yes

# Listen only to IPv6 addresses in standalone mode (ie. disable IPv4)
# By default, both IPv4 and IPv6 are enabled.
IPV6Only no

# UTF-8 support for file names (RFC 2640)
# Define charset of the server filesystem and optionnally the default charset
# for remote clients if they don't use UTF-8.
# Works only if pure-ftpd has been compiled with --with-rfc2640
# 客户端编码
#FileSystemCharset utf-8
ClientCharset gbk
```

编辑完成后，就可以启动服务了。
```
chkconfig --levels 235 pure-ftpd on
service pure-ftpd start
```
## 防火墙规则
接着就可以在iptables里面增加相应的规则，比如运行21端口和PASSIVE端口范围。
```
iptables -A INPUT -p tcp -m state --state NEW --dport 21 -j ACCEPT iptables -A INPUT -p tcp -m multiport --dports 45000:50000 -j ACCEPT
```

如果在内网，还需要在网关上添加端口映射。
```
iptables -t nat -A PREROUTING -d 202.123.212.113 -p tcp -m tcp --dport 2020 -j DNAT --to-destination 192.168.10.3:2020
iptables -t nat -A PREROUTING -d 202.123.212.113 -p tcp -m multiport --dports 43000:45000 -j DNAT --to-destination 192.168.10.3
```

## 证书生成方法

```
mkdir -p /etc/ssl/private/
openssl req -x509 -nodes -days 7300 -newkey rsa:2048 -keyout /etc/ssl/private/pure-ftpd.pem -out /etc/ssl/private/pure-ftpd.pem
chmod 600 /etc/ssl/private/pure-ftpd.pem
```
