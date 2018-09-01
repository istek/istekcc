---
title: CENTOS6安装POSTFIX和dovecot
date: 2013-10-16T18:42:23+00:00
layout: post
categories:
  - 我的世界
tags: 
  - centos
  - postfix
---

## 1、安装系统包

使用yum安装postfix，dovecot包，启用系统服务。
```
yum install postfix dovecot
chkconfig postfix on
chkconfig dovecot on
```
<!--more-->
## 2、配置main.cf文件
```
vi /etc/postfix/main.cf
```

```
# 修改或增加如下行
myhostname = mail.fileregion.com
mydomain = fileregion.com
myorigin = $mydomain
inet_interfaces = all
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain
mynetworks = 127.0.0.0/8
home_mailbox = Maildir/
smtpd_sasl_type = dovecot
smtpd_sasl_path = private/auth
smtpd_sasl_auth_enable = yes
smtpd_recipient_restrictions = permit_mynetworks, permit_sasl_authenticated, reject_unauth_destination, permit
broken_sasl_auth_clients = yes
```

重启postfix:
```
/etc/init.d/postfix restart
```

## 3、配置Dovecot

配置`dovecot.conf`:
```
vi /etc/dovecot/dovecot.conf
```

将下面的行取消注释:
```
protocols = imap pop3
listen = *
```

配置认证机制：
```
vi /etc/dovecot/conf.d/10-auth.conf

修改下面的行:
disable_plaintext_auth = no
auth_mechanisms = plain login
```

配置mail_location:

```
vi /etc/dovecot/conf.d/10-mail.conf

修改或者取消注释下面的行:
mail_location = maildir:~/Maildir
```

配置auth服务:
```
vi /etc/dovecot/conf.d/10-master.conf

# 注释下面的行
#unix_listener auth-userdb {
    #mode = 0600
    #user =
    #group =
  #}
# change or setup on these line:
# Postfix smtp-auth
  unix_listener /var/spool/postfix/private/auth {
    mode = 0666
    user = postfix
    group = postfix
  }
```

配置POP3:

```
vi /etc/dovecot/conf.d/20-pop3.conf

# 取消注释:
pop3_uidl_format = %08Xu%08Xv
pop3_client_workarounds = outlook-no-nuls oe-ns-eoh
```

重启Dovecot服务 :
```
/etc/init.d/dovecot restart
```
