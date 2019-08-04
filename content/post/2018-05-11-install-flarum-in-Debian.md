---
title: "在debian上安装Flarum论坛"
date: 2018-05-11 11:30:00
layout: post
tags: 
  - flarum
categories:
  - Linux
---

给兰州linux爱好者建了一个论坛，使用了flarum论坛，因为使用的是nginx，所以伪静态还需要自己添加。

# 条件
- web server: apache(rewrite要打开) , nginx
- PHP 5.5 +（需要fileinfo模块）
- mysql 5.5+


<!--more-->


# 安装
## 1.安装git

```
apt install git
```
## 2.clone一键安装脚本

```
git clone https://gitee.com/skywalker512/php-repo-installer.git
```

## 3.将 index.php 文件放入web根目录

```
cp php-repo-installer/index.php /var/www/html/
```

## 4.安装
打开浏览器开始安装，首先这个脚本会将flarum论坛的代码包下载，之后解压，然后从flarumChina下载语言包和一些插件包，解压后，开始安装进程。

![bd2f886egw1f3xea6oiuhj217a13mn11.jpg][1]

填写完成上述内容，点击安装，安装完成后，就可以使用flarum了。

## 5.nginx重写规则（可选）

```
location / { try_files $uri $uri/ /index.php?$query_string; }
location /api { try_files $uri $uri/ /api.php?$query_string; }
location /admin { try_files $uri $uri/ /admin.php?$query_string; }

location /flarum {
 deny all;
 return 404;
 }

location ~* \.html$ {
 expires -1;
 }

location ~* \.(css|js|gif|jpe?g|png)$ {
 expires 1M;
 add_header Pragma public;
 add_header Cache-Control "public, must-revalidate, proxy-revalidate";
 }
```


  [1]: http://image.bestzhou.us/usr/uploads/2018/05/1791558129.jpg