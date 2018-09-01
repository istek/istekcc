---
title: CentOS 6.7安装MariaDB 5.5
date: 2016-02-02T06:27:15+00:00
layout: post
categories:
  - 我的世界
tags:
  - CentOS
  - 数据库
---

## 一、配置Yum源

需要在`/etc/yum.repos.d`目录创建`mariadb.repo`文件。

```
# MariaDB 5.5 CentOS repository list - created 2014-03-04 11:20 UTC
# http://mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB  
baseurl = http://yum.mariadb.org/5.5/centos6-amd64  
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB  
gpgcheck=1  
```
<!--more-->
_注意_：baseurl 中的地址是X64（64位）系统使用的，如果是i686（32位），则应将baseurl中的地址改为`http://yum.mariadb.org/5.5/centos6-x86/`。

## 二、更新软件源

更新新添加的yum源，使得可以安装mariadb。
```
[root@xia mysql]# yum update
Loaded plugins: fastestmirror  
Setting up Update Process  
Loading mirror speeds from cached hostfile  
 * atomic: www6.atomicorp.com
 * base: mirror.chpc.utah.edu
 * epel: linux.mirrors.es.net
 * extras: reflector.westga.edu
 * updates: repos.forethought.net
mariadb                                           | 2.9 kB     00:00  
mariadb/primary_db                                |  19 kB     00:00  
No Packages marked for Update  
```

## 三、安装mariadb数据库

