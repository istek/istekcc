---
title: 使用rsync同步服务器文件
date: 2012-07-07T08:42:14+00:00
layout: post
categories:
  - Linux
tags:
  - rsync
---

这篇指南告诉你如何使用rsync同步你的web服务器的文件至另一台备份服务器，假如主服务器Down掉，你可以立即手工却换至备份服务器。我们使用rsync进行服务器文件数据同步，通过使用cron的计划任务可以让rsync每X分钟进行同步一次，可以保证备份服务器的数据是最新的。

rsync只同步有变化的或者新的文件，也就是我们通常所说的增量备份。若主服务器上的文件被删除，它也可以从备份服务器删除文件。它能保证主服务器与备份服务器的文件和目录的权限和属主是一致的，但是前提要求是必须以root权限运行rsync。若主服务器上的文件或目录的属性发生了变化，rsync也能同步至备份服务器，始终保持一致。

本指南中rsync是通过更加安全的ssh方式进行同步，使用默认的ssh端口即可，不需要在防火墙中为rsync另开端口，但问题是ssh登录是需要密码的，若我们使用cron计划任务，这就需要人工输入密码。那么还有办法吗？幸运的是，ssh提供公钥登录，只需要在备份服务器生成公钥和私钥，然后将公钥保存至主服务器，这样SSH登录就不再需要密码，那么用cron计划任务来使用rsync同步文件数据就非常方便，可靠了。

现有服务器 server1.example.com 192.168.0.100

备份服务器 server2.example.com 192.168.0.101，
<!--more-->
需要同步的目录是server1.example.com的/var/www/html。

[_SSH密钥配置_](/howto-use-pubkey-authtication-under-centos5/)

**1.安装rsync**
```
yum install rsync
```

**2.在server1.example.com上创建一个普通用户**
```
useradd -d /home/someuser -m -s /bin/bash someuser
```

设置someuser的密码
```
passwd someuser
```

**3.测试rsync**

在server2.example.com上以root用户测试rsync
```
rsync -avz -e ssh someuser@server1.example.com:/var/www/html/ /var/www/html/
```

你应该能看到如下提示，输入Yes
```
[root@server2 ~]# rsync -avz -e ssh someuser@server1.example.com:/var/www/html/ /var/www/html/
The authenticity of host 'server1.example.com (192.168.0.100)' can't be established. RSA key fingerprint is 96:1e:9d:39:25:40:9d:89:53:f6:71:8f:fd:79:89:18.
Are you sure you want to continue connecting (yes/no)? <-- yes
```

之后输入someuser的密码，你会看到server1.example.com的/var/www/html目录被同步到server2.example.com的/var/www/html。

之后在两台服务器上，输入如下命令，可以看到两边文件和目录的权限都是一致的。
```
ls -al /var/www/html
```

**4.在server2.example.com上创建密钥**

现在在server2.example.com上以root用户创建公钥与私钥。
```
mkdir /root/rsync
ssh-keygen -t dsa -b 1024 -f /root/rsync/mirror-rsync-key
```

可以看到如下信息
```
[root@server2 ~]# ssh-keygen -t dsa -b 1024 -f /root/rsync/mirror-rsync-key
Generating public/private dsa key pair.
Enter passphrase (empty for no passphrase): <-- ENTER
Enter same passphrase again: <-- ENTER
Your identification has been saved in /root/rsync/mirror-rsync-key.
Your public key has been saved in /root/rsync/mirror-rsync-key.pub.
The key fingerprint is:
68:1e:9c:12:f1:f5:7f:53:d5:1d:d0:f2:dd:c2:88:f3 root@server2.example.com
The key's randomart image is:
+--[ DSA 1024]----+ |    .   .    .o.=| |     o . .   . .+| |    . .   .. oo +| |     o o  o.. ooo| |    . * S  o. o. | |     + .    E. . | |      .          | |                 | |                 | +-----------------+
[root@server2 ~]#
```

注意：在输入passphrase的地方，不要输入任何东西，否则会导致无法同步，这里直接回车即可。

