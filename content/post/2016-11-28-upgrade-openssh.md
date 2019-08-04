---
title: OpenSSH更新办法
date: 2016-11-28T19:13:25+00:00
layout: post
categories:
  - Linux
tags:
  - openssh
---
局方提供了一份安全扫描文件，提示两台服务器的openssh有漏洞，需要修补漏洞，鉴于此，更新系统的openssh到最新版本。

我们的服务器一般都是使用rhel 6.X x64的操作系统。

<!--more-->

# <一、安装必须的开发包

建议下载ISO文件到服务器，挂载到`/mnt`目录下，然后修改`cd.repo`，使用yum安装必须的开发工具和开发库，单独下载很麻烦啊。
```
mount -o loop -t iso9660 /home/rhel-server-6.4-x86_64-dvd.iso /mnt  
```

然后修改`/etc/yum.repos.d/cd.repo`
```
[CDROM]
name=isofile  
baseurl=file:///mnt  
enabled=1  
gpgcheck=0  
gpgkey=file:///mnt/RPM-GPG-KEY-redhat-release  
```

#  二、下载openssh包

打开[openssh](http://www.openssh.com/)官方站，下载for linux的tar包，目前最新版本是openssh 7.3p1，请到[openssh_mirror](http://openbsd.hk/pub/OpenBSD/OpenSSH/portable/)站下载。下载后，上传tar包到服务器的`/usr/src`。

# 三、其他工作（可选）

因为更新的时候，会断开ssh，所以建议打开telnet server，以免更新失败，无法连接服务器。
```
[root@localhost:~]# yum install telnet-server xinetd
```

编辑`/etc/xinetd.d/telnet`
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
		disable = no 
}
```

然后启动xinetd服务
```
[root@localhost:~]# service xinetd start
```

启动成功后，检查telnet是否已经在监听，检查23端口是否listen
```
[root@localhost:~]# netstat -lptun
```

然后就可以使用普通用户登录telnet了，登录成功后，使用 `sudo -i`进入root提示符。

# 四、编译openssh

进入`/usr/src`目录，解压tar包
```
[root@localhost:/usr/src]# tar xvf openssh-7.3p1.tar.gz
[root@localhost:/usr/src]# cd openssh-7.3p1
```

先config编译参数，注意prefix和sysconfdir参数，经过这样配置后，就替换了系统给中原有的openssh。
```
[root@localhost:/usr/src/openssh-7.3p1]# ./configure \
 --prefix=/usr \
 --sysconfdir=/etc/ssh \
 --with-ssl-dir=/usr/include/openssl \
 --with-md5-passwords \
 --with-pam \
 --with-mantype=man \
 --with-tcp-wrappers

[root@localhost:/usr/src/openssh-7.3p1]# make && make install
```

# 五、尝试启动服务

编译成功后，打开`/etc/ssh/sshd_config`文件，按照自己的要求，修改文件，这里不再叙述，之后，`service sshd restart`重启服务，查看服务是否启动成功使用`netstat -lptun`查看是否有监听端口，使用`ssh -V`查看版本。

附：

大致的步骤就是这样，可能在configure的过程中，会报错，报错的原因一般都是缺少开发库，像openssl-devel，zlib-devel都是必须的。

