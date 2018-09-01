---
title: CentOS安装php加速软件Zend Guard
date: 2012-07-05T19:45:58+00:00
layout: post
categories:
  - 我的世界
tags:
  - CentOS
  - php
---

说明：PHP5.3以上的版本不再支持Zend Optimizer，已经被全新的 Zend Guard Loader 取代，下面是安装Zend Guard具体步骤，以下操作均在终端命令行执行

## 1、下载Zend Guardcd /home
```
# wget http://downloads.zend.com/guard/5.5.0/ZendGuardLoader-php-5.3-linux-glibc23-i386.tar.gz #32位
# wget http://downloads.zend.com/guard/5.5.0/ZendGuardLoader-php-5.3-linux-glibc23-x86_64.tar.gz #64位
```

## 2、安装Zend Guard
```
# mkdir /usr/zend #建立Zend Guard安装目录
# tar xvfz ZendGuardLoader-php-5.3-linux-glibc23-i386.tar.gz #解压安装文件
# cp ZendGuardLoader-php-5.3-linux-glibc23-i386/php-5.3.x/ZendGuardLoader.so /usr/zend/ #拷贝文件到安装目录
# rm -rf /home/ZendGuardLoader-php-5.3-linux-glibc23-i386* #删除安装包
```
<!--more-->
## 3、配置Zend Guard
```
cp /etc/php.ini /etc/php.inibak #修改之前先备份
vi /etc/php.ini #编辑文件
```

在最后位置添加以下内容
```
[Zend Guard]
zend_extension=/usr/zend/ZendGuardLoader.so
zend_loader.enable=1
zend_loader.disable_licensing=0
zend_loader.obfuscation_level_support=3
zend_loader.license_path=
```

## 4、重启web服务器
```
/etc/init.d/httpd restart
```

至此，Zend Guard安装完成。