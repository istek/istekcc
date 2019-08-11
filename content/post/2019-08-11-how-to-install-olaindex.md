---
title: "安装及配置OLAINDEX"
date: 2019-08-11T15:17:32+08:00
draft: false
categories:
 - WEB
tags:
 - olaindex
featured_image: https://camo.githubusercontent.com/531630cfe86c547739f37171fd200bb1862f073c/68747470733a2f2f692e6c6f6c692e6e65742f323031392f30362f31352f3564303439643732333039633337363133332e706e67
---

OLAINDEX是一款 OneDrive 目录文件索引应用，基于优雅的 PHP 框架 Laravel5.8 搭建，并通过 Microsoft Graph 接口获取数据展示，支持多类型帐号登录，多种主题显示，简单而强大。
<!--more-->
## 功能：

- OneDrive 目录查看索引分页查看；
- 支持代码、图片、文本文件即时预览、图片列表栏展示；
- 支持音视频播放（兼容大部分格式），视频播放采用 Dplayer.js，音乐播放采用 Aplayer；
- 支持自定义创建文件夹、文件夹加密、文件/文件夹删除、文件/文件夹的复制与移动；
- 支持文件搜索、文件上传、文件直链分享与删除、文件直链一键下载；
- 支持管理 readme/head 说明文件；
- 支持图床；
- 支持命令行操作；
- 支持文件离线下载（个人版）；
- 后台基本显示管理，多主题管理，文件预览管理等等（清理缓存后及时生效）；
- 支持世纪互联；
- 支持多种缓存系统（Redis、Memcached等）；

## PHP及模块要求

- PHP >= 7.1.3
- PHP OpenSSL 扩展
- PHP PDO 扩展
- PHP Mbstring 扩展
- PHP Tokenizer 扩展
- PHP XML 扩展
- PHP Ctype 扩展
- PHP JSON 扩展
- PHP BCMath 扩展
- PHP Fileinfo 扩展
- PHP函数`exec`,`shell_exec`,`proc_open`
- [composer](https://getcomposer.org)

## 安装

```bash
cd web目录
git clone https://github.com/WangNingkai/OLAINDEX.git tmp 
mv tmp/.git . 
rm -rf tmp 
git reset --hard 
cp database/database.sample.sqlite database/database.sqlite  # 数据库文件
composer install -vvv # 这里确保已成功安装 composer ，如果报权限问题，建议给予用户完整权限。
chmod -R 777 storage/ database/ 
chown -R www:www * # 此处 www 根据服务器具体用户组而定
php artisan od:install # 此处绑定域名需根据实际域名谨慎填写（包含http/https）
```

## Nginx配置要点

### 路径重写

```ini
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

### root目录

需设置为`public`目录，如下

```bash
root /web/www.ab.com/public;
```

## OneDrive

申请application，需要去[azure](https://portal.azure.com)申请，使用你的onedrive帐号登录portal，然后点击`Azure Active Directory`，再点击`App registrations`，就会显示已经建立的应用，如果要新建应用，点击`New registration`,`Redirect URI (optional)`就是回调地址。

![](https://oss.zhoutao.ren/img/20190811153241.png)

## 升级

```bash
git pull 
composer update -vvv # 无版本更新只需执行到此

chmod -R 755 storage # 补充，执行此处两条命令，确保缓存的写入权限，否则500
chown -R www:www *
```

## 重置

- `php artisan od:reset`重置全部应用数据
- `php artisan od:logout`重置当前绑定账号数据
- `php artisan od:login`登陆账号

## 后台登录

后台默认地址：`https://your.domain.com/admin`,初始后台密码12345678,也可通过命令行工具`php artisan od:password`生成一个新的8位数的密码。

