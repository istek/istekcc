---
title: HP-UX下OC4J的故障处理二例
date: 2012-11-23T00:09:14+00:00
layout: post
categories:
  - 我的世界
tags:
  - hpux
  - oc4j
---

我们这个平台使用的是HP的小机rx6600，因为跑java程序，所以选用了oracle公司的oc4j这个中间件，顺便吐槽一下,这个oc4j真的太麻烦，太讨厌了，搞不懂公司当初在实施的时候，为什么不采用weblogic或者是websphere这类的中间件。

前几天因为服务器双机出现故障，硬件厂家来了工程师处理好了双机问题。我这边因为修正了一个接口地址，本想重启应用，可应用死活启动不了。

**故障一：**

故障现象：

看`control.sh.log`内容，报如下错误：
```
opmnctl: starting opmn and all managed processes...
RCV: Permission denied
Communication error with the OPMN server local port.
Check the OPMN log files
```

之后打开`opmn.log`，查看日志内容，发现如下错误：
```
12/11/16 19:22:14 [ons-internal] ONS server initiated
12/11/16 19:22:14 [pm-internal] PM state directory exists: /oracle/oracle10g/product/10.1.3.1/OracleAS_1/opmn/logs/states
12/11/16 19:22:14 [pm-internal] OPMN server ready. Request handling enabled.
12/11/16 19:22:14 [ons-listener] 127.0.0.1,6100: BIND (Address already in use)
12/11/16 19:22:14 [ons-connect] Local connection 127.0.0.1,6100 invalid form factor<unknown>
```
<!--more-->
从日志可以看出，ons监听器无法绑定地址到本地的6100端口，经过查询OTN，发现应该是opmn进程没有退出，导致ons监听器启动不了。

解决办法：

1.在双机未启动的情况下，查看opmn进程是否存在：
```
ps -ef|grep 'opmn -d'|grep -v 'grep'
```

2.若存在进程，注意一下进程的所属用户，并且使用kill -9杀掉这个进程：
```
kill -9 <PID>
```

3.查看`.formfactor`的权限和属主，若不正确使用相应的命令处理：
```
$ cd $ORACLE_HOME/opmn/conf
$ ls -ld1 .*
 drwx------ 3 oracle oinstall 4096 Sep 7 09:08 .
 drwx------ 8 oracle oinstall 4096 May 30 09:00 ..
 -r-------- 1 root root 21 Sep 7 09:08 .formfactor
$ chown oracle：oinstall .formfactor
```

4.启动双机，检查应用是否启动。

PS:出现这种故障的情况，是因为opmn进程可能以其他用户启动的，而正确的属主用户无法停止，如本例中是oracle用户。还有就是`.formfactor`的权限问题，也要注意。

**故障二：**

故障现象：

在control.sh.log中，看到如下错误：
```
Error
-->Process (index=1,uid=528,pid=3990)
failed to start a managed process after the maximum retry limit

Log:
/oracle/oracle10g/product/10.1.3.1/OracleAS_1/opmn/logs//HTTP_Server~1.log
logout
ERROR: Function customer_defined_run_cmds
ERROR: Failed to RUN customer commands
```

查看opmn.log，发现如下问题：
```
gstas.gstasap1~default_group~home~default_group~527:3797
Status: Stop
Operation: request (time out while waiting for a managed process to stop)
ErrFile: /oracle/oracle10g/product/10.1.3.1/OracleAS_1/opmn/logs//default_group~home~default_group~1.log
```

之后查看`HTTP_Server~1.log`，发现非常多的startssl信息：
```
--------
12/11/20 20:50:22 Start process
--------
/oracle/oracle10g/product/10.1.3.1/OracleAS_1/Apache/Apache/bin/apachectl startssl: execing httpd

--------
12/11/20 20:50:32 Start process
--------
/oracle/oracle10g/product/10.1.3.1/OracleAS_1/Apache/Apache/bin/apachectl startssl: execing httpd
```

解决方案：

起初我以为是oc4j中的apache配置文件出现了问题，于是在`opmn.xml`中禁用了SSL，但是故障现象依旧出现，我将修改过的`opmn.xml`还原回去，然后继续查，发现`httpd.pid`文件的属主不正确，竟然属于root用户，这明显有问题，应该是属于oracle用户的，于是我找到这个`httpd.pid`文件，将其mv至其他目录，重启应用，yeah，应用正常启动了。

**总结：**

在生产环境中，出现应用启动失败的情况，一点都不可怕，而且也请不要慌张，所有错误都是有迹可循的，仔细查看日志，分析错误原因，就能找到正确的解决办法，当然，找到问题却解决不了的时候，也还是可以google的。呵呵。oc4j的几个重要日志一定要注意：

1.opmn的日志：

> /oracle/oracle10g/product/10.1.3.1/OracleAS_1/opmn/logs/opmn.log

2.apache的日志:

> /oracle/oracle10g/product/10.1.3.1/OracleAS_1/Apache/Apache/logs

3.双机cmcluster的控制脚本的日志:

> /etc/cmcluster/app_pkg[应用名]/control.sh.log

4.你的java应用的日志.