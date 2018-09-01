---
title: 利用正则表达式自动给Email地址和Url加上连接
date: 2006-06-26T04:43:50+00:00
layout: post
categories:
  - 我的世界
tags:
  - 'c#'
---

```
    /// <summary>

    /// 自动给邮件地址或email地址加上url

    using System;

    using System.Text.RegularExpressions;

    namespace CommLayer

    {

    /// <summary>

    /// 自动给邮件地址或email地址加上url

    /// </summary>

    public class HyperlinkUrl

    {

    private static Regex urlregex = new Regex(@"(http://([w.]+/?)S*)",

    RegexOptions.IgnoreCase|RegexOptions.Compiled);

    private static Regex emailregex = new Regex(@"([a-zA-Z_0-9.-]+@[a-zA-Z_0-9.-]+.w+)",

    RegexOptions.IgnoreCase|RegexOptions.Compiled);

    public HyperlinkUrl()

    {

    }

    /// <summary>

    /// 生成带连接的字符串

    /// </summary>

    /// <param name="link">需要生成带连接地址的字符串</param>

    /// <returns>经过转换的字符串</returns>

    public static string GenHyperlinkUrl(string link)

    {

    link = emailregex.Replace(link, "<a href=mailto:$1>$1</a>");

    link = urlregex.Replace(link, "<a href="$1" target="_blank">$1</a>");

    return link;

    }

    }

    }
```