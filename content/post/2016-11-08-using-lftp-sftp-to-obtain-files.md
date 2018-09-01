---
title: 使用lftp与sftp获取远端文件脚本
date: 2016-11-08T21:26:36+00:00
layout: post
categories:
  - 我的世界
tags:
  - lftp
  - RHEL
---
因为工作的原因，需要每日使用sftp获取远程主机上的文件，所以就在想能不能脚本化，这样就不用天天定时定点的在服务器前面守着了。

lftp是linux下面的一个ftp客户端工具，使用起来非常方便，它的参数如下：
```
[zhou@localhost ~]$ lftp -h
Usage: lftp [OPTS] <site>
`lftp' is the first command executed by lftp after rc files
 -f <file>           execute commands from the file and exit
 -c <cmd>            execute the commands and exit
 --help              print this help and exit
 --version           print lftp version and exit
Other options are the same as in `open' command
 -e <cmd>            execute the command just after selecting
 -u <user>[,<pass>]  use the user/password for authentication
 -p <port>           use the port for connection
 <site>              host name, URL or bookmark name
```
<!--more-->
那么如何配置sftp使用呢？很简单，只需要`lftp -u user,pass sftp://IP:PORT`就可以了。

我的脚本是原先用ftp直接取文件进行处理的，后来远程服务器因为FTP漏洞，被迫关闭了FTP端口，只开放了SSH和SFTP，所以脚本就凑合着改了一下，先用起来再说。
```
#!/bin/bash
cd /gscbsp/TY
rm -fr /gscbsp/TY/*.zip
lftp -u 'agent_ppsf','asd&dfd' sftp://1*5.1*8.*0.*0:1*3*6 <<EOF
lcd /gscbsp/TY
mget /cson/agentposfile/0930/*2016*.zip
mget /cson/agentposfile/0931/*2016*.zip
mget /cson/agentposfile/0932/*2016*.zip
mget /cson/agentposfile/0933/*2016*.zip
mget /cson/agentposfile/0934/*2016*.zip
mget /cson/agentposfile/0935/*2016*.zip
mget /cson/agentposfile/0936/*2016*.zip
mget /cson/agentposfile/0937/*2016*.zip
mget /cson/agentposfile/0938/*2016*.zip
mget /cson/agentposfile/0939/*2016*.zip
mget /cson/agentposfile/0941/*2016*.zip
mget /cson/agentposfile/0943/*2016*.zip
mget /cson/agentposfile/0945/*2016*.zip
mget /cson/agentposfile/0947/*2016*.zip
close
bye
today=`date --date='0 days ago' +%Y%m%d`
#printf $today >> /gscbsp/get_ty.log
ls /gscbsp/TY/*.zip | xargs -n1 unzip
cd /afms/bestpay
find . -name "*.zip"|xargs rm -rf "*"
find . -name "*.txt"|xargs rm -rf "*"
find . -name "*.xls"|xargs rm -rf "*"
cp /gscbsp/TY/*.* /afms/bestpay rm -fr /gscbsp/TY/*.txt
rm -fr /gscbsp/TY/*.xls
printf "$today Job is done! \n" >> /gscbsp/bin/get_ty.log
```

通过脚本达到了我的基本要求。
