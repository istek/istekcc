---
title: Linux下ARP绑定
date: 2010-04-28T02:55:27+00:00
layout: post
categories:
  - Linux
tags:
  - iptables
  - arp
---

## 一.ARP协议简介

当主机上的需要发送一个数据到一个目的IP时,设备驱动程序并不能理解这个IP地址. 系统需要将IP地址转换为网络地址,再传递给设备驱动程序发送出去. ARP(地址解析协议)就是这样的一种网络协议.用于将高层协议地址(IP地址)转换为物理网络地址.linux内核中保存有一个ARP表,里面保存有IP地址和MAC地址的对应关系.使用arp命令可以查看主机的ARP表.
```
# arp -a
 m1 (192.168.0.1) at 00:E0:4C:FF:D7:31 [ether] on eth0
```

## 二.ARP协议相关工具

### 1.arptables

#### a) arptables简介
<!--more-->
arptables是用户空间工具,用来管理 linux内核中的ARP规则表.这些规则用来检查ARP帧.arptables类似于iptables,但没有那么复杂.iptables工作于ip层,用于对ip包进行管理.arptables工作与arp协议层,用于对arp数据帧进行管理.arptables可以像iptables那样对arp数据帧进行各种规则设置,可以ACCEPT,DROP等.

#### b) arptables的安装

CentOS下安装方法：
```
yum install arptables_jf.i386
```

Ubuntu下安装方法:
```
sudo apt-get install arptables
```

#### c) arptables命令的语法
```
arptables [-t table] -[AD] chain rule-specification [options]
arptables [-t table] -[RI] chain rulenum rule-specification [options]
arptables [-t table] -D chain rulenum [options]
arptables [-t table] -[LFZ] [chain] [options]
arptables [-t table] -[NX] chain
arptables [-t table] -E old-chain-name new-chain-name
arptables [-t table] -P chain target [options]
```

**链(chain)**

kernel表是用来区分不同设置的不同功能.规则的每个设置叫做一个”链”.每个链是有一个排序了的规则列表,用来于ARP帧相匹配.如果一个规则与一个ARP帧相匹配,一个”操作说明”会提供需要进行哪些操作.操作说明叫做”目标”.然而,如果帧与当前链中的当前规则不匹配,则继续与链中的下一条规则进行检查.用户可以建立一个新的链,作为规则的”目标”来使用.

**目标(target)**

包含有一个ARP帧与一个帧处理说明的防火墙规则说明叫做一个”目标”.当一个帧与一个规则相匹配时,则kernel按”目标”的定义进行下一步操作.“目标”可以是:ACCEPT,DROP.CONTINUE,RETURN,扩展定义或用户指定规则. ACCEPT表示允许这个帧通过.DROP表示这个帧将被丢弃.CONTINUE表示继续进行下一条规则.这样可以很方便的计算,有多少帧经过了某个规则.RETURN表示不在这个链中继续进行匹配,返回到上一条链的下一条规则.

**表(table)**

在kernel中只有一个ARP表.这个表是一个过滤器.你可以在arptables命令中使用`-t filter`参数.使用时,-t参数必须是arptables命令的第一个参数. `-t,–table`是一个过滤器,在kernel中仅有这一个表,它包含2个(2.4.x内核)或3个(2.6.x内核)内建规则:INPUT(发送帧的源主机),OUTPUT(本地产生的帧),FORWARD(由桥代码转发的帧).2.4.X内核中没有FORWARD规则.

**arptables命令参数**

命令行参数分为几个部分:命令部分,杂项部分,规则说明,匹配扩展,监视器扩展.

命令(command)
```
    arptables命令参数用于指定在使用-t参数定义的表中的执行动作.如果你没有使用-t参数指定一个表名,则命令将应用于默认的过滤表.使用-Z命令时,命令行每次只能  使用一个命令.
    -A,–append 在指定的链结尾添加一个规则.
    -D,–delete 从指定的链中删除规则.这个命令有2种用法:
    1.指定要删除规则的序号,语法是: start_nr[:end_nr],可以使用负数.
    2.指定要删除规则的详细内容.
    -I,–insert 按序号,在指定的链中插入规则.如当前的序号为N,则可以使用-N到N+1作为插入序号.序号0表示表示在最后一条规则后插入新规则,等同于-A参数.
    -R,–replace 替换链中指定的规则.如果当前的序号是N,则指定的序号可以是1到N之间的数字.
    -P,–policy 在链中设置指定目标的策略,可以为:ACCEPT,DROP或RETURN.
    -F,–flush 清空指定的链.如果没有指定链,则所有的链都将被清空.清空链不会改变链的策略.
    -Z,–zero 将指定链的计数器置0.如果没有指定链,则所有计数器都将置0.-Z命令可以与-L命令结合使用.当同时使用-Z,-L命令时,计数器的值会先被打印出来,然后置0.
    -L,–list 输出指定链中的规则.如果没有指定链,所有将输出所有链中的规则.
    -N,–new-chain 建立新的用户链.用户链的数量没有限制,但用户链的名称最多可以有31个字符.
    -X,–delete-chain 删除指定的用户链.用户链中必须为空.如果没有指定用户链,则所有为空的用户链将被删除.
    -E,–rename-chain 重命名指定链.你可以重命名一个用户链,也可以重命名一个标准链名.
    -V,–version 显示arptables程序的版本号.
    -h,–help 输出语法帮助信息.
    -j,–jump 目标  规则的目标.可以是:ACCEPT,DROP,CONTINUE,RETURN,目标扩展或用户定义链名.
    -s,–source-ip [!] IP地址[/掩码]  源IP地址
    -d,–destination-ip [!] IP地址[/掩码] 目的IP地址
    –source-mac [!] MAC地址[/掩码] 源MAC地址
    –destination-mac [!] MAC地址[/掩码]  目的MAC地址.
    -i,–in-interface [!] 设备名 用于接收帧的接口(应用于INPUT,FORWARD链).–in-if是这个选项的别名.
    -o,–out-interface [!] 设备名 用于发送帧的接口(应用于OUTPUT,FORWARD链).–out-if是这个选项的别名.
    -l,–h-length 长度[/掩码] 硬件长度(单位字节).
    –opcode 代码[/掩码] 操作码(2字节).可以使用:1=请求,2=回复,3=反解析请求,4=反解析回复, 5=动态反解析请求,6=动态反解析回复,7=动态反解析错误,8=逆向ARP请求, 9=ARP_NAK
    –h-type 类型[/掩码] 硬件类型(2字节,十六进制).可以使用:1=Ethernet.
    –proto-type 类型[/mask] 协议类型(2字节).可以使用:0×800=IPv4
```

