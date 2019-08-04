---
title: CentOS5下iptables+tc流量控制心得
date: 2010-04-14T05:19:15+00:00
layout: post
categories:
  - Linux
tags:
  - iptables
  - tc
---

上周一个客户的网关服务器出现故障，于是乎重新安装了操作系统，我选择了CentOS，也许有人会问为什么不用RouterOS，做流量控制很容易，都有现成的脚本可用，干嘛要那么费事？其实，我的想法是Linux下面的流量控制，我没有接触过，期望借此机会，能够学习一下，以便未来使用。抱着这样的目标，才有了真正的学习知识的动力。

因为Centos是RH的一个分支，所以获得的技术支持比较健全和稳定，再者很多人推荐，在服务器上安装使用很方便。国内的源也比较多，比如[网易](http://mirrors.163.com)，[搜狐](http://mirrors.sohu.com)都有。

操作系统的安装我就再不废话了，网上有很多教程。系统安装完成后，iptables就是已经在运行了，tc([traffic control](http://www.lartc.org/))也有了，cbq脚本也有，具体目录:
```
/sbin/tc
/sbin/cbq
```

在做之前，我曾在网上搜集过资料，但是很多都是抄来抄去的资料，没有多少价值。正确的步骤应该是，首先添加cbq需要的配置文件，然后compile配置文件，生成tc规则语句并且加载到接口上，之后启动cbq start进行流量控制。
<!--more-->
因客户有100台客户端，一台装有CentOS的服务器扮演了路由和网关的角色，服务器有eth0（连接内网），eth1（连接公网）。起初，按照cbq脚本的说明，写的配置文件，总是在compile的时候报错，错误消息如下：
```
    find: warning: you have specified the -maxdepth option after a non-option argument (, but options are not positional (-maxdepth affects tests specified before it as well as those specified after it). Please specify options before other arguments.

    find: warning: you have specified the -maxdepth option after a non-option argument (, but options are not positional (-maxdepth affects tests specified before it as well as those specified after it). Please specify options before other arguments.
```

搞的限速不能成功实现，于是问谷歌，看看有没有什么解决办法，后来终于从一个老外那发现了有价值的信息，是因为cbq脚本的一个错误导致的，修改好后就可以了。编辑`/sbin/cbq`文件，查找`Gather all DEVICE`字符串，修改的脚本如下：
```
### Get a list of configured classes
 CLASSLIST=`find $1 -maxdepth 1 \( -type f -or -type l\ ) -name 'cbq-*' \
 -not -name '*~' -printf "%f\n"| sort`
 [ -z "$CLASSLIST" ] &&
 cbq_failure "no configuration files found in $1!"

### Gather all DEVICE fields from $1/cbq-*
 DEVFIELDS=`find $1 -maxdepth 1 \( -type f -or -type l \) -name 'cbq-*' \
 -not -name '*~'| xargs sed -n 's/#.*//;
 s/[[:space:]]//g; /^DEVICE=[^,]*,[^,]*(,[^,]*)?/ \
 { s/.*=//; p; }'| sort -u`
```

修改之后，顺利生成限速规则，限速成功。下面我讲一下配置文件，配置文件主要是给cbq生成规则适用的，CentOS的默认路径在`/etc/sysconfig/cbq`这个目录下，当然可以改其他目录，编辑`/sbin/cbq`文件，搜索`CBQ_PATH`字符串，将后面的路径改为你希望放置cbq配置文件的目录。

配置文件的命名在cbq脚本文件中没有写，网络搜索的结果是`cbq-xxxx.yyy`，`XXXX`是`0000-ffff`的16进制数，`yyy`是所限制的接口名，比如eth0。如果大家有更加准确的信息，请与我分享一下。

我的配置文件内容如下：
```
DEVICE=eth0,100Mbit,20Mbit
RATE=1600Kbit
WEIGHT=160Kbit
PRIO=5
RULE=,192.168.10.21
```

eth0是局域网接口，100M链接，我是按照20Mbit的速度进行划分的。因客户端数量为100，所以限制下载速度200k。rule规则的意思就是不管从哪里来，到192.168.10.21的流量受到限制。

编辑完成后，执行
```
cbq compile && cbq start
```

之后，就可以使用`cbq stats`查看具体的统计情况了。

你也许会问iptables的应用在哪里？这个主要是应用在上传限制上，目前我正在测试脚本，如果正常，我将分享我的测试脚本。

下面是我的上传限制脚本，但是我还是不满意，因为它会对每一个IP生称两条mangle表规则，如果IP够多的话，会让mangle表很庞大，不知道哪位朋友有更好的优化办法？
```
#!/bin/sh
UPLOAD=800Kbit #上传限制
WEIGHT=80Kbit #最大可获得上传

INET=192.168.10.
IPS=11 #起始IP
IPE=110 #结束IP

IDEV=eth0 #内网接口
ODEV=eth1 #公网接口

COUNTER=$IPS
#empty any 20 rules on ODEV
/sbin/tc qdisc del dev $ODEV root handle 20:
#add a root class for sub class
/sbin/tc qdisc add dev $ODEV root handle 20: cbq bandwidth 10Mbit avpkt 1000
 while [ $COUNTER -le $IPE ]
 do
/sbin/tc class add dev $ODEV parent 20:0 classid 20:1$COUNTER cbq bandwidth 10Mbit rate $UPLOAD weight $WEIGHT prio 5 allot 1514 maxburst 20 avpkt 1000
/sbin/tc qdisc add dev $ODEV parent 20:1$COUNTER sfq quantum 1514b perturb 15
/sbin/tc filter add dev $ODEV parent 20:0 protocol ip prio 100 handle $COUNTER fw classid 20:1$COUNTER
COUNTER=` expr $COUNTER + 1 `
done

#define iptables

COUNTER=$IPS
 /sbin/iptables -t mangle -F
 while [ $COUNTER -lt $IPE ]
 do
 /sbin/iptables -t mangle -A PREROUTING -i $IDEV -s $INET$COUNTER -j MARK –set-mark $COUNTER
 /sbin/iptables -t mangle -A PREROUTING -i $IDEV -s $INET$COUNTER -j RETURN
 COUNTER=` expr $COUNTER + 1 `
 done
```