```
    [root@xia mysql]# yum install MariaDB-server MariaDB-client
    Loaded plugins: fastestmirror  
    Setting up Install Process  
    Loading mirror speeds from cached hostfile  
     * atomic: www6.atomicorp.com
     * base: mirror.chpc.utah.edu
     * epel: linux.mirrors.es.net
     * extras: reflector.westga.edu
     * updates: repos.forethought.net
    Resolving Dependencies  
    --> Running transaction check
    ---> Package MariaDB-client.i686 0:5.5.47-1.el6 will be installed
    --> Processing Dependency: MariaDB-common for package: MariaDB-client-5.5.47-1.el6.i686
    ---> Package MariaDB-server.i686 0:5.5.47-1.el6 will be installed
    --> Processing Dependency: libaio.so.1(LIBAIO_0.4) for package: MariaDB-server-5.5.47-1.el6.i686
    --> Processing Dependency: libaio.so.1(LIBAIO_0.1) for package: MariaDB-server-5.5.47-1.el6.i686
    --> Processing Dependency: libaio.so.1 for package: MariaDB-server-5.5.47-1.el6.i686
    --> Processing Dependency: perl(DBI) for package: MariaDB-server-5.5.47-1.el6.i686
    --> Running transaction check
    ---> Package MariaDB-common.i686 0:5.5.47-1.el6 will be installed
    --> Processing Dependency: MariaDB-compat for package: MariaDB-common-5.5.47-1.el6.i686
    ---> Package libaio.i686 0:0.3.107-10.el6 will be installed
    ---> Package perl-DBI.i686 0:1.609-4.el6 will be installed
    --> Running transaction check
    ---> Package MariaDB-compat.i686 0:5.5.47-1.el6 will be installed
    --> Finished Dependency Resolution
    
    Dependencies Resolved
    
    ================================================================================
     Package               Arch        Version                 Repository      Size
    ================================================================================
    Installing:  
     MariaDB-client        i686        5.5.47-1.el6            mariadb        9.9 M
     MariaDB-server        i686        5.5.47-1.el6            mariadb         33 M
    Installing for dependencies:  
     MariaDB-common        i686        5.5.47-1.el6            mariadb         23 k
     MariaDB-compat        i686        5.5.47-1.el6            mariadb        2.6 M
     libaio                i686        0.3.107-10.el6          base            21 k
     perl-DBI              i686        1.609-4.el6             base           705 k
    
    Transaction Summary  
    ================================================================================
    Install       6 Package(s)
    
    Total download size: 46 M  
    Installed size: 178 M  
    Is this ok [y/N]: y  
    Downloading Packages:  
    (1/6): MariaDB-5.5.47-centos6-i686-client.rpm            | 9.9 MB     00:01
    (2/6): MariaDB-5.5.47-centos6-i686-common.rpm            |  23 kB     00:00
    (3/6): MariaDB-5.5.47-centos6-i686-compat.rpm            | 2.6 MB     00:00
    (4/6): MariaDB-5.5.47-centos6-i686-server.rpm            |  33 MB     00:01
    (5/6): libaio-0.3.107-10.el6.i686.rpm                    |  21 kB     00:00
    (6/6): perl-DBI-1.609-4.el6.i686.rpm                     | 705 kB     00:00
    --------------------------------------------------------------------------------
    Total                                            12 MB/s |  46 MB     00:03  
    warning: rpmts_HdrFromFdno: Header V4 DSA/SHA1 Signature, key ID 1bb943db: NOKEY  
    Retrieving key from https://yum.mariadb.org/RPM-GPG-KEY-MariaDB  
    Importing GPG key 0x1BB943DB:  
     From  : https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
    Is this ok [y/N]: y  
    Running rpm_check_debug  
    Running Transaction Test  
    Transaction Test Succeeded  
    Running Transaction  
      Installing : MariaDB-common-5.5.47-1.el6.i686                             1/6
      Installing : MariaDB-compat-5.5.47-1.el6.i686                             2/6
      Installing : MariaDB-client-5.5.47-1.el6.i686                             3/6
      Installing : libaio-0.3.107-10.el6.i686                                   4/6
      Installing : perl-DBI-1.609-4.el6.i686                                    5/6
      Installing : MariaDB-server-5.5.47-1.el6.i686                             6/6
    chown: cannot access `/var/lib/mysql': No such file or directory  
    160131 22:00:59 [Note] /usr/sbin/mysqld (mysqld 5.5.47-MariaDB) starting as process 14907 ...  
    160131 22:00:59 [Note] /usr/sbin/mysqld (mysqld 5.5.47-MariaDB) starting as process 14915 ...
    
    PLEASE REMEMBER TO SET A PASSWORD FOR THE MariaDB root USER !  
    To do so, start the server, then issue the following commands:
    
    '/usr/bin/mysqladmin' -u root password 'new-password'  
    '/usr/bin/mysqladmin' -u root -h xia.bestzhou.us password 'new-password'
    
    Alternatively you can run:  
    '/usr/bin/mysql_secure_installation'
    
    which will also give you the option of removing the test  
    databases and anonymous user created by default.  This is  
    strongly recommended for production servers.
    
    See the MariaDB Knowledgebase at http://mariadb.com/kb or the  
    MySQL manual for more instructions.
    
    Please report any problems at http://mariadb.org/jira
    
    The latest information about MariaDB is available at http://mariadb.org/.  
    You can find additional information about the MySQL part at:  
    http://dev.mysql.com  
    Support MariaDB development by buying support/new features from MariaDB  
    Corporation Ab. You can contact us about this at sales@mariadb.com.  
    Alternatively consider joining our community based development effort:  
    http://mariadb.com/kb/en/contributing-to-the-mariadb-project/
    
      Verifying  : MariaDB-compat-5.5.47-1.el6.i686                             1/6
      Verifying  : MariaDB-server-5.5.47-1.el6.i686                             2/6
      Verifying  : MariaDB-common-5.5.47-1.el6.i686                             3/6
      Verifying  : perl-DBI-1.609-4.el6.i686                                    4/6
      Verifying  : MariaDB-client-5.5.47-1.el6.i686                             5/6
      Verifying  : libaio-0.3.107-10.el6.i686                                   6/6
    
    Installed:  
      MariaDB-client.i686 0:5.5.47-1.el6     MariaDB-server.i686 0:5.5.47-1.el6
    
    Dependency Installed:  
      MariaDB-common.i686 0:5.5.47-1.el6     MariaDB-compat.i686 0:5.5.47-1.el6
      libaio.i686 0:0.3.107-10.el6           perl-DBI.i686 0:1.609-4.el6
    
    Complete!  