#### d) arptables使用实例
```
# arp -a
 m1 (192.168.0.1) at 00:E0:4C:FF:D7:31 [ether] on eth0
```

显示当前ARP表信息

当前ARP表中保存有一个主机的arp信息,m1主机,ip地址192.168.0.1
```
# arptables -D INPUT -s 192.168.0.1 -j DROP
```

设置arp规则,将所有192.168.0.1的arp包全部丢弃.
```
# arp -d 192.168.0.1 -i eth0
```

使用arp命令,删除arp表中的192.168.0.1的记录.
```
# arp -a
 m1 (192.168.0.1) at on eth0
```

arp表中已没有m1主机的信息.
```
# ping 192.168.0.1
 PING m1 (192.168.0.1) 56(84) bytes of data.
 From m2 (192.168.0.2) icmp_seq=2 Destination Host Unreachable
 From m2 (192.168.0.2) icmp_seq=3 Destination Host Unreachable
 From m2 (192.168.0.2) icmp_seq=4 Destination Host Unreachable
```

由于无法获得m1主机MAC信息,所以,无法与m1主机进行通信.
```
# arptables -D INPUT -s 192.168.0.1 -j DROP
```

删除arptables规则.
```
# arp -a
m1 (192.168.0.1) at 00:E0:4C:FF:D7:31 [ether] on eth0
```

arp表中重新记录了m1的MAC信息.
```
# ping 192.168.0.1
PING m1 (192.168.0.1) 56(84) bytes of data.
64 bytes from m1 (192.168.0.1): icmp_seq=1 ttl=64 time=0.315 ms
```

一般情况下，我们可以做如下的规则定义:
```
 arptables -F
 arptables -A IN –src-mac ! 网关MAC -j DROP
 arptables -A IN -s ! 网关IP -j DROP
```

查询arptables状态
```
 arptables -L
```

### 2.arpwatch

#### a) arpwatch简介

arpwatch用来监听网络中的ARP数据包并进行记录,同时将监听到的变化通过E-mail来报告给系统管理员. arpwatch使用pcap(3)来监听本地以太接口的arp数据包.

#### b) arpwatch的安装

CentOS下安装方法：
```
yum install arpwatch.i386
```

Ubuntu下安装方法:
```
sudo apt-get install arpwatch
```

#### c)arpwath命令的语法
```
 arpwatch [ -dN ]
 [ -f datafile ]
 [ -i interface ]
 [ -n net[/width ]]
 [ -r file ]
 [ -s sendmail_path ]
 [ -p ]
 [ -a ]
 [ -m addr ]
 [ -u username ]
 [ -R seconds ]
 [ -Q ]
 [ -z ignorenet/ignoremask ]
 -d 标记用来启用调试模式.
 -f 标记用来设置使用的数据库名,默认是arp.dat.
 -i 用来指定网络接口.
 -n 用于说明本地网络.
 -r 指定读取一个由tcpdump或pcapture生成信息文件,而不从网络接口中
 读取.\\-s 用于指定sendmail程序的路径.
 -p 指定禁用”混合模式”.网络接口不是”混合模式”时,ARP广播也可以通过.
 -a 默认情况下,arpwatch仅记录默认网络接口上,第一个IP地址子网的arp
 信息.使用-a参数,则记录网络接口上所有IP地址子网的arp信息.
 -m 指定一个用于接收变更信息的邮件地址.
```

#### d)arpwatch使用实例
```
# arpwatch -i eth0 -s root@localhost.localdomain
```

监听eth0接口,并将arp的变更信息,发送到本地的root用户邮箱中.

### 3.手动arp绑定的方式。

如果服务器作为内网网关使用，可以在内网网卡界面
```
ifconfig eth1 -arp
```

关闭ARP响应(假设eth1是内网网卡)
```
arp -f /etc/arp.list #设置静态ARP表
```

以下是ARP(8) 关于arp.list格式的描述，

> Cause the file filename to be read and multiple entries to be set in the ARP tables. Entries in the file should be of the form hostname ether_addr \[temp\] \[pub\] with argument meanings as given above. Leading whitespace and empty lines are ignored. A \`#&#8217; character will mark the rest of the line as a comment.