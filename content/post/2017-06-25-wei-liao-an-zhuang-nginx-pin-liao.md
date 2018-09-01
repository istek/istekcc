---
title: weblogic前端代理应用于nginx
date: 2017-06-25T10:56:27+00:00
layout: post
categories:
- 我的世界
tags:
- nginx
- RHEL
---

# 前言

客户给我们了几台vps，主要是为了给我们的应用使用，目前我们的应用程序运行在A机和B机两台VPS上，使用了weblogic的集群，但是，有一个proxy应用安装在A机上，专门负责分发应用，这样的话，无形中增加了A机的负载，年初就因为这个事情，A机每天高峰期就宕机，我又拿了一台闲置的vps专门跑应用的计划任务，目前一共A,B,C三台vps在跑应用，最近开始关注这个事情，为什么不能使用nginx去做负载呢？我还有一台闲置的vps啊，我可以安装nginx专门做负载，转发流量给其他三台，这样A机就不会有负载问题了。

<!--more-->

# 规划

一共四台VPS，A,B,C,D。

**设计原则**：

* 利用现有网络规划；
* 不调整现有主机和应用结构，减小生产环境的影响；
* 因为没有公网，所以必须使用源码编译安装；

A机和B机主要负责正常的负载均衡，C机做备用，D机跑NGINX做流量转发。客户端向D机发送请求，D机根据NGINX的轮询机制，转发给A机或者B机，C机可以设为backup，若A机和B机出现问题，负责所有请求。很简单的设计。

# APR

## 为什么要安装apr？

因为D机只跑了nginx，也可以再运行一个tomcat作为backup，基于这种考虑，我想把apr安装好，使得tomcat以apr的运行模式工作。

## 安装步骤

### 1. 下载源码包

