---
title: Ubuntu网络配置
date: 2009-10-31T10:24:11+00:00
layout: post
categories:
  - Linux
tags:
  - network
---

### 1.以DHCP方式配置网卡

`/etc/network/interfaces`打开后里面可设置DHCP或手动设置静态ip。前面auto eth0，让网卡开机自动挂载。编辑文件`/etc/network/interfaces`:
```
sudo vi /etc/network/interfaces
```

并用下面的行来替换有关eth0的行:
```
# The primary network interface – use DHCP to find our address
 auto eth0
 iface eth0 inet dhcp
```

用下面的命令使网络设置生效:
```
sudo /etc/init.d/networking restart
```

也可以在命令行下直接输入下面的命令来获取地址
```
sudo dhclient eth0
```
<!--more-->
### 2. 为网卡配置静态IP地址

编辑文件`/etc/network/interfaces`:
```
sudo vi /etc/network/interfaces
```

并用下面的行来替换有关eth0的行:
```
# The primary network interface
 auto eth0
 iface eth0 inet static
 address 192.168.3.90
 gateway 192.168.3.1
 netmask 255.255.255.0
 #network 192.168.3.0
 #broadcast 192.168.3.255
```

将上面的ip地址等信息换成你自己就可以了.用下面的命令使网络设置生效:
```
sudo /etc/init.d/networking restart
```

### 3. 设定第二个IP地址(虚拟IP地址)

编辑文件·/etc/network/interfaces·:
```
sudo vi /etc/network/interfaces
```

在该文件中添加如下的行:
```
auto eth0:1
iface eth0:1 inet static
address 192.168.1.60
netmask 255.255.255.0
network x.x.x.x
broadcast x.x.x.x
gateway x.x.x.x
```

根据你的情况填上所有诸如address,netmask,network,broadcast和gateways等信息.

用下面的命令使网络设置生效:
```
sudo /etc/init.d/networking restart
```

### 4.设置主机名称(hostname)

使用下面的命令来查看当前主机的主机名称:
```
sudo /bin/hostname
```

使用下面的命令来设置当前主机的主机名称:
```
sudo /bin/hostname newname
```

系统启动时,它会从`/etc/hostname`来读取主机的名称.

关于设置主机名称的更多信息,请访问这里

### 5. 配置DNS

首先,你可以在`/etc/hosts`中加入一些主机名称和这些主机名称对应的IP地址,这是简单使用本机的静态查询.

要访问DNS 服务器来进行查询,需要设置`/etc/resolv.conf`文件.

假设DNS服务器的IP地址是`192.168.3.2`, 那么`/etc/resolv.conf`文件的内容应为:
```
search test.com
nameserver 192.168.3.2
```

### 6. ifconfig

LINUX下的网络配置命令是ifconfig类似于WINDOWS命令行中的ipconfig。可以使用ifconfig命令来配置并查看网络接口的配置情况。
```
（1） 配置eth0的IP地址， 同时激活该设备。
#ifconfig eth0 192.168.1.10 netmask 255.255.255.0 up
（2） 配置eth0别名设备eth0:1的IP地址，并添加路由。
#ifconfig eth0 192.168.1.3
#route add –host 192.168.1.3 dev eth0:1
（3） 激活设备。
#ifconfig eth0 up
（4） 禁用设备。
#ifconfig eth0 down
（5） 查看指定的网络接口的配置。
#ifconfig eth0
（6） 查看所有的网络接口配置。
#ifconfig
```

### 7. route

可以使用route命令来配置并查看内核路由表的配置情况。
```
 （1） 添加到主机的路由。
 #route add –host 192.168.1.2 dev eth0:0
 #route add –host 10.20.30.148 gw 10.20.30.40
 （2） 添加到网络的路由。
 #route add –net 10.20.30.40 netmask 255.255.255.248 eth0
 #route add –net 10.20.30.48 netmask 255.255.255.248 gw 10.20.30.41
 #route add –net 192.168.1.0/24 eth1
 （3） 添加默认网关。
 #route add default gw 192.168.1.1
 （4） 查看内核路由表的配置。
 #route
 （5）删除路由。
 #route del –host 192.168.1.2 dev eth0:0
 #route del –host 10.20.30.148 gw 10.20.30.40
 #route del –net 10.20.30.40 netmask 255.255.255.248 eth0
 #route del –net 10.20.30.48 netmask 255.255.255.248 gw 10.20.30.41
 #route del –net 192.168.1.0/24 eth1
 #route del default gw 192.168.1.1
```
 对于1和2两点可使用下面的语句实现：
```
 Ifconfig eth0 172.16.19.71 netmask 255.255.255.0
 Route 0.0.0.0 gw 172.16.19.254
 Service network restart
```

### 8. traceroute

可以使用traceroute命令显示数据包到达目的主机所经过的路由。

例如：
```
traceroute www.sina.com.cn
```

### 9.ping

可以使用ping 命令来测试网络的连通性。

例如：
```
ping www.sina.com.cn
ping –c 4 192.168.1.12
```

### 10. netstat

可以使用netstat命令来显示网络状态信息。

例如：
```
（1） 显示网络接口状态信息。
#netstat –i
（2） 显示所有监控中的服务器的Socket和正使用Socket的程序信息。
#netstat –lpe
（3） 显示内核路由表信息。
#netstat –r
#netstat –nr
（4） 显示TCP/UDP传输协议的连接状态。
#netstat –t
#netstat –u
```

### 11. hostname

可以使用hostname命令来更改主机名。例如；
```
#hostname myhost
```

### 12.arp

可以使用arp命令来配置并查看arp缓存。例如：
```
（1） 查看arp缓存。
#arp
（2） 添加一个IP地址和MAC地址的对应记录。
#arp –s 192.168.33.15 00:60:08:27:CE:B2
（3） 删除一个IP地址和MAC地址的对应缓存记录。
#arp –d192.168.33.15
```
