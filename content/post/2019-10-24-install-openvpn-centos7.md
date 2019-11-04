---
title: "CentOS 7 配置 OpenVPN"
date: 2019-10-24T17:17:32+08:00
draft: false
categories:
 - Linux
tags:
 - openvpn
featured_image: https://oss.zhoutao.ren/wp-content/uploads/2019/10/about_text_logo.png
---

## 使用环境：

- openvpn服务端安装在centos7系统平台上；
- openvpn客户端安装在windows平台上；

## 先决条件
### 安装软件
```
# yum install openvpn easy-rsa
```

<!--more-->

### 准备相关目录和配置文件
复制easy-rsa的文件：`easyrsa`、`openssl-easyrsa.cnf`、`x509-types`到`/etc/openvpn/easy-rsa`
```
# mkdir -p /etc/openvpn/easy-rsa
# cp /usr/share/doc/easy-rsa-3.0.6/vars.example /etc/openvpn/easy-rsa/vars
# cp -r /usr/share/easy-rsa/3.0.6/* /etc/openvpn/easy-rsa/
```
复制服务端配置文件到`/etc/openvpn`目录
```
# cp /usr/share/doc/openvpn-2.4.5/sample/sample-config-files/server.conf /etc/openvpn/
```
编辑vars文件：
```
set_var EASYRSA_REQ_COUNTRY "CN"
set_var EASYRSA_REQ_PROVINCE "Beijing"
set_var EASYRSA_REQ_CITY "Beijing"
set_var EASYRSA_REQ_ORG "OpenVPN CA"
set_var EASYRSA_REQ_EMAIL "mail@example.com"
set_var EASYRSA_REQ_OU  "example VPN"
```
## 创建服务器端证书和key
### 目录初始化
```
# cd /etc/openvpn/easy-rsa/
# ./easyrsa init-pki
```
### 创建CA根证书
```
# ./easyrsa build-ca
Enter PEM pass phrase: //输入2次pem密码，并记住（输入的pem密码是openvpn，后面会用到）
........
Common Name (eg: your user, host, or server name) [Easy-RSA CA]: //输入CA名称
```
回车后显示：
```
CA creation complete and you may now import and sign cert requests.
Your new CA certificate file for publishing is at:
/etc/openvpn/easy-rsa/pki/ca.crt
```
### 创建服务器端证书
```
# ./easyrsa gen-req server nopass
Common Name (eg: your user, host, or server name) [server]: //输入服务器名,这里输入node2
```
输入回车后显示：
```
Keypair and certificate request completed. Your files are:
req: /etc/openvpn/easy-rsa/pki/reqs/server.req
key: /etc/openvpn/easy-rsa/pki/private/server.key
```
### 签署服务器端证书：
```
# ./easyrsa sign server server
```
回车后
```
Confirm request details: （输入yes）
Enter pass phrase for /etc/openvpn/easy-rsa/pki/private/ca.key: （输入之前CA根证书的pem密码是openvpn）
```
回车后显示：
```
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName :ASN.1 12:'node2'
Certificate is to be certified until Apr 4 16:04:29 2028 GMT (3650 days)

Write out database with 1 new entries
Data Base Updated

Certificate created at: /etc/openvpn/easy-rsa/pki/issued/server.crt
```
### 创建Diffie-Hellman
```
# ./easyrsa gen-dh
```
回车后，等的时间稍微长一点，最后显示：
```
DH parameters of size 2048 created at /etc/openvpn/easy-rsa/pki/dh.pem
```
### 生成ta密钥文件
```
# openvpn --genkey --secret /etc/openvpn/easy-rsa/ta.key
```
若没有ta.key文件，会报如下错误信息：
```
Sat Apr 7 12:53:37 2018 WARNING: cannot stat file 'ta.key': No such file or directory (errno=2)
Options error: --tls-auth fails with 'ta.key': No such file or directory (errno=2)
Options error: Please correct these errors.
Use --help for more information.
```
## 创建客户端证书及key ：

### 生成客户端证书
```
# ./easyrsa gen-req client
```
回车后显示
```
Enter PEM pass phrase: //输入密码，密码是之后客户端连接服务器要用的（输入的是vpnclient）
Common Name (eg: your user, host, or server name) [client]: //输入的是client，后面会用到
```
回车后显示：
```
Keypair and certificate request completed. Your files are:
req: /etc/openvpn/easy-rsa/pki/reqs/client.req
key: /etc/openvpn/easy-rsa/pki/private/client.key
```
### 签署客户端证书
```
# ./easyrsa sign client client
```
回车后，输入yes
```
Enter pass phrase for /etc/openvpn/easy-rsa/pki/private/ca.key: （输入的是openvpn）
```
**注意：**
这里生成client所以第一个client位置必须为client，第二个参数client要与之前导入名字一致，导入的时候会要求输入密码，这个密码是第一次设置的根证书的密码，不要输错；因为openvpn是一个客户端对应一组证书密钥文件的；

