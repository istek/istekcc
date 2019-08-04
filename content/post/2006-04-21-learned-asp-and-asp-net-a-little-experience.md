---
title: 学习了ASP和ASP.NET的一点心得
date: 2006-04-21T08:38:13+00:00
layout: post
categories:
  - .Net
tags:
  - c#
---

今天从网络上主要看了一下ASP的教程,看的不是很深,仅仅介绍了ASP的几个对象和方法,还有简单的几个例子。不过从网络看的[10天学会ASP.net](http://bbs.wum.cn/dispbbs.asp?boardid=146&id=2355)非常不错,很适合速成。

也对新手容易出现的问题,文章中都一一列举出来了,尤其是用C#开发ASP.NET的时候,特别明显.因为很容易忘记大小写和分号,以及传递参数的时候使用圆括号。但是文章中谈到的验证控件,似乎没有,因为当我执行代码的时候报错,好像在System.Web.UI.WebControls里面没有validater这样的控件。下面贴出错误消息:
```
Parser Error

Description: An error occurred during the parsing of a resource required to service this request. Please review the following specific parse error details and modify your source file appropriately. Parser Error Message: Could not load type System.Web.UI.WebControls.RequireFieldValidator from assembly System.Web, Version=1.0.5000.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a.
```
看完这个10天学会ASP.NET后,我是信心大增啊,没有想到原来也可以这样简单.当然,学过之后的应用仍然是需要锻炼和总结的.

下面给出几个比较有用的连接:

<http://www.ddvip.com/>

<http://www.chinaasp.com/>

<http://www.ibook8.com/>

希望各位能找到自己需要的东西.