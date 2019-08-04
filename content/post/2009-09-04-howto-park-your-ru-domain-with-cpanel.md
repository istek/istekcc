---
title: Howto park your .ru domain with cpanel?
date: 2009-09-04T21:18:04+00:00
layout: post
categories:
  - WEB
tags:
  - 免费资源
---
It’s a tutorial that tell you how to park your .ru domain with cpanel.

I’m using everydns.net, but I think this way is the same to other free dns service.

First, login your RIPN account [here](http://www.ripn.net/nic/dns/form/en/), point name server of your .ru domain to everydns.net.

Second, login everydns.net, just delete your .ru domain from domain control panel, If it’s already there. Make sure there’s NOT any record. Of course, you can use [this web tool](http://www.kloth.net/services/nslookup.php) to check your nameserver or any records. Usually it will take effect in 24 hours, so be patient.
<!--more-->
If you confirm there’s no any records of your .ru domain, then login your cpanel with your account, click Parked Domain, try to park your domain, generally it’s successful. Okay, right now your domain is parked. But there’s one step to do, yes, add a A record in everydns.

Login your everydns.net, we must add the domain name which we deleted before. and then add a A record, its value is shared IP in your cpanel.

okay,wait a while, you will see it works well.