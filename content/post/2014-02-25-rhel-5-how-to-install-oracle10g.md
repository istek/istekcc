---
title: 'RHEL 5: How to install Oracle10g'
date: 2014-02-25T05:58:34+00:00
layout: post
categories:
  - 我的世界
tags:
  - RHEL
  - 数据库
---
该文档适用于RHEL 5系列操作系统下面安装Oracle10G。

## 一、安装前的准备工作

安装oracle前，内核参数调整，添加到`/etc/sysctl.conf`：
```
# oracle kernel settings
fs.suid_dumpable = 1
fs.aio-max-nr = 1048576
fs.file-max = 6553600
kernel.shmall = 2097152
kernel.shmmax = 2147483648
kernel.shmmni = 4096
kernel.sem = 250 32000 100 128
net.ipv4.ip_local_port_range = 1024 65535
net.core.rmem_default = 4194304
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 262144
```
<!--more-->
安装oracle前，`/etc/security/limits.conf`的调整
```
# oracle settings
oracle              soft    nproc   2047
oracle              hard    nproc   16384
oracle              soft    nofile  4096
oracle              hard    nofile  65536
oracle              soft    stack   10240
```

## 二、系统软件
```
cd /media/cdrom/Server
rpm -Uvh binutils-2.*
rpm -Uvh compat-libstdc++-33*
rpm -Uvh compat-libstdc++-33*.i386.rpm
rpm -Uvh elfutils-libelf*
rpm -Uvh gcc-4.*
rpm -Uvh gcc-c++-4.*
rpm -Uvh glibc-2.*
rpm -Uvh glibc-common-2.*
rpm -Uvh glibc-devel-2.*
rpm -Uvh glibc-headers-2.*
rpm -Uvh ksh*
rpm -Uvh libaio-0.*
rpm -Uvh libaio-devel-0.*
rpm -Uvh libgomp-4.*
rpm -Uvh libgcc-4.*
rpm -Uvh libstdc++-4.*
rpm -Uvh libstdc++-devel-4.*
rpm -Uvh make-3.*
rpm -Uvh sysstat-7.*
rpm -Uvh unixODBC-2.*
rpm -Uvh unixODBC-devel-2.*
rpm -Uvh numactl-devel-*
rpm -Uvh libXp-*
```

## 三、添加oracle使用的用户和用户组

添加oracle用户和组
```
groupadd oinstall
groupadd dba
groupadd oper
groupadd asmadmin
useradd -g oinstall -G dba,oper,asmadmin oracle
passwd oracle
gpasswd -a oracle wheel 将oracle用户加入wheel组 [可选]
```

## 四、创建目录
```
mkdir -p /oracle/product/10.2/db_1
chown -R oracle:oinstall /oracle
chmod -R 775 /oracle
```

## 五、修改oracle用户的环境变量

修改oracle用户的shell环境变量, 编辑`~/.bash_profile`加入下面的内容
```
# oracle settings
TMP=/tmp; export TMP
TMPDIR=$TMP; export TMPDIR
ORACLE_HOSTNAME=localhost.localdomain; export ORACLE_HOSTNAME
ORACLE_UNQNAME=test; export ORACLE_UNQNAME
ORACLE_BASE=/oracle; export ORACLE_BASE
ORACLE_HOME=$ORACLE_BASE/product/10.2/db_1; export ORACLE_HOME
ORACLE_SID=test; export ORACLE_SID
PATH=/usr/sbin:$PATH; export PATH
PATH=$ORACLE_HOME/bin:$PATH; export PATH
LD_LIBRARY_PATH=$ORACLE_HOME/lib:/lib:/usr/lib; export LD_LIBRARY_PATH
CLASSPATH=$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib; export CLASSPATH
```

```
source /home/oracle/.bash_profile
```

## 六、安装
```
xhost +
su - oracle
gunzip 10201_database_linux_x86_64.cpio.gz
cpio -idmv < 10201_database_linux_x86_64.cpio
cd database
./runInstaller
```

## 七、开启oracle10g的开机自动启动服务（以下都用root身份修改）

1. 打开终端执行`vi /etc/oratab`，把`orcl:/opt/oracle/oracle/product/10g:N`这一行最后的N改成Y，保存退出。
2. 打开终端执行`vi /oracle/product/10g/bin/dbstart`，修改`ORACLE_HOME_LISTNER=/ade/vikrkuma_new/oracle`为`ORACLE_HOME_LISTNER=/oracle/product/10g/`
3. 打开终端执行`vi /etc/rc.local`，追加以下内容让oracle随系统启动（注意-和oracle之间存在空格）：
```
su – oracle -c "/oracle/product/10g/bin/dbstart"
```

## 八、Oracle10g的每天定时自动备份

1. 判断计划任务是否启动，root身份下执行：
```
/sbin/service crond status
```

查看启动状态，如果没有启动，
```
vi /etc/rc.d/rc.local
```

将`/sbin/service crond start`语句加入rc.loca文件，使开机自启。

2. 创建备份存放目录，oracle身份下执行：
```
mkdir -p /oracle/backup
```

3. 创建自动备份脚本，以oracle用户在主目录`vi backup.sh`，加入以下内容，语句功能是备份并删除一个月前的过期备份：
```
export LANG="en_SU.UTF-8"
export NLS_LANG="Simplified Chinese_china".ZHS16GBK
export ORACLE_BASE=/oracle/
export ORACLE_HOME=/oracle/product/10g
export ORACLE_SID=kms
export PATH=$PATH:$HOME/bin:$ORACLE_HOME/bin
backuptime=`date +"%Y%m%d%H"`
deletetime=`date -d "1 month ago" +"%Y%m%d%H"`
rm -f /oracle/backup/mvs_$deletetime.dmp
exp mvs/mvs file=/oracle/backup/mvs_$backuptime.dmp full=y
```

保存后`chmod +x backup.sh`将执行权限赋予自己。然后`./backup.sh`执行下试验效果。

4. Oracle身份下，调用计划任务来执行backup.sh脚本，完成自动备份，执行：
```
crontab –e
```

调出计划任务编辑界面后，录入以下内容，完成每天3点自动备份的功能：
```
00 03 * * * /home/oracle/backup.sh
```

## 九、关于oracle10g的卸载

1. 做好备份工作
2. 用oracle帐户登录
3. 在终端里打入`lsnrctl stop`停止监听
4. 在终端里打入`sqlplus "/ as sysdba"`进入sqlplus
5. SQL状态里`shutdown immediate`停止数据库
6. 用root帐户登录
7. 删除oracle安装目录及`/etc/ora`

