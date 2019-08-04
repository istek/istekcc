---
title: Install Sun/Oracle Java JDK/JRE 6u29 on Fedora 16/15, CentOS/RHEL 6/5.7(中英对照)
date: 2011-11-22T02:00:42+00:00
layout: post
categories:
  - Linux
tags:
  - jdk
---
**Please note: This guide still working normally if you want install Sun/Oracle Java 6, but if you want latest Java 7 version, then check [Howto Install Sun/Oracle Java JDK/JRE 7 on Fedora 16/15, CentOS 6/5.7 and Red Hat (RHEL) 6.1/6/5.7](http://www.if-not-true-then-false.com/2010/install-sun-oracle-java-jdk-jre-7-on-fedora-centos-red-hat-rhel/ "Install Sun/Oracle Java JDK/JRE 7 on Fedora 16/15, CentOS/Red Hat (RHEL) 6.1/6/5.7").** By default, Fedora 16/15/14/13/12 and CentOS/Red Hat (RHEL) 6/5.7 Linux operating systems use the OpenJDK Java, which is a good choice for normal use and it works with almost all the Java programs normally. OpenJDK is also easy to install and maintain with YUM package management, but some cases, Sun/Oracle Java installation is necessary, for example, if some program have to compile with Sun/Oracle Java or a particular program does not work without Sun/Oracle Java.

默认情况下，Fedora 16/15/14/13/12和CentOS/RHEL 6/5.7Linux操作系统使用OpenJDK Java，对于普通应用情况，这个就可以，它能执行绝大多数java程序。通过YUM包管理机制，它可以方便的安装和维护，但是在一些情况下，Sun/Oracle Java却是必需的，例如，一些程序是使用Sun/Oracle Java编译的，或者，某个特别的程序依赖Sun/Oracle Java.
<!--more-->
This is guide, **howto install Oracle Java JDK/JRE 6u29 on Fedora 16/15/14/13/12, CentOS/RHEL 6/5.7**.

## Install Sun/Oracle Java JDK /JRE 6u29 on Fedora 16/15/14/13/12 CentOS 6/5.7, Red Hat (RHEL) 6.1/6/5.7

1.Download Sun/Oracle Java JDK or JRE RPMs

1.下载Sun/Oracle Java JDK 或者 JRE rpm包

Download Sun/Oracle Java JDK or JRE from [here](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (current version is JDK 6 Update 29) .

从[这个网址](http://www.oracle.com/technetwork/java/javase/downloads/index.html)下载Sun/Oracle Java JDK或JRE(当前版本是JDK 6 Update 29).

**Note: Select rpm.bin package (example jdk-6u29-linux-i586-rpm.bin, jre-6u29-linux-i586-rpm.bin, jdk-6u29-linux-x64-rpm.bin or jre-6u29-linux-x64-rpm.bin).**

注意：选择rpm.bin后缀的软件包(例如jdk-6u29-linux-i586-rpm.bin, jre-6u29-linux-i586-rpm.bin, jdk-6u29-linux-x64-rpm.bin(在64位系统下使用的JDK) 或 jre-6u29-linux-x64-rpm.bin(在64位系统下使用的JRE) )

### 2. Change to root user.

2.以root用户身份登录
```
sudo -i
## OR ##
su -
```
### 3a. Run Sun/Oracle Java JDK binary

3a.运行Sun/Oracle Java JDK安装文件
```
chmod +x /path/to/file/jdk-6u29-linux-_-rpm.bin /path/to/binary/jdk-6u29-linux-_-rpm.bin
## OR ##
sh /path/to/binary/jdk-6u29-linux-*-rpm.bin
```
**Note: Use full file name (without asterix) if you have both i586 and x64 versions downloaded.**

### 3b. Run Sun/Oracle Java JRE binary

3b.运行Sun/Oracle Java JRE 安装文件
```
chmod +x /path/to/file/jre-6u29-linux-_-rpm.bin /path/to/binary/jre-6u29-linux-_-rpm.bin
## OR ##
sh /path/to/binary/jre-6u29-linux-*-rpm.bin
```
**Note: Use full file name (without asterix) if you have both i586 and x64 versions downloaded.**

### 4a. Install Sun/Oracle JDK *java*, *javaws*, *libjavaplugin.so* (for Firefox/Mozilla) and *javac* with *alternatives –install* command

4a. 使用`alternatives –install`命令安装Sun Oracle JDK java,javaws,javac和libjavaplugin.so(浏览器插件)
```
## java ##
alternatives --install /usr/bin/java java /usr/java/jdk1.6.0_29/jre/bin/java 20000
## javaws (32-bit only) ##
alternatives --install /usr/bin/javaws javaws /usr/java/jdk1.6.0_29/jre/bin/javaws 20000
## Java Browser (Mozilla) Plugin 32-bit ##
alternatives --install /usr/lib/mozilla/plugins/libjavaplugin.so libjavaplugin.so /usr/java/jdk1.6.0_29/ jre/lib/i386/libnpjp2.so 20000
## Java Browser (Mozilla) Plugin 64-bit ##
alternatives --install /usr/lib64/mozilla/plugins/libjavaplugin.so libjavaplugin.so.x86_64 /usr/java/jdk1.6.0_29/ jre/lib/amd64/libnpjp2.so 20000
## Install javac only if you installed JDK (Java Development Kit) package ##
alternatives --install /usr/bin/javac javac /usr/java/jdk1.6.0_29/bin/javac 20000 alternatives --install /usr/bin/jar jar /usr/java/jdk1.6.0_29/bin/jar 20000
```

### 4b. Install Sun/Oracle JRE *java*, *javaws* and *libjavaplugin.so* (for Firefox/Mozilla) with *alternatives –install* command

4b.使用`alternatives –install`命令安装Sun Oracle JRE java,javaws,javac和libjavaplugin.so(浏览器插件)
```
## java ##
alternatives --install /usr/bin/java java /usr/java/jre1.6.0_29/bin/java 20000
## javaws (32-bit only) ##
alternatives --install /usr/bin/javaws javaws /usr/java/jre1.6.0_29/bin/javaws 20000
## Java Browser (Mozilla) Plugin 32-bit ##
alternatives --install /usr/lib/mozilla/plugins/libjavaplugin.so libjavaplugin.so /usr/java/jre1.6.0_29/ lib/i386/libnpjp2.so 20000
## Java Browser (Mozilla) Plugin 64-bit ##
alternatives --install /usr/lib64/mozilla/plugins/libjavaplugin.so libjavaplugin.so.x86_64 /usr/java/jre1.6.0_29/ lib/amd64/libnpjp2.so 20000
```

### 5. Check current java, javac, javaws and libjavaplugin.so versions

5.检查当前java,javac,javaws和libjavaplugin.so的版本
```
java -version
java version "1.6.0_29" Java(TM) SE Runtime Environment (build 1.6.0_29-b03)
Java HotSpot(TM) Server VM (build 20.1-b02, mixed mode)

javac -version
javac 1.6.0_29
javaws Java(TM) Web Start 1.6.0_29
[...]
```

**Note: Check libjavaplugin.so with restarting Mozilla Firefox and writing _about:plugins_ on address bar.**

### 6. Swap between OpenJDK and Sun/Oracle JDK versions

6.在OpenJDK和Sun/Oracle JDK之间选择使用的版本
```
alternatives --config java
# or javac or javaws or libjavaplugin.so
There are 4 programs which provide 'java'. Selection Command
-----------------------------------------------
1 /usr/lib/jvm/jre-1.6.0-openjdk/bin/java
2 /usr/lib/jvm/jre-1.5.0-gcj/bin/java *
3 /usr/java/jdk1.6.0_18/jre/bin/java +
4 /usr/java/jdk1.6.0_29/jre/bin/java
Enter to keep the current selection[+], or type selection number:
```

**Note: java with [+] is currently on use**

## Post-Installation Setup

安装之后的设置

**Add JAVA_HOME environment variable to `/etc/profile file` or `$HOME/.bash_profile` file**
在/etc/profile或者主目录下的.bash_profile中增加JAVA_HOME环境变量

```
## export JAVA_HOME JDK ##
export JAVA_HOME="/usr/java/jdk1.6.0_29"
## export JAVA_HOME JRE ##
export JAVA_HOME="/usr/java/jre1.6.0_29"
```