之后，将server2.example.com的公钥拷贝至server1.example.com
```
scp /root/rsync/mirror-rsync-key.pub someuser@server1.example.com:/home/someuser/
```

**5.配置server1.example.com**

以`someuser`登录至server1.example.com，执行下面的命令
```
mkdir ~/.ssh
chmod 700 ~/.ssh
mv ~/mirror-rsync-key.pub ~/.ssh/
cd ~/.ssh
touch authorized_keys
chmod 600 authorized_keys
cat mirror-rsync-key.pub >> authorized_keys
```

authorized_keys的文件内容应类似下面
```
ssh-dss AAAAB3NzaC1[...]qqOyXtbUx7HOMEw== root@server2.example.com
```

现在我们只接受来自server2.example.com的连接，并且连接的用户只能运行rsync，所以将
```
command="/home/someuser/rsync/checkrsync",from="server2.example.com",no-port-forwarding,no-X11-forwarding,no-pty
```

加入到`authorized_keys`的开头，新的authorized_keys文件内容应类似如下
```
command="/home/someuser/rsync/checkrsync",from="server2.example.com",no-port-forwarding,no-X11-forwarding,no-pty ssh-dss AAAAB3NzaC1[...]qqOyXtbUx7HOMEw== root@server2.example.com
```

**注意：from=的后面必须使用FQDN标准的主机名，若使用IP地址，则rsync不会工作。**

现在创建`/home/someuser/rsync/checkrsync`脚本，它可以拒绝所有的命令，除了rsync。
```
mkdir ~/rsync vi ~/rsync/checkrsync
```

脚本内容如下
```
#!/bin/sh
case "$SSH_ORIGINAL_COMMAND" in *\&*) echo "Rejected" ;; *\(*) echo "Rejected" ;; *\{*) echo "Rejected" ;; *\;*) echo "Rejected" ;; *\<*) echo "Rejected" ;; *\`*) echo "Rejected" ;; rsync\ --server*) $SSH_ORIGINAL_COMMAND ;; *) echo "Rejected" ;; esac chmod 700 ~/rsync/checkrsync
```

**6.在server2.example.com上测试rsync**

现在我们必须在server2.example.com上测试一下rsync，看看是否还提示输入密码。
```
rsync -avz --delete --exclude=**/stats --exclude=**/error --exclude=**/files/pictures -e "ssh -i /root/rsync/mirror-rsync-key" someuser@server1.example.com:/var/www/html/ /var/www/html/
```

–delete表示在server1.example.com上删除的文件或目录，在server2.example.com上也删除，–exclude表示这些文件或目标不需要同步。

之后你应该能看到如下信息
```
[root@server2 ~]# rsync -avz --delete --exclude=**/stats --exclude=**/error --exclude=**/files/pictures -e "ssh -i /root/rsync/mirror-rsync-key" someuser@server1.example.com:/var/www/html/ /var/www/html/
receiving incremental file list sent 62 bytes received 48 bytes 73.33 bytes/sec total size is 20 speedup is 0.18
[root@server2 ~]#
```

OK，没有提示输入密码，这就是我们需要的！

**7.创建cron计划任务**

我们要在server2.example.com上创建自动同步的计划任务，以root身份运行`crontab -e`

创建计划任务
```
*/5 * * * * /usr/bin/rsync -azq --delete --exclude=**/stats --exclude=**/error --exclude=**/files/pictures -e "ssh -i /root/rsync/mirror-rsync-key" someuser@server1.example.com:/var/www/html/ /var/www/html/
```

这个计划任务说明每5分钟同步一次。

OK，这就是整个操作指南。

tips:

若SSH的端口为3000，那么crontab格式如下：
```
*/5 * * * * /usr/bin/rsync -azq --delete --exclude=**/stats --exclude=**/error --exclude=**/files/pictures -e "ssh -p 10000 -i /root/rsync/mirror-rsync-key" someuser@server1.example.com:/var/www/html/ /var/www/html/
```
