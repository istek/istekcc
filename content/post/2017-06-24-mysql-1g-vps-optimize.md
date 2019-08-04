---
title: MYSQL在1G VPS上的性能优化配置
date: 2017-06-24T03:56:30+00:00
layout: post
categories:
  - Linux
tags:
  - mysql
---

今天不小心把数据给删除了，好在有备份，唉，烦死了。然后，各种装，装好后，在优化数据库这块，看到螃蟹氪的这篇优化笔记[^1]，顺手拿来用了，目前感觉好使。

```
[mysql]
# CLIENT #
port                           = 3306
socket                         = /var/lib/mysql/mysql.sock

[mysqld]
# GENERAL #
user                           = mysql
default-storage-engine         = InnoDB
socket                         = /var/lib/mysql/mysql.sock
pid-file                       = /var/lib/mysql/mysql.pid

# MyISAM #
key-buffer-size                = 32M
myisam-recover                 = FORCE,BACKUP

# SAFETY #
max-allowed-packet             = 16M
max-connect-errors             = 1000000
skip-name-resolve
innodb                         = FORCE

# DATA STORAGE #
datadir                        = /var/lib/mysql/

# CACHES AND LIMITS #
tmp-table-size                 = 32M
max-heap-table-size            = 32M
query-cache-type               = 0
query-cache-size               = 0
max-connections                = 500
thread-cache-size              = 50
open-files-limit               = 65535
table-definition-cache         = 1024
table-open-cache               = 2048

# INNODB #
innodb-flush-method            = O_DIRECT
innodb-log-files-in-group      = 2
innodb-log-file-size           = 64M
innodb-flush-log-at-trx-commit = 1
innodb-file-per-table          = 1
innodb-buffer-pool-size        = 512M

# LOGGING #
log-error                      = /var/lib/mysql/mysql-error.log
log-queries-not-using-indexes  = 1
slow-query-log                 = 1
slow-query-log-file            = /var/lib/mysql/mysql-slow.log
```

[^1]: http://pangxiekr.com/1g-vpsshang-mysql-pei-zhi-you-hua/