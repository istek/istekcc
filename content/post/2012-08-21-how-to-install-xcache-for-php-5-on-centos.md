---
title: How to Install XCache for PHP 5 on CentOS
date: 2012-08-21T09:16:39+00:00
layout: post
categories:
  - 我的世界
tags:
  - CentOS
  - xcache
---

XCache is a open-source opcode cacher, which means that it accelerates the performance of PHP on servers. It optimizes performance by removing the compilation time of PHP scripts by caching the compiled state of PHP scripts into the shm (RAM) and uses the compiled version straight from the RAM. This will increase the rate of page generation time by up to 5 times as it also optimizes many other aspects of php scripts and reduce server load.

Download xcahce:
```
cd /opt
wget tar -zxvf xcache-2.0.1.tar.gz
cd xcache-2.0.1
```

Use `yum` to install the PHP compile tools, if you can have installed before, skip this step
```
yum -y install php-devel yum install -y gcc make
```
<!--more-->
Use`phpize`command to prepare xcache as a PHP extension for compiling, if you dont know where is phpize, use `find -name phpize`:
```
phpize
```

Configure, compile and install xcache:
```
./configure --enable-xcache
make make install
```

If successfully install Xcache, the default xache.so installation location is, the location will be displayed in the last step:
64 bit PHP module installed at `/usr/lib64/php/modules/xcache.so`
32 bit PHP module installed at `/usr/lib/php/modules/xcache.so`

```
$ echo -n "password"| md5sum 
5f4dcc3b5aa765d61d8327deb882cf99 password
```

open `php.ini`, and add the following to php.ini.
```
[xcache-common]
zend_extension      = /usr/lib/php/modules/xcache.so [xcache.admin]
xcache.admin.user   = "admin"
;how to create md5 pwd: echo -n "password"| md5sum xcache.admin.pass   = "5f4dcc3b5aa765d61d8327deb882cf99" [xcache]
; Change xcache.size to tune the size of the opcode cache
; to disable: xcache.size=0
; to enable : xcache.size=64M etc (any size > 0) and your system mmap allows
; someone said, if >=64 will get problem
xcache.size         = 32M
xcache.shm_scheme   = "mmap"
; set to cpu count (cat /proc/cpuinfo |grep -c processor) xcache.count        = 4
; just a hash hints, you can always store count(items) > slots xcache.slots        = 8K
; ttl of the cache item, 0=forever
xcache.ttl          = 0
; interval of gc scanning expired items, 0=no scan, other values is in seconds xcache.gc_interval  = 0
; Change xcache.var_size to adjust the size of variable cache
; same as aboves but for variable cache, if you have not used xcache function in script,forget it
xcache.var_size     = 8M
xcache.var_count    = 1
xcache.var_slots    = 8K
xcache.var_ttl      = 0
xcache.var_maxttl   = 0
xcache.var_gc_interval =     300
xcache.test         = Off
xcache.readonly_protection = On
; for *nix, xcache.mmap_path is a file path, not directory.
; Use something like "/tmp/xcache" if you want to turn on ReadonlyProtection
; 2 group of php won’t share the same /tmp/xcache
; for win32, xcache.mmap_path=anonymous map name, not file path
xcache.mmap_path    = "/tmp/xcache"
; leave it blank(disabled) or "/tmp/phpcore/"
; make sure it’s writable by php (without checking open_basedir)
xcache.coredump_directory =   ""
; per request settings
xcache.cacher       = On
xcache.stat         = On
xcache.optimizer    = Off
[xcache.coverager]
; per request settings
; enable coverage data collecting for xcache.coveragedump_directory and
; xcache_coverager_start/stop/get/clean() functions (will hurt executing performance)
xcache.coverager    = On
; ini only settings
; make sure it’s readable (care open_basedir) by coverage viewer script
; requires xcache.coverager=On
xcache.coveragedump_directory = ""
```

type the following command for verification:
```
php -v
```

you will see output like this:
```
PHP 5.3.3 (cli) (built: Jul  3 2012 16:40:30) Copyright (c) 1997-2010 The PHP Group Zend Engine v2.3.0, Copyright (c) 1998-2010 Zend Technologies
	with the ionCube PHP Loader v3.3.11, Copyright (c) 2002-2010, by ionCube Ltd., and
	with XCache v2.0.1, Copyright (c) 2005-2012, by mOo
```