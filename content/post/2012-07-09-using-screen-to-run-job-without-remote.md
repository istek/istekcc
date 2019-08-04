---
title: 使用Screen使程序继续运行
date: 2012-07-09T07:08:11+00:00
layout: post
categories:
  - Linux
tags:
  - screen
---

碰到问题：

通过SSH远程登录到Linux系统，要在/home目录下面下载一个mysql-5.5.25.tar.gz的安装包在下载的过程中，不小心把SSH远程连接关闭了，这个时候下载也会中断必须重新登录到系统，再次手动执行命令，才能继续下载之前未下载完成的文件。有没有办法在SSH远程连接被断开或者关闭的时候，系统里面的下载程序还能继续运行？

再次登录到系统之后，还能够看都上次正在下载的文件？

解决办法：（以CentOS系统为例）
<!--more-->

#### 1、安装screen
```
yum install screen
```

#### 2、创建screen会话
```
screen -S mysql5.5
```

自定义screen虚拟终端的名称，mysql5.5可以改为你想要的名称

#### 3、下载mysql源码包

进入/home目录，使用wget命令下载mysql-5.5.25.tar.gz
```
cd /home
wget http://mysql.mirror.kangaroot.net/Downloads/MySQL-5.5/mysql-5.5.25.tar.gz
```

#### 4、测试

关闭SSH远程连接窗口，然后重新登录
```
screen -r mysql5.5 #查看之前的下载会话虚拟终端
```

可以看到下载还在继续进行，目的达到！

**tips：**

1. screen -ls #查看所有screen会话
2. 按键盘上面的Ctrl+a，然后再按d #保存当前的screen会话
3. exit #退出screen
4. screen -wipe mysql5.5 #删除会话