到[官网](https://apr.apache.org/download.cgi)下载apr和apr-utils源码包，上传至D服务器。

```
apr-1.5.2.tar.gz
apr-util-1.5.4.tar.gz
```

### 2. 安装APR

远程至D机，以root身份安装，在编译之前，需要修改一下configure文件，否则编译会报错`rm: cannot remove 'libtoolT': No such file or directory`。

解压文件，进入apr目录

```
tar -xvf apr-1.5.2.tar.gz
cd apr-1.5.2
```

vi编辑configure文件，查找`$RM “$cfgfile”` ，注释该句，我没数行数，但是那行就这么一句代码。

```
vi configure
# $RM "$cfgfile"
```

然后开始编码和安装

```
./configure
make && make install
```

### 3. 安装APR-UTIL

apr-util安装没有难度，按照标准程序执行。

```
tar -zxvf apr-util-1.5.4.tar.gz
cd apr-util-1.5.4
./configure --with-apr=/usr/local/apr
make && make install
```

给`ld.so.conf.d`增加库文件查找路径
```
echo '/usr/local/apr/lib' > /etc/ld.so.conf.d/apr
```

# openssl

我将openssl编译为了PIC[^1]的动态库。
```
tar xvf openssl-1.0.2l.tar.gz
cd openssl-1.0.2l
./config --prefix=/usr/local/openssl -fPIC
make && make install
```

编译完成之后，还需要将动态库路径增加到`ld.so.conf`配置文件
```
echo '/usr/local/openssl/lib' > /etc/ld.so.conf.d/openssl
```

# openssh

## 1. 为什么还要编译openssh？

这是因为我们编译安装了openssl，会影响到openssh，所以需要重新编译安装openssh。

## 2. 安装之前

强烈建议打开telnet服务，一旦openssh启动失败，如果你没有启用telnet，你就哭去吧。

建议提前下载系统镜像到目录，方便yum安装xinetd软件包。

```
mount -o loop -t iso9660 /home/rhel-server-6.4-x86_64-dvd.iso /mnt
```

修改`yum.repos.d`目录下的`cd.repo`文件

```
[CDROM]
name=isofile
baseurl=file:///mnt
enabled=1
gpgcheck=0
gpgkey=file:///mnt/RPM-GPG-KEY-redhat-release
```

之后yum安装所需软件包

```
yum install telnet-server xinetd
```

配置xinetd服务，使得telnet启动，编辑`/etc/xinetd.d/telnet`

```
# default: on
# description: The telnet server serves telnet sessions; it uses \
# unencrypted username/password pairs for authentication.
service telnet {
	flags = REUSE
	socket_type = stream
	wait = no
	user = root
	server = /usr/sbin/in.telnetd
	log_on_failure += USERID
	disable = no ##修改为no
}
```

启动服务

```
chkconfig xinetd on
service xinetd start
```

_提示: 如果使用了iptables，请一并关闭。_

## 3. 安装openssh

因我之前已经编译安装了openssh7.3p1，所以仍然使用这个软件包。

```
cd openssh-7.3p1
make clean #清除之前编译产生的文件
```

编译参数

```
./configure \
 --prefix=/usr \
 --sysconfdir=/etc/ssh \
 --with-ssl-dir=/usr/include/openssl \
 --with-md5-passwords \
 --with-pam \
 --with-mantype=man \
 --with-tcp-wrappers
```

安装

```
make && make install
```

## 4. 验证服务

重启openssh验证是否可以启动服务
```
service sshd restart
```

## 5. Trouble shooting

服务无法启动，原则先看日志，检查启动的报错信息，根据报错信息处理。

一般来说，可能都是因为配置文件的问题导致的。

配置文件路径：`/etc/ssh/sshd_config`

# zlib

zlib目前版本1.2.11，可以直接从官方网站下载即可。

```
tar xvf zlib-1.2.11.tar.gz
cd zlib-1.2.11
./configure --shared
make && make install
```

# nginx

## 1. 安装

安装nginx之前需要准备好pcre和zlib
```
tar xvf pcre-8.40.tar.gz
tar xvf zlib-1.2.11.tar.gz
tar xvf nginx-1.12.0.tar.gz
```

进入nginx目录，开始编译安装

```
cd nginx-1.12.0
./configure --build=nginx --with-poll_module --with-select_module --prefix=/usr/local/nginx \
--pid-path=/var/run/nginx.pid --lock-path=/var/lock/nginx.lock --with-http_ssl_module \
--with-http_dav_module --with-http_flv_module --with-http_realip_module \
--with-http_gzip_static_module --with-http_stub_status_module --with-mail \
--with-mail_ssl_module --with-pcre=../pcre-8.40 --with-zlib=../zlib-1.2.11 \
--with-debug --http-client-body-temp-path=/var/tmp/nginx/client \
--http-proxy-temp-path=/var/tmp/nginx/proxy \
--http-fastcgi-temp-path=/var/tmp/nginx/fastcgi --http-uwsgi-temp-path=/var/tmp/nginx/uwsgi \
--http-scgi-temp-path=/var/tmp/nginx/scgi --conf-path=/etc/nginx/nginx.conf --sbin-path=/usr/sbin
make && make install
```

nginx的配置文件路径在`/etc/nginx`目录，执行脚本在`/usr/sbin/`目录，其他文件都在`/usr/local/nginx`。

## 2. 配置

创建一个进程用户

```
useradd -M -r -s /sbin/nologin nginx
```

创建日志目录

```
mkdir /var/log/nginx
chown -R nginx.nginx /var/log/nginx
ln -s /var/log/nginx /etc/nginx/logs
```

修改配置文件

```
vi /etc/nginx/nginx.conf
```

配置参数参见[此文](https://www.bestzhou.org/linux-optimize-mysql-nginx/)

## 3. 开机服务

在`/etc/init.d`目录新建nginx文件，内容如下

```
#!/bin/bash
# nginx This shell script takes care of starting and stopping
# nginx
#
# chkconfig: - 13 68
# description: nginx is a web server
### BEGIN INIT INFO
# Provides: $named
# Short-Description: start|stop|status|restart|configtest
### END INIT INFO
#variables
NGINX_BIN="/usr/sbin/nginx"
NGINX_CONF="/etc/nginx/nginx.conf"
NGINX_PID="/var/run/nginx.pid"
NETSTAT="/bin/netstat"
alter=$1
prog=nginx
#load system function
. /etc/rc.d/init.d/functions
#function:echo ok or error
function if_no {
	if [ $2 == 0 ]; then
		echo -n $"$1 ${prog}:" && success && echo
	else
		echo -n $"$1 ${prog}:" && failure && echo
	fi
}
#start nginx
function start {
	if [ -s ${NGINX_PID} ]; then
		echo "nginx already running"
	else
		if [ `${NETSTAT} -tnpl | grep nginx | wc -l` -eq 0 ]; then
			rm -f ${NGINX_PID} 2>/dev/null
			${NGINX_BIN} -c ${NGINX_CONF}
			if_no start $?
		else
			${NETSTAT} -tnpl | grep nginx | awk '{ print $7}' | cut -d '/' -f 1 > ${NGINX_PID}
			if_no start $?
		fi
	fi
}
#stp nginx
function stop {
	if [ -s ${NGINX_PID} ]; then
		cat ${NGINX_PID} | xargs kill -QUIT
		if_no stop $?
	else
		if [ `${NETSTAT} -tnpl | grep nginx | wc -l` -eq 0 ]; then
			rm -f ${NGINX_PID} 2>/dev/null
			if_no stop 0
		else
			rm -f ${NGINX_PID} 2>/dev/null
			kill `${NETSTAT} -tnpl | grep nginx | awk '{ print $7}' | cut -d '/' -f 1`
			if_no stop $?
		fi
	fi
}
function restart {
	if [ -s ${NGINX_PID} ]; then
		cat ${NGINX_PID} | xargs kill -HUP
		if_no restart $?
	else
		stop
		sleep 1
		start
	fi
}
function status {
${NETSTAT} -tnpl | grep nginx | grep LISTEN
[ $? == 0 ] && echo "nginx is running" || echo "nginx is not running"
}
function configtest {
	${NGINX_BIN} -t
}
case $alter in
start)
start
;;
stop)
stop
;;
restart)
restart
;;
status)
status
;;
configtest)
configtest
;;
*)
echo "use:${NGINX} {start|stop|restart|status|configtest}"
;;
esac
```

给于权限和开机执行

```
chmod a+x /etc/init.d/nginx
chkconfig --add nginx
chkconfig nginx on
service nginx start
```

## 4. 验证服务

访问IP，应该显示nginx的欢迎页面

![alt](http://upyun.zhoutao.ren/20170625/wPdydVWbAwcy8r2d8jl4UV2e.png)

## 5. 配置负载

### 5.1 内置负载策略

Nginx负载均衡是通过upstream模块来实现的，内置实现了三种负载策略，配置还是比较简单的。官网负载均衡配置说明：http://nginx.org/en/docs/http/load_balancing.html

* 轮循（默认）：Nginx根据请求次数，将每个请求均匀分配到每台服务器
* 最少连接：将请求分配给连接数最少的服务器。Nginx会统计哪些服务器的连接数最少。
* IP Hash：绑定处理请求的服务器。第一次请求时，根据该客户端的IP算出一个HASH值，将请求分配到集群中的某一台服务器上。后面该客户端的所有请求，都将通过HASH算法，找到之前处理这台客户端请求的服务器，然后将请求交给它来处理。

### 5.2 配置

参考了别人的配置文件[^2]，配置如下：

```
http {
# ... 省略其它配置
	upstream tomcats {
		server A机IP:7010;
		server B机IP:7010;
		server C机IP:8080 backup;
	}
	server {
		listen 80;
		location / {
			proxy_pass http://tomcats;
		}
	}
# ... 省略其它配置
}
```

## 补充后续事宜

周一上班后，我测试了一下，发现在会话的保存上存在问题，而且不能存在混合状态的主机。比如我的A机和B机是WEBLOGIC集群，C机又是tomcat，无法做负载。后来我修改了一下配置文件，

```
upstream tomcats {
iphash;
server A机IP:7010;
server B机IP:7010;
}
```

是可以做负载了，但是我登录系统以后，我发现我的应用的各种报表和功能菜单，点击后要么还是无故返回未登录状态，要么就是一直持续加载中。看来nginx负载均衡weblogic集群的方案还是不可行。

于是，我又求助于oracle官方站点，发现一篇文章[^2]是将apache作为前端web服务器负载均衡weblogic，弄了半天weblogic给Apache做了module，只要有那个module，Apache也可以作为负载均衡的前端。我这边安装的welogic版本是10.3.6.0，于是我在weblogic的安装目录找了半天，也没发现这个组件，我就又去google找，终于找到了。

下载地址在这里：[ORACLE Webtier](http://www.oracle.com/technetwork/middleware/webtier/downloads/index.html)

根据我的weblogic的版本号，我需要下载Oracle WebLogic Server Proxy Plugins 11gR1 (11.1.1.9)这个版本。

下载以后解压出来，然后从apache的配置文件中load这个模块。

```
[root@tasdb2 conf.d]# cat /etc/httpd/conf.d/mod_wl.conf
LoadModule weblogic_module /u01/wlsplugin/lib/mod_wl.so
```

然后新建一个虚拟主机，可以使用下面的代码

```
<VirtualHost 135.149.80.36:80>
	#WLSRequest On
	SetHandler weblogic-handler
	WebLogicCluster 135.149.80.33:7010,135.149.80.34:7010
	MatchExpression *.jsp
	#WLLogFile /var/log/httpd/wlproxy.log
	<ifmodule mod_deflate.c>
		DeflateCompressionLevel 9
		SetOutputFilter DEFLATE
		AddOutputFilterByType DEFLATE text/html text/plain text/xml
		AddOutputFilterByType DEFLATE application/javscript
		AddOutputFilterByType DEFLATE text/css
	</ifmodule>
</VirtualHost>
```

注意一下配置文件里面的`WebLogicCluster` ，我用的是weblogic集群，所以这里这样配置，然后为了性能，我还打开了gzip压缩模块。

重启apache，然后我又测试了一次所有的菜单和报表，OK，全部都可以正常使用。终于，搞定了。


[^1]: 位置无关代码（Position Independent Code，PIC）

[^2]: http://blog.csdn.net/xyang81/article/details/51702900

