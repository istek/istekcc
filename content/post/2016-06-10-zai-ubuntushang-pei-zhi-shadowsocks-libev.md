---
title: 在Ubuntu上配置shadowsocks-libev
date: 2016-06-10T06:31:03+00:00
layout: post
categories:
  - 我的世界
tags:
  - Ubuntu
---
[shadowsocks-libev](https://github.com/shadowsocks/shadowsocks-libev)是一款轻量级且功能丰富，面向嵌入式设备和低端VPS的SOCKS5代理软件。该软件以C语言开发，占用存储空间非常小，大概就几兆，能处理数千的连接。在运行时，占用内存约600K，CPU使用率不会超过5%。目前该开源软件由[@madeya](https://github.com/madeye)和[@linusyang](https://github.com/linusyang)负责维护。

特色功能：

* 一次验证
* UDP中转
* 多用户
* 管理API
* ss-redir
* ss-tunel
* Fast Open

<!--more-->
以下安装配置在安装了ubuntu 15.04 minimal操作系统的VPS上实验成功。

有两种办法安装shadowsocks-libev：一种是将[shadowsocks.org](https://shadowsocks.org/)的软件源添加到系统，使用apt安装deb包，另一种是自己编译源代码。这里，我们先说第一种办法。

### 一、使用apt安装shadowsocks-libev {#aptshadowsockslibev}

首先，导入公钥到操作系统。_注意，以下操作以root用户执行。_
```
wget -O- http://shadowsocks.org/debian/1D27208A.gpg | apt-key add -
```

然后，添加软件源
```
echo "deb http://shadowsocks.org/debian wheezy main" >> /etc/apt/sources.list  
```

最后更新软件源，安装shadowsocks-libev
```
apt-get update && apt-get install shadowsocks-libev  
```

OK，shadowsocks-libev已经安装好了，接下来就是修改配置文件，启用服务且启动它了。
```
vi /etc/shadowsocks-libev/config.js  
```

修改配置文件，我们需要一次验证，内容如下：
```
    {
        "server":"0.0.0.0",
        "server_port":9098,
        "password":"sample",
        "method":"aes-256-cfb",
        "fast_open":false,
        "auth":true
    }
```

|参数|	说明|
|---|---|
|server|	服务器IP地址,默认4个0就Ok|
|server_port|	服务器服务端口|
|password|	连接所需的密码|
|method	|支持的加密方法：table, rc4, rc4-md5, aes-128-cfb, aes-192-cfb, aes-256-cfb,bf-cfb,camellia-128-cfb, camellia-192-cfb,camellia-256-cfb, cast5-cfb, des-cfb, idea-cfb,rc2-cfb, seed-cfb, salsa20 and chacha20|
|fast_open	|仅在服务端和客户端均为Linux时可以使用|
|auth	|一次验证**（推荐）**|

设置开机自启动以及启动服务
```
systemctl enable shadowsocks-libev  
systemctl restart shadowsocks-libev  
```

### 从源码编译 {#}

首先从仓库取回代码
```
git clone https://github.com/shadowsocks/shadowsocks-libev.git  
```

进入目录，安装编译所需的依赖包
```
cd shadowsocks-libev
apt-get install build-essential autoconf libtool libssl-dev awk debhelper dh-systemd init-system-helpers pkg-config  
```

然后开始编译和安装
```
dpkg-buildpackage -b -us -uc -i  
```

deb在上级目录，直接安装
```
cd ..

dpkg -i shadowsocks-libev*.deb  
```

安装完成后，修改配置文件，最后启用服务。开机服务的脚本在`debian`目录下，可以看一下INSTALL文件。

附： salsa20 and chacha20安装方法

```
apt-get install build-essential  
wget https://github.com/jedisct1/libsodium/releases/download/1.0.8/libsodium-1.0.8.tar.gz  
tar xf libsodium-1.0.8.tar.gz && cd libsodium-1.0.8  
./configure && make -j2
make install  
ldconfig
```
