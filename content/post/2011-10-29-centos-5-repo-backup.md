---
title: CentOS 5 可用源记录
date: 2011-10-29T09:16:44+00:00
layout: post
categories:
  - Linux
tags:
  - yum
---

用了一段时间CentOS5，主要用来做iptables+tc的网关，还有几台在做web服务器，也收集了一些非常有用的源，作为备份留存。

**nginx CentOS nginx官方源**
```
[nginx]
name=nginx repo baseurl=http://nginx.org/packages/centos/5/$basearch/
gpgcheck=0
enabled=1
```

<!--more-->

**Jason’s Utter Ramblings Repo**
```
[utterramblings]
name=Jason's Utter Ramblings Repo baseurl=http://www.jasonlitka.com/media/EL$releasever/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://www.jasonlitka.com/media/RPM-GPG-KEY-jlitka
```

**RPMForge源**
```
### Name: RPMforge RPM Repository for RHEL 5 - dag
### URL: http://rpmforge.net/
[rpmforge]
name = RHEL $releasever - RPMforge.net - dag
baseurl = http://apt.sw.be/redhat/el5/en/$basearch/rpmforge
mirrorlist = http://apt.sw.be/redhat/el5/en/mirrors-rpmforge
#mirrorlist = file:///etc/yum.repos.d/mirrors-rpmforge
enabled = 1
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1

[rpmforge-extras]
name = RHEL $releasever - RPMforge.net - extras
baseurl = http://apt.sw.be/redhat/el5/en/$basearch/extras
mirrorlist = http://apt.sw.be/redhat/el5/en/mirrors-rpmforge-extras
#mirrorlist = file:///etc/yum.repos.d/mirrors-rpmforge-extras
enabled = 0
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1

[rpmforge-testing]
name = RHEL $releasever - RPMforge.net - testing
baseurl = http://apt.sw.be/redhat/el5/en/$basearch/testing
mirrorlist = http://apt.sw.be/redhat/el5/en/mirrors-rpmforge-testing
#mirrorlist = file:///etc/yum.repos.d/mirrors-rpmforge-testing
enabled = 0
protect = 0
gpgkey = file:///etc/pki/rpm-gpg/RPM-GPG-KEY-rpmforge-dag
gpgcheck = 1
```

**EPEL Repo**

i386
```
rpm -Uvh http://download.fedora.redhat.com/pub/epel/5/i386/epel-release-5-4.noarch.rpm
```

x86_64
```
rpm -ihv http://download.fedora.redhat.com/pub/epel/5/x86_64/epel-release-5-4.noarch.rpm
```

**RPMFusion Repo**

```
rpm -Uvh http://mirrors.163.com/rpmfusion/free/el/updates/testing/5/i386/rpmfusion-free-release-5-0.1.noarch.rpm
```
