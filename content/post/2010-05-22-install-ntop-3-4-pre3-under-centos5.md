---
title: CentOS 5.5下ntop 3.4 pre3安装配置笔记
date: 2010-05-22T01:51:58+00:00
layout: post
categories:
  - Linux
tags:
  - ntop
---
昨天发现有网络丢失包的情况，老男孩给了一个建议，使用ntop可以实时观察，统计，分析流量。那么ntop是个什么东东？Ntop是一个网络使用状况监测软件，在互动模式下，ntop会将网络的使用状况显示在使用者的终端机画面上。在Web模式中，ntop会像Web Server一样产生出内含网络使用状况的网页传回到使用者的浏览器上。它不仅可以列出节点的网络流量数据统计，也可以根据高层协议进性流量统计分析，比如P2P,HTTP,SSH,FTP等，还提供了插件功能，可以使用第三方插件，3.4Pre3默认情况下已包括NetFlow，cPacket，icmpWatch，rrdplugin，sFlow五个插件。

了解了NTOP是什么软件之后，那么我们就可以开始干活了。我这里需要使用ntop的服务器是一台跑iptables+tc的网关服务器。

有两种方式安装ntop，一种是通过编译安装的形式，另一种是使用rpmfusion库直接yum安装。首先，我们介绍第一种方法。
<!--more-->
## 第一种方法:

### 1.安装ntop所必需的开发库和软件
```
yum install glibc glibc-devel gcc cpp libtool m4 autoconf automake gdbm gdbm-devel libpcap libpcap-devel gd gd-devel libpng libpng-devel openssl openssl-devel zlib zlib-devel ruby libdbi.i386
```

之后去[rpmfind](http://www.rpmfind.net)下载rrdTool软件，一共需要三个包，分别是`rrdtool`,`perl-rrdtool`,`rrdtool-devel`,注意选择正确，一致的版本。
```
rpm -ivh rpm -ivh rrdtool-1.4.2-1.el5.rf.i386.rpm rrdtool-devel-1.4.2-1.el5.rf.i386.rpm perl-rrdtool-1.4.2-1.el5.rf.i386.rpm
```

安装GeoIP。
```
wget http://geolite.maxmind.com/download/geoip/api/c/test/GeoIP-1.4.7beta4.tar.gz
tar xvf GeoIP-1.4.7beta4.tar.gz
cd GeoIP-1.4.7beta4
./configure
make && make install
```

至此，所需要的软件和开发库就已经安装好了。

### 2.安装Ntop

将下载的ntop-3.4-pre3.tar.gz解压。
```
tar xvf ntop-3.4-pre3.tar.gz
cd ntop-3.4-pre3
./autogen.sh
make && make install
```

如果系统默认启用了Selinux，则还需要配置selinux策略。
```
make install-selinux-policy
```

安装完成了，默认将ntop安装到`/usr/local`下了，配置文件在`/usr/local/etc/ntop`,执行文件在`/usr/local/bin`下，如果你想要自定义安装路径，请给`autogen.sh`增加`–prefix=/usr`参数，如下：
```
./autogen.sh –prefix=/usr
```

在tar中有packages目录，里面包括了各个发行版所需要的配置文件和init文件，centos使用redhat的即可。
```
cp packages/RedHat/ntop.conf.sample /usr/local/etc/ntop/ntop.conf
cp packages/RedHat/ntop.init /etc/init.d/ntop
```

### 3.配置ntop

新建ntop用户，使ntop程序以ntop用户的权限运行。
```
useradd -M -s /sbin/nologin -r ntop
```

建立ntop程序存放数据库文件的目录。
```
mkdir -p /var/db/ntop
```

为ntop程序使用的目录设置权限。
```
chown -R ntop:ntop /var/db/ntop
chown -R ntop:ntop /usr/local/share/ntop
```

设置admin用户密码
```
ntop -A
```

启动ntop程序
```
/usr/local/bin/ntop -i eth1 -L -u ntop -P /var/db/ntop –skip-version-check –use-syslog=daemon
```

具体参数说明，使用`ntop –help`查阅。第一次启动时，需要输入admin的密码，之后可以进入web界面“Admin”->”Configure”进行细致的设置，比如以daemon运行。

如果想要自启动，请在`/etc/rc.d/rc.local`中添加
```
/usr/local/bin/ntop -i eth1 -d -L -u ntop -P /var/db/ntop –skip-version-check –use-syslog=daemon
```

打开浏览器，输入`http://ip:3000`查看流量统计分析

## 第二种安装方法：

下载rpmforge第三方软件仓库
```
wget http://packages.sw.be/rpmforge-release/rpmforge-release-0.5.1-1.el5.rf.i386.rpm
```

安装rpmforge
```
rpm -ivh rpmforge-release-0.5.1-1.el5.rf.i386.rpm
```

重新生成makecache缓存
```
yum makecache
```

安装所有软件
```
yum install glibc glibc-devel gcc cpp libtool m4 autoconf automake gdbm gdbm-devel libpcap libpcap-devel gd gd-devel libpng libpng-devel openssl openssl-devel zlib zlib-devel ruby libdbi.i386 rrdtool rrdtool-devel ntop
```

设置admin密码
```
ntop -A
```

编辑配置文件`vi /etc/ntop.conf`,修改其中的interface，subnet等几个地方。

具体深入的使用，请阅读随tar包的pdf文件。