---
title: CentOS下nginx+php+fastcgi笔记
date: 2011-10-15T08:14:43+00:00
layout: post
categories:
  - 我的世界
tags:
  - CentOS
  - nginx
  - php
---

Centos版本5.7，首先需要做的进行内核优化。下面是内核优化参数

## 内核优化
```
net.ipv4.ip_forward = 0
net.ipv4.conf.default.rp_filter = 1 net.ipv4.conf.default.accept_source_route = 0
kernel.sysrq = 0 kernel.core_uses_pid = 1
net.ipv4.tcp_syncookies = 1
kernel.msgmnb = 65536
kernel.msgmax = 65536
kernel.shmmax = 68719476736
kernel.shmall = 4294967296
net.ipv4.tcp_max_tw_buckets = 6000
net.ipv4.tcp_sack = 1
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_rmem = 4096 87380 4194304
net.ipv4.tcp_wmem = 4096 16384 4194304
net.core.wmem_default = 8388608
net.core.rmem_default = 8388608
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216
net.core.netdev_max_backlog = 262144
net.core.somaxconn = 262144
net.ipv4.tcp_max_orphans = 3276800
net.ipv4.tcp_max_syn_backlog = 262144
net.ipv4.tcp_timestamps = 0
net.ipv4.tcp_synack_retries = 1
net.ipv4.tcp_syn_retries = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_mem = 94500000 915000000 927000000
net.ipv4.tcp_fin_timeout = 1
net.ipv4.tcp_keepalive_time = 30
net.ipv4.ip_local_port_range = 1024 65000
```
<!--more-->
## 1.安装MySQL

yum安装即可
```
yum install mysql mysql-server
```

设置MySQL启动级别
```
chkconfig --levels 235 mysqld on
/etc/init.d/mysqld start
```

编辑my.cnf，进行MySQL调优设置，与内存，应用需求等有关。
```
vi /etc/my.cnf
```

我的优化代码
```
set-variable=max_connections=1000
set-variable=wait_timeout=10
max_connect_errors = 300
query_cache_size=128M
query_cache_limit=2M
query_cache_min_res_unit=2k
sort_buffer_size=6M
tmp_table_size=246M
record_buffer=16M
thread_stack=512K
thread_cache_size=300
table_cache=512
max_allowed_packet=32M
key_buffer_size=256M
read_buffer_size=3M
read_rnd_buffer_size=16M
myisam_sort_buffer_size=64M
skip-locking
skip-name-resolve
back_log=384
```

重启MySQL服务，以便加载刚才做的设置。之后运行安装脚本，设置密码。
```
mysql_secure_installation
```

## 2.安装nginx

需要使用epel源，下载安装163镜像的即可。
```
rpm -Uvh http://download.fedora.redhat.com/pub/epel/5/i386/epel-release-5-4.noarch.rpm rpm -import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL

yum install nginx php-cli php make automake gcc gcc-c++ \
spawn-fcgi php-mysql php-gd php-imap php-ldap php-odbc php-pear \
php-xml php-xmlrpc php-eaccelerator php-magickwand \ php-magpierss php-mbstring php-mcrypt php-mssql php-shout php-snmp \
php-soap php-tidy
```

在这里说一下，比较讨厌的是因为依赖关系的原因，还是会安装apache2，在安装完毕后，先关闭启动级别，然后用chkconfig删除启动项。它就不会启动了，不用怕占用端口。
```
chkconfig httpd off
chkconfig --del httpd
chkconfig --level 35 nginx on
service nginx start
```

## 3.配置nginx

这里先使用www.example.com作为一个实例，创建必须的日志目录和web目录,并且更改目录所属权。
```
mkdir -p /srv/www/www.example.com/public_html
mkdir /srv/www/www.example.com/logs
chown -R nginx:nginx /srv/www/www.example.com
```

之后到nginx配置目录新建2个虚拟主机的目录。
```
mkdir /etc/nginx/sites-available
mkdir /etc/nginx/sites-enabled
```

然后编辑`nginx.conf`文件，包含sites-enabled目录即可。在`include /etc/nginx/conf.d/*.conf`之后添加。
```
 # Load virtual host configuration files. include /etc/nginx/sites-enabled/*;
```

okay，现在开始编辑虚拟主机www.example.com文件的内容。
```
vi /etc/nginx/site-available/www.example.com

server {
	server_name www.example.com example.com;
	access_log /srv/www/www.example.com/logs/access.log;
	error_log /srv/www/www.example.com/logs/error.log;
	root /srv/www/www.example.com/public_html;
	location / { index index.html index.htm index.php; }
	location ~ \.php$ {
		try_files $uri =404;
		if ($fastcgi_script_name ~ \..*\/.*php ) {
			return 403;
		}
		include /etc/nginx/fastcgi_params;
		fastcgi_pass 127.0.0.1:9000;
		fastcgi_index index.php;
		fastcgi_param SCRIPT_FILENAME /srv/www/www.example.com/public_html$fastcgi_script_name;
	}
}
```

现在让站点加入sites-enabled中，重启nginx.
```
cd /etc/nginx/sites-enabled/
ln -s /etc/nginx/sites-available/www.example.com
service nginx restart
```

## 4.配置spawn-fcgi

使用国外高手制作的spawn-fcgi脚本和php-fastcgi，设置启动选项。
```
cd /opt
wget -O php-fastcgi-rpm.sh http://library.linode.com/assets/696-php-fastcgi-rpm.sh
mv php-fastcgi-rpm.sh /usr/bin/php-fastcgi
chmod +x /usr/bin/php-fastcgi
```
```
wget -O php-fastcgi-init-rpm.sh http://library.linode.com/assets/697-php-fastcgi-init-rpm.sh
mv php-fastcgi-init-rpm.sh /etc/rc.d/init.d/php-fastcgi
chmod +x /etc/rc.d/init.d/php-fastcgi
chkconfig --add php-fastcgi
chkconfig php-fastcgi on
/etc/init.d/php-fastcgi start
```

## 5.其他

a.php临时上传目录权限要更改。
```
chown -R root:nginx /var/lib/php/session
```

b.php-fastcgi的启动脚本的restart函数貌似有个错误，说是无configtest此命令。

解决办法就是删除或者注释掉restart函数里面含有configtest的这一行,直接使用`killall -9 php-cgi`,stop函数也需要改一下。

c.由于安全问题，需要将`/etc/php.ini`中的`cgi.fix_pathinfo`设置为1.