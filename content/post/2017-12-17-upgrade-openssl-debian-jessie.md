---
title: Debian 8 升级openssl
date: 2017-12-17 15:14:30
layout: post
tags: 
  - Debian
  - openssl
---
# Debian 8.X 'Jessie' 升级OpenSSL 1.0.2l

debian jessie 官方软件源默认只安装了 openssl 1.0.1 的版本，对于需要使用 nginx 的朋友来说，使用 http2 的 OpenSSL 最低版本要求是 1.0.2，所以必须要先升级到1.0.2才可以。

##　开启back-ports软件源
```bash
vi /etc/apt/source.list
```

增加如下内容：

```bash
#Backports
deb http://ftp.debian.org/debian jessie-backports main
```

<!--more-->

## 更新软件源
```bash
apt update
```

## 升级OpenSSL

从 backports 软件源安装 libssl ，之后使用 backports 的软件更新系统。
```bash
apt-get -t jessie-backports install libssl1.0.0
apt-get -t jessie-backports update && apt-get -t jessie-backports upgrade
```

## 验证OpenSSL版本号
```bash
istek@localhost:~$ openssl version
OpenSSL 1.0.2l  25 May 2017
```

## 附：dotdeb软件源

```bash
#Dotdeb
deb http://packages.dotdeb.org jessie all
deb-src http://packages.dotdeb.org jessie all
```

添加软件源以后，还需要导入gpg key
```bash
wget --no-check-certificate https://www.dotdeb.org/dotdeb.gpg
apt-key add dotdeb.gpg
```