```

## 四、启动数据库

### 1. 启动数据库

[root@xia mysql]# service mysql start
Starting MySQL. SUCCESS!  


### 2. 修改数据库root密码
```
[root@xia yum.repos.d]# mysql_secure_installation
/usr/bin/mysql_secure_installation: line 379: find_mysql_client: command not found

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MariaDB  
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MariaDB to secure it, we'll need the current  
password for the root user.  If you've just installed MariaDB, and  
you haven't set the root password yet, the password will be blank,  
so you should just press enter here.

Enter current password for root (enter for none):  
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB  
root user without the proper authorisation.

Set root password? [Y/n] Y  
New password:  
Re-enter new password:  
Password updated successfully!  
Reloading privilege tables..  
 ... Success!


By default, a MariaDB installation has an anonymous user, allowing anyone  
to log into MariaDB without having to have a user account created for  
them.  This is intended only for testing, and to make the installation  
go a bit smoother.  You should remove them before moving into a  
production environment.

Remove anonymous users? [Y/n] Y  
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This  
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] Y  
 ... Success!

By default, MariaDB comes with a database named 'test' that anyone can  
access.  This is also intended only for testing, and should be removed  
before moving into a production environment.

Remove test database and access to it? [Y/n] Y  
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far  
will take effect immediately.

Reload privilege tables now? [Y/n] Y  
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB  
installation should now be secure.

Thanks for using MariaDB!  
```

## 五、优化mysql数据库

因为数据库的优化是根据数据库的运行状况，内存使用情况等各方面，综合性的调优数据库，在这里，我们只能稍作小小优化，提高运行效率，降低内存占用。

配置文件: `/etc/my.cnf.d/server.cnf`
```
# These groups are read by MariaDB server.
# Use it for options that only the server (but not clients) should see
#
# See the examples of server my.cnf files in /usr/share/mysql/
#

# this is read by the standalone daemon and embedded servers
[server]

# this is only for the mysqld standalone daemon
[mysqld]
general_log_file        = /var/log/mysql/mysql.log  
general_log             = 1  
log_warnings            = 2  
log_output              = table,file  
log-error               = /var/log/mysql/mysql.log

character-set-server=utf8  
init_connect='SET NAMES utf8'  
slow_query_log=1  
slow_query_log_file=/var/log/mysql/mysql-slow.log  
long_query_time=1  
skip-innodb  
default-storage-engine=MYISAM

wait_timeout=60  
connect_timeout=10  
interactive_timeout=120  
expire_logs_days = 10

key_buffer_size = 8M  
max_allowed_packet = 1M  
table_open_cache = 128  
sort_buffer_size = 512K  
net_buffer_length = 8K  
read_buffer_size = 256K  
read_rnd_buffer_size = 512K  
myisam_sort_buffer_size = 4M  
query_cache_size = 8M  
thread_cache_size = 4

lower_case_table_names=1

#
# * Galera-related settings
#
[galera]
# Mandatory settings
#wsrep_provider=
#wsrep_cluster_address=
#binlog_format=row
#default_storage_engine=InnoDB
#innodb_autoinc_lock_mode=2
#bind-address=0.0.0.0
#
# Optional setting
#wsrep_slave_threads=1
#innodb_flush_log_at_trx_commit=0

# this is only for embedded server
[embedded]

# This group is only read by MariaDB-5.5 servers.
# If you use the same .cnf file for MariaDB of different versions,
# use this group for options that older servers don't understand
[mysqld-5.5]

# These two groups are only read by MariaDB servers, not by MySQL.
# If you use the same .cnf file for MySQL and MariaDB,
# you can put MariaDB-only options here
[mariadb]

[mariadb-5.5]
```

改完后，记得重启mysql服务。在我这边，我选择一个客户服务器上内存的使用大概是220M左右，WEB应用是企业CMS，运行状况良好。