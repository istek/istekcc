---
title: weblog数据库设计笔记
date: 2006-05-16T14:49:55+00:00
layout: post
categories:
  - 我的世界
---

今天开始写weblog了，其实早在上周就已经开始准备了，写下了自己对于我所理解的blog程序的计划，然后设计数据库，目前我仅仅是为了实现一些简单的功能，所以数据库的设计上比较随意。

一共4张表，Catalog表主要是为了存储日志分类，2个字段，CatalogID,CatalogTitle,数据类型，INT，VARCHAR,CatalogID为主键。

User表存储日志的所有者，内包括一些字段，比如UserID,Username,Nickname,Password,SecureQuestion,SecureAnswer，其中最后两个字段是为了取回密码用的。Password使用md5加密，这在VS里面，using System.Security.Cryptography ,new一个md5的实例，然后调用Create()方法创建该实例的md5字符串，UserID为主键。

Article表，主要是存储日志项目，有ArticleID,ArticleTitle,ArticleSummary,ArticleContent,ArticleDate,UserID,CatalogID,主键ArticleID。

Comment表存储日志评论项目，字段有CommentID,CommentTitle,CommentText,Author,CommentDate,ArticleID,CommentID为主键。我想，我这样一写，表之间的关系很明显了。