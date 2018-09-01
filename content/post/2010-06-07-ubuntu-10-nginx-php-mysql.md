---
title: Ubuntu 9.04 Nginx+php+mysql
date: 2010-06-07T15:38:52+00:00
layout: post
categories:
  - 我的世界
tags:
  - nginx
  - php
  - Ubuntu
  - 数据库
---

由于Ubuntu 9.04已经包含了nginx，所以根本不要编译，安装超简单！

修改`/etc/apt/sources.list`文件内容为国内镜像，然后运行：
```
apt-get update apt-get install nginx
```

即可完成安装。启动nginx：
```
/etc/init.d/nginx start
```

然后就可以访问`http://localhost/`了，一切正常！如果不能访问，先不要继续，看看是什么原因，解决之后再继续。
<!--more-->
安装php和MySQL:
```
apt-get install php5-cli php5-cgi mysql-server-5.0 php5-mysql spawn-fcgi
```

修改nginx的配置文件：`/etc/nginx/sites-available/default`

修改 server_name 为你的hostname;

修改index的一行修改为：

`index index.php index.html index.htm;`

去掉下面部分的注释：
```
location ~ \.php$ {
	fastcgi_pass 127.0.0.1:9000;
	fastcgi_index index.php;
	fastcgi_param SCRIPT_FILENAME /var/www/nginx-default$fastcgi_script_name;
	include /etc/nginx/fastcgi_params;
}
```

重新启动nginx:
```
/etc/init.d/nginx stop /etc/init.d/nginx start
```

启动fastcgi php:
```
spawn-fcgi -a 127.0.0.1 -p 9000 -C 10 -u www-data -f /usr/bin/php-cgi
```

为了让php-cgi开机自启动：
```
cd /etc/init.d
cp nginx php-cgi
vim php-cgi
```

替换nginx为php-cgi并修改相应部分为：
```
DAEMON=/usr/bin/spawn-fcgi
DAEMON_OPTS="-a 127.0.0.1 -p 9000 -C 10 -u www-data -f /usr/bin/php-cgi"

 … stop)
echo -n "Stopping $DESC: " pkill -9 php-cgi echo "$NAME."
```

然后运行**rcconf**设置php-cgi为开机自启动

在`/var/www/nginx-default/`目录下创建一个文件：
```
echo '' > /var/www/nginx-default/index.php
```

最后用浏览器测试是否能够解析php脚本。一切OK。为了优化和缓存php，我们需要安装eAccelerator，安装必须还有开发工具，开发库已经PHP5-DEV。
```
apt-get install build-essential php5-dev
```
```
wget http://bart.eaccelerator.net/source/0.9.6.1/eaccelerator-0.9.6.1.tar.bz2
tar xvf eaccelerator-0.9.6.1.tar.bz2
cd eaccelerator-0.9.6.1
phpize
./configure –enable-eaccelerator=shared –with-php-config=/usr/bin/php-config
make && make install
mkdir /var/cache/eaccelerator
chown www-data.www-data /var/cache/eaccelerator
```

编辑/etc/php5/cgi/php.ini，在最后添加内容：
```
[eaccelerator]
extension="eaccelerator.so"
eaccelerator.shm_size="16"
eaccelerator.cache_dir="/var/cache/eaccelerator"
eaccelerator.enable="1" eaccelerator.optimizer="1"
eaccelerator.check_mtime="1"
eaccelerator.debug="0"
eaccelerator.filter=""
eaccelerator.shm_max="0"
eaccelerator.shm_ttl="0"
eaccelerator.shm_prune_period="0"
eaccelerator.shm_only="0"
eaccelerator.compress="1"
eaccelerator.compress_level="9"
```

最后重启nginx和php-cgi.