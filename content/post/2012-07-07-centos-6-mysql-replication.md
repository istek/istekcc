---
title: CentOS 6.2MySQL主从同步配置
date: 2012-07-07T09:01:50+00:00
layout: post
categories:
  - Linux
tags:
  - mysql
---

### 主从服务器配置文件设置

**master：**
```
[root@localhost ~]# vi /etc/my.cnf
server-id = 1
```

确认server-id为1

**slave: **
```
[root@localhost ~]# vi /etc/my.cnf
server-id = 2
```

确认server-id为>1就可以了
<!--more-->
### 启动主从MYSQL服务
```
[root@localhost ~]#service mysql start
```

在master上建立slave用户并授权，192.168.0.199为slave的IP。
```
mysql> grant replication slave on *.* to 'slave'@'192.168.0.199' identified by '1234';
mysql> show master status;
------------------ ---------- -------------- ------------------
| File | Position | Binlog_Do_DB | Binlog_Ignore_DB |
------------------ ---------- -------------- ------------------
| mysql-bin.000025 | 511 | | |
 ------------------ ---------- -------------- ------------------
1 row in set (0.00 sec)
```

记住File、Position的值。

slave服务器操作：
```
mysql> change master to master_host='192.168.0.199',master_user='slave',master_password='1234',master_port=3306,master_log_file='mysql-bin.000025',master_log_pos=511;
```

启动slave
```
mysql> slave start;
```

### 检查运行状态
```
mysql> show slave status\G;
*************************** 1. row ***************************
Slave_IO_State: Waiting for master to send event
Master_Host: 192.168.0.199
Master_User: root
Master_Port: 3306
Connect_Retry: 60
Master_Log_File: mysql-bin.000025
Read_Master_Log_Pos: 511
Relay_Log_File: localhost-relay-bin.000002
Relay_Log_Pos: 657
Relay_Master_Log_File: mysql-bin.000025
Slave_IO_Running: Yes 此两项一定要为Yes,不然就表示未同步成功
Slave_SQL_Running: Yes
Replicate_Do_DB:
Replicate_Ignore_DB:
Replicate_Do_Table:
Replicate_Ignore_Table:
Replicate_Wild_Do_Table:
Replicate_Wild_Ignore_Table:
Last_Errno: 0
Last_Error:
Skip_Counter: 0
Exec_Master_Log_Pos: 511
Relay_Log_Space: 817
Until_Condition: None
Until_Log_File:
Until_Log_Pos: 0
Master_SSL_Allowed:
No Master_SSL_CA_File:
Master_SSL_CA_Path:
Master_SSL_Cert:
Master_SSL_Cipher:
Master_SSL_Key:
Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert:
No Last_IO_Errno: 0
Last_IO_Error:
Last_SQL_Errno: 0
Last_SQL_Error:
Replicate_Ignore_Server_Ids:
Master_Server_Id: 1
1 row in set (0.00 sec)
```

OK.主从同步工作了。

另外，在主从服务器的MY.CNF中，应配置二进制日志的格式为混合内容mixed，而不是statement。
```
log-bin=mysql-bin
binlog_format=mixed
```

这里介绍一些管理MYSQL主从同步的命令：

**停止MYSQL同步**
```
STOP SLAVE IO_THREAD;    #停止IO进程
STOP SLAVE SQL_THREAD;    #停止SQL进程
STOP SLAVE;            #停止IO和SQL进程
```

**启动MYSQL同步**
```
START SLAVE IO_THREAD;    #启动IO进程
START SLAVE SQL_THREAD;  #启动SQL进程
START SLAVE;             #启动IO和SQL进程
```

### 重置MYSQL同步
```
RESET SLAVE;
```

用于让从属服务器忘记其在主服务器的二进制日志中的复制位置, 它会删除master.info和relay-log.info文件，以及所有的中继日志，并启动一个新的中继日志,当你不需要主从的时候可以在从上执行这个操作。不然以后还会同步，可能会覆盖掉你的数据库，我以前就遇到过这样傻叉的事情。哈哈！

### 查看MYSQL同步状态
```
SHOW SLAVE STATUS;
```

这个命令主要查看Slave_IO_Running、Slave_SQL_Running、Seconds_Behind_Master、Last_IO_Error、Last_SQL_Error这些值来把握复制的状态。

### 临时跳过MYSQL同步错误

经常会朋友mysql主从同步遇到错误的时候，比如一个主键冲突等，那么我就需要在确保那一行数据一致的情况下临时的跳过这个错误，那就需要使用`SQL_SLAVE_SKIP_COUNTER = *n*`命令了，n是表示跳过后面的n个事件,比如我跳过一个事件的操作如下：
```
STOP SLAVE;
SET GLOBAL SQL_SLAVE_SKIP_COUNTER=1;
START SLAVE;
```

### 从指定位置重新同步

有的时候主从同步有问题了以后，需要从log位置的下一个位置进行同步，相当于跳过那个错误，这时候也可以使用CHANGE MASTER命令来处理，只要找到对应的LOG位置就可以,比如：
```
CHANGE MASTER TO MASTER_HOST=’10.1.1.75′,MASTER_USER=’replication’,MASTER_PASSWORD=’123456′,MASTER_LOG_FILE=’mysql-bin.000006′, MASTER_LOG_POS=106;
START SLAVE;
```
