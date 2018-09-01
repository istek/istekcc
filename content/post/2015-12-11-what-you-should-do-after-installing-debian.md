---
title: VPS安装Debian后需要做的事情
date: 2015-12-11T06:18:00+00:00
layout: post
categories:
  - 我的世界
tags:
  - Debian
---
每次给VPS安装DEBIAN后，发现有些事情是必须做的，为了免得再去东找西找，特意把需要做的事情都记录下来。

## 1. 安装apt-keyring

安装它的目的是为了在更新系统时，不再提示什么keyID找不到的消息。
```
apt-get install debian-keyring debian-archive-keyring
apt-key update
```
<!--more-->
## 2. 安装dialog

装了dialog后，可以方便地对locales进行调整，避免在更新系统的时候，出现一些讨厌的locales错误信息，如下：

> perl: warning: Setting locale failed. perl: warning: Please check that your locale settings: LANGUAGE = (unset), LC_ALL = (unset), LANG = “zh_CN.UTF-8″

方法如下：
```
# apt-get install dialog
# dpkg-reconfigure locales
```

选择`en_US.UTF-8`，之后，编辑bash的profile文件增加以下环境变量
```
export LANGUAGE="en_US.UTF-8"
export LANG=en_US:zh_CN.UTF-8
export LC_ALL=C
```

以后就再也没有这讨厌的错误信息了。

## 3. lowendbox

有个lowendbox脚本非常好使，推荐使用，方便搭建环境。使用方法：
```
# wget --no-check-certificate https://github.com/lowendbox/lowendscript/raw/master/setup-debian.sh ...
# bash setup-debian.sh system ...
# bash setup-debian.sh exim4 ...
# bash setup-debian.sh nginx ...
# bash setup-debian.sh mysql ...
# bash setup-debian.sh php ...
# bash setup-debian.sh wordpress blog.example.com
```

## 4. mysql无法启动

经查看mysql日志，发现已经关闭了innodb，而my.cnf中未定义默认存储引擎导致，将如下语句增加到`[mysqld]`小节下，
```
# vi /etc/mysql/my.cnf
default-storage-engine=myisam
# /etc/init.d/mysql start
```

如果日志中还出现一些warn日志，可以自行处理。


