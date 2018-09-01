---
title: Sun Blade 100 做代理服务器的相关设置
date: 2006-03-12T07:52:15+00:00
layout: post
categories:
  - 我的世界
tags:
  - freebsd
  - proxy
---

## 一、 网络环境

1、主机A：
安装freebsd6.0，安装两块网卡rl0和gem0。rl0为对外网卡，IP：x.x.x.x ISP为我提供的IP地址，gem0为对内服务提供区域网卡，IP：192.168.80.1。

2、其他工作站N台。

## 二、编译内核

1、编译过程

```
cd /sys/sparc64/conf
cp GENERIC proxy
cd ../../compile/proxy
make kepend
make
make install
```

2、编辑proxy,加入一下选项：
```
options IPFILTER
options IPFILTER_LOG
options IPFILTER_DEFAULT_BLOCK
```
3、开始编译
```
#/usr/sbin/config proxy
# cd ../../compile/proxy
# make kepend
# make
# make install
```
4、编辑`/etc/rc.rc.conf`,打开以下选项：
```
ipfilter_enable=”YES” # Start ipf firewall
ipfilter_rules=”/etc/ipf.rules” # loads rules definition text file
ipmon_enable=”YES”  # Start IP monitor log
ipmon_flags=”-Ds”   # D = start as daemon
                    # s = log to syslog
                    # v = log tcp window, ack, seq
                    # n = map IP & port to names

gateway_enable=”YES” # Enable as LAN gateway
ipnat_enable=”YES” # Start ipnat function
ipnat_rules=”/etc/ipnat.rules” # rules definition file for ipnat
```

## 三、配置防火墙

1、 设置地址转换ipnat。在/etc下新建文件`ipnat.rules`,内容为：
```
map rl0 192.168.0.0/16 -> 0/32 proxy port ftp ftp/tcp
map rl0 192.168.0.0/24 -> 0/32 portmap tcp/udp auto
map rl0 192.168.0.0/24 -> 0/32
```
2、设置包过滤ipfilter。在/etc下新建文件`ipf.rules`,内容为：
```
block in log quick all with short
block in log quick all with ipopts
block in log quick all with frag
block in log quick all with opt lsrr
block in log quick all with opt ssrr
```
以上五句为过滤掉可能会带来安全问题的短数据包或具备路由信息的数据包以及防止非法扫描服务器
```
pass out on gem0 all
pass in on gem0 all
pass out quick on lo0 all
pass in quick on lo0 all
```
以上为内部网络界面和loopback网络界面可以自由发送和接受数据包
```
block out on rl0 all
```
以上为屏蔽外部网络界面向外发送数据包
```
block out on fxp0 from any to 192.168.0.0/16
block out quick on fxp0 from any to 0.0.0.0/8
block out quick on fxp0 from any to 169.254.0.0/8
block out quick on fxp0 from any to 10.0.0.0/8
block out quick on fxp0 from any to 127.16.0.0/12
block out quick on fxp0 from any to 127.0.0.0/8
block out quick on fxp0 from any to 192.0.2.0/24
block out quick on fxp0 from any to 204.152.64.0/23
block out quick on fxp0 from any to 224.0.0.0/3
```
以上为屏蔽不合法地址的输出数据
```
pass out log on rl0 proto tcp/udp from any to any keep state
pass out log on rl0 proto icmp all keep state
```
以上为允许TCP 、UDP、ICMP数据包向外发送出去，并且允许回应数据包发送回到内部网络
```
block in on fxp0 from 192.168.0.0/16 to any
block in quick on fxp0 from 10.0.0.0/8 to any
block in quick on fxp0 from 172.16.0.0/12 to any
block in quick on fxp0 from 127.0.0.0/8 to any
block in quick on fxp0 from 192.0.2.0/24 to any
block in quick on fxp0 from 169.254.0.0/16 to any
block in quick on fxp0 from 224.0.0.0/3 to any
block in quick on fxp0 from 204.152.64.0/23 to any
block in quick on fxp0 from x.x.x.x/32 to any
block in quick on fxp0 from any to x.x.x.0/32
block in quick on fxp0 from any to x.x.x.255/32
```
以上为屏蔽具备内部网络地址的数据包被转发到外部网络
```
pass in quick on rl0 proto tcp from any to any port = 80 flags S/SA keep state
pass in quick on rl0 proto tcp from any to any port = 22 flags S/SA keep state
pass in quick on rl0 proto tcp from any to any port = ftp flags S/SA keep state
pass in quick on rl0 proto tcp from any to any port = ftp-data flags S/SA keep state
pass in quick on rl0 proto tcp from any to any port 30000
```
以上为允许www和ftp进入，并且允许对ftp数据端口的数据进行转发
```
block in blog quick on rl0 all
```
禁止其他的连接进入fxp0
```
block in log quick on rl0 proto icmp from any to any icmp-type redir
block in log quick on rl0 proto icmp from any to any
block in log quick on rl0 proto icmp from any to any icmp-type echo
```
以上为禁止别人ping我得网络
```
block return-rst in log on fxp0 proto tcp from any to any flags S/SA
block return-icmp(net-unr) in log on fxp0 proto udp from any to any
```
以上对其他tcp请求，防火墙回应一个RST数据包关闭连接。对UDP请求，防火墙回应网络不可达到的ICMP包。

或者在`/etc/sysctl.conf`中加入：
```
net.inet.tcp.blackhole=2
net.inet.udp.blackhole=1
```
能够有效地避免端口扫描

3、在`/var/log/`建立文件`ipfilter.log`,并更改其属性为755，这样你的防火墙日志就记录到`/var/log/ipfilter.log`文件中，可以随时对其进行查看。
```
touch /var/log/ipfilter.log
```
4.重起代理服务器reboot

## 四、设置FTP服务器，使其支持被动连接（pasv）

1．Proftpd：编辑你的proftpd的配置文件`proftpd.conf`,加入一下内容：
```
MasqueradeAddress x.x.x.x
PassivePorts 30001 50000
```
2．Pure-ftpd:编辑你的FTP配置文件，加入一下内容：
```
PassivePortRange 30001 50000
ForcePassiveIP x.x.x.x #公网IP
```
3．Serv-U:
    1. 在serv-U的`本地服务器`―――`设置`―――`高级`―――`PASV端口范围`输入`30001 50000`；
    2. 在serv-U的`域`―――`你自己建立的域`―――`设置`―――`高级`选中`允许被动模式传送`，`使用IP`输入公网IP。