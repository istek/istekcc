---
title: tomcat 6/7 优化
date: 2017-03-17T19:03:57+00:00
layout: post
categories:
  - Linux
tags:
  - tomcat
---

## tomcat的运行模式

运行模式有三种：bio（默认），nio，apr。

**bio**

bio(blocking I/O)，顾名思义，即阻塞式I/O操作，表示Tomcat使用的是传统的Java I/O操作(即java.io包及其子包)。Tomcat在默认情况下，就是以bio模式运行的。遗憾的是，就一般而言，bio模式是三种运行模式中性能最低的一种。我们可以通过Tomcat Manager来查看服务器的当前状态。

**nio**

Java SE 1.4及后续版本提供的一种新的I/O操作方式(即java.nio包及其子包)。Java nio是一个基于缓冲区、并能提供非阻塞I/O操作的Java API，因此nio也被看成是non-blocking I/O的缩写。它拥有比传统I/O操作(bio)更好的并发运行性能。

**apr**

Apache Portable Runtime/Apache可移植运行库，是Apache HTTP服务器的支持库。你可以简单地理解为，Tomcat将以JNI的形式调用Apache HTTP服务器的核心动态链接库来处理文件读取或网络传输操作，从而大大地提高Tomcat对静态文件的处理性能。 Tomcat apr也是在Tomcat上运行高并发应用的首选模式。
<!--more-->
## 各模式的启用方法

### 1、nio模式

直接修改`server.xml`文件即可，connector节点修改如下：
```
<Connector port="80″
protocol="org.apache.coyote.http11.Http11NioProtocol"
connectionTimeout="20000"
URIEncoding="UTF-8"
useBodyEncodingForURI="true"
enableLookups="false"
redirectPort="8443" />
```

### 2、apr模式

首先，到[官网](https://apr.apache.org/download.cgi)下载apr和apr-utils源码包。
```
wget http://ftp.mirror.tw/pub/apache/apr/apr-1.5.2.tar.gz
tar -zxvf apr-1.5.2.tar.gz
cd apr-1.5.2
./configure
make&&make install
```

_注意：configure时会报一个错误`rm: cannot remove 'libtoolT': No such file or directory`_

编辑configure文件， 把`$RM "$cfgfile"`这行注释掉，重新编译即可通过。
```
# wget http://ftp.mirror.tw/pub/apache/apr/apr-util-1.5.2.tar.gz
# tar -zxvf apr-util-1.5.4.tar.gz
# cd apr-util-1.5.4
# ./configure --with-apr=/usr/local/apr
# make # make install
```

编译完成后，编译tomcat/bin目录下的tomcat-native包
```
# cd $CATALINA_HOME/bin
# tar -zxvf tomcat-native.tar.gz
# cd tomcat-native-1.2.10-src/native
# ./configure --with-apr=/usr/local/apr --with-java-home=/usr/java/jdk1.6.0_45
```

在这可能会报错，假如你的openssl版本过低。

> 报错信息：
> Found OPENSSL_VERSION_NUMBER 0x10000003 (OpenSSL 1.0.0 29 Mar 2010)
> Require OPENSSL_VERSION_NUMBER 0x1000200f or greater (1.0.2)

**安装openssl 1.0.2k**

从官方网站下载openssl 1.0.2k
```
# tar xvf openssl-1.0.2k.tar.gz
# cd openssl-1.0.2k
# ./config --prefix=/usr/local/openssl -fPIC
# make && make install
```

再次编译native
```
# ./configure --with-apr=/usr/local/apr --with-java-home=/usr/java/jdk1.6.0_45 --with-ssl=/usr/local/openssl
# make && make install
```

安装完成后，原生库在`/usr/local/apr/lib`目录.

**环境变量**

这里需要重新修改当前用户的环境变量
```
JAVA_HOME=/usr/local/java
JAVA_BIN=$JAVA_HOME/bin
PATH=$PATH:$JAVA_BIN
CLASSPATH=$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JAVA_HOME JAVA_BIN PATH CLASSPATH
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/apr/lib
```

**在tomcat中集成apr**

在`catalina.sh`文件增加如下内容
```
CATALINA_OPTS="-Djava.library.path=/usr/local/apr/lib"
```

然后编辑`server.xml`
```
<Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="off" />
...
<Connector port="8080"
protocol="org.apache.coyote.http11.Http11AprProtocol"
connectionTimeout="20000" //链接超时时长
redirectPort="8443"
maxThreads="500"
minSpareThreads="20"
acceptCount="1000"
enableLookups="false"
URIEncoding="UTF-8" />
```

最后增加jvm内存参数，新增文件`$TOMCAT/bin/setenv.sh`，内容如下：
```
JAVA_OPTS="-Xms1024m -Xmx1024m -Xss1024K -XX:PermSize=64m -XX:MaxPermSize=128m"
```

_参数详解_
* -Xms JVM初始化堆内存大小
* -Xmx JVM堆的最大内存
* -Xss 线程栈大小
* -XX:PermSize JVM非堆区初始内存分配大小
* -XX:MaxPermSize JVM非堆区最大内存
* -Xms和-Xmx选项设置为相同堆内存分配，以避免在每次GC 后调整堆的大小，堆内存建议占内存的60%~80%;非堆内存是不可回收内存，大小视项目而定;线程栈大小推荐256k.