回车后显示：
```
Check that the request matches the signature
Signature ok
The Subject's Distinguished Name is as follows
commonName :ASN.1 12:'client'
Certificate is to be certified until Apr 4 16:38:37 2028 GMT (3650 days)

Write out database with 1 new entries
Data Base Updated

Certificate created at: /etc/openvpn/easy-rsa/pki/issued/client.crt
```

## 配置服务端

拷贝服务器端所需文件到各自位置：
```
# cp pki/ca.crt /etc/openvpn/
# cp pki/private/server.key /etc/openvpn/
# cp pki/issued/server.crt /etc/openvpn/
# cp pki/dh.pem /etc/openvpn/
# cp /etc/openvpn/easy-rsa/ta.key /etc/openvpn/
```
修改vpn配置文件：
```
# egrep -v "^$|^#|^;" /etc/openvpn/server.conf
port 1194
proto udp
dev tun
ca /etc/openvpn/ca.crt
cert /etc/openvpn/server.crt
key /etc/openvpn/server.key # This file should be kept secret
dh /etc/openvpn/dh.pem
server 10.8.0.0 255.255.255.0
ifconfig-pool-persist ipp.txt
push "redirect-gateway def1 bypass-dhcp"
push "dhcp-option DNS 8.8.8.8"
push "dhcp-option DNS 208.67.220.220"
keepalive 10 120
tls-auth ta.key 0 # This file is secret
cipher AES-256-CBC
comp-lzo
max-clients 100
persist-key
persist-tun
status openvpn-status.log
verb 3
explicit-exit-notify 1 
```
启动openvpn服务器：
```
# openvpn /etc/openvpn/server.conf &
```
启动成功后显示：
```
Sat Apr 7 13:00:23 2018 OpenVPN 2.4.5 x86_64-redhat-linux-gnu [Fedora EPEL patched] [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Mar 1 2018
Sat Apr 7 13:00:23 2018 library versions: OpenSSL 1.0.2k-fips 26 Jan 2017, LZO 2.06
Sat Apr 7 13:00:23 2018 Diffie-Hellman initialized with 2048 bit key
Sat Apr 7 13:00:23 2018 Outgoing Control Channel Authentication: Using 160 bit message hash 'SHA1' for HMAC authentication
Sat Apr 7 13:00:23 2018 Incoming Control Channel Authentication: Using 160 bit message hash 'SHA1' for HMAC authentication
Sat Apr 7 13:00:23 2018 ROUTE_GATEWAY 192.168.255.1/255.255.255.0 IFACE=eno16777736 HWADDR=00:0c:29:ef:e4:a7
Sat Apr 7 13:00:23 2018 TUN/TAP device tun0 opened
Sat Apr 7 13:00:23 2018 TUN/TAP TX queue length set to 100
Sat Apr 7 13:00:23 2018 do_ifconfig, tt->did_ifconfig_ipv6_setup=0
Sat Apr 7 13:00:23 2018 /sbin/ip link set dev tun0 up mtu 1500
Sat Apr 7 13:00:23 2018 /sbin/ip addr add dev tun0 local 10.8.0.1 peer 10.8.0.2
Sat Apr 7 13:00:23 2018 /sbin/ip route add 10.8.0.0/24 via 10.8.0.2
Sat Apr 7 13:00:24 2018 Could not determine IPv4/IPv6 protocol. Using AF_INET
Sat Apr 7 13:00:24 2018 Socket Buffers: R=[212992->212992] S=[212992->212992]
Sat Apr 7 13:00:24 2018 UDPv4 link local (bound): [AF_INET][undef]:1194
Sat Apr 7 13:00:24 2018 UDPv4 link remote: [AF_UNSPEC]
Sat Apr 7 13:00:24 2018 MULTI: multi_init called, r=256 v=256
Sat Apr 7 13:00:24 2018 IFCONFIG POOL: base=10.8.0.4 size=62, ipv6=0
Sat Apr 7 13:00:24 2018 IFCONFIG POOL LIST
Sat Apr 7 13:00:24 2018 Initialization Sequence Completed
```
或使用systemctl启动：
```
systemctl -f enable openvpn@server.service
#设置启动文件
systemctl start openvpn@server.service
```
## 配置客户端
拷贝客户端所需文件到本地windows电脑
```
# cp pki/ca.crt /root/client/
# cp pki/issued/client.crt /root/client/
# cp /root/client/pki/private/client.key /root/client/
# cp /etc/openvpn/easy-rsa/ta.key /root/client/
# cp /usr/share/doc/openvpn-2.4.7/sample/sample-config-files/client.conf /root/client/client.ovpn
```
从centos7上把ca.crt、client.crt、client.key、ta.key、client.conf并改名为client.ovpn五个文件，放在安装目录下的config目录里即可

client.ovpn配置文件内容：
```
client
dev tun
proto tcp-client

remote 192.168.268.12 1194

resolv-retry infinite
nobind

;user nobody
;group nobody

;route 192.168.0.0 255.255.252.0

persist-key
persist-tun

ca ca.crt
cert client.crt
key client.key
ns-cert-type server
tls-auth ta.key 1
comp-lzo
auth-nocache
verb 4
```
openvpn客户端登录：
VPN连接过程中，会提示输入密码，输入前文生成客户端证书时，填写的密码，即vpnclient。

