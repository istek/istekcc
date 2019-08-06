---
title: How to flush dns cache in Ubuntu?
date: 2009-08-13T09:30:38+00:00
layout: post
categories:
  - Linux
tags:
  - nscd
---
Some distributions run a caching nameserver in the background out of the box while some do not. To clear the cache if you have such a daemon running, simply restart the nscd service in ubuntu.

_\*Why would you clear your cache? \*_

Most DNS clients will cache the results of name resolution request to speed up multiple lookups to the same URL. Just think about how many requests are made to the same domain when visiting a single web page. Every file, image, style sheet, etc. that is on that page and served from the same domain requires a DNS lookup.

So if you have an invalid DNS entry cached on your local client you’ll need to flush it out of the cache so your client can do a new lookup and get the correct information. Or your other option is to wait until that DNS entry expires and the cache flushes it automatically… which typically takes about 24 hours.
<!--more-->
In ubuntu if you want to flush DNS cache you need to restart nscd daemon

Install nscd using the following command

Flush DNS Cache in Ubuntu Using the following command

By Stanley:

it’s a very useful tool.it doesn’t like Windows, ’cause Windows have a tool named ifconfig, and it has a parameter named flushdns, and it’s functional to clear your dns. But under ubuntu system, i didn’t find out any tool like that. okay, right now mark it, maybe you will use it next time.
