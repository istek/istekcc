---
title: 关于使用HASH函数加密
date: 2006-10-17T07:56:44+00:00
layout: post
categories:
  - .Net
---

今天在写guestbook的代码的时候，想给密码加密，以前我发过一篇日志，是使用自己的MD5的方法加密的，虽然MD5加密不可逆，但是仍然感觉那个加密方法需要改进一下。

从baidu上搜索了一下HASH后得到MD5加密字符串的范例，特与各位ASP.NET朋友共享一下。
```
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.IO;
    using System.Data;

    public string CoumputeHash1(string szToHashString)
    {

    //将 szToHashString转换为字节数组
    byte[] byteToHash = Encoding.Default.GetBytes(szToHashString);
    //创建的 MD5 类的实例
    MD5CryptoServiceProvider csp=new MD5CryptoServiceProvider();
    byte[] byteHashValue = csp.ComputeHash(byteToHash);//计算哈希值
    //将byte数组转化为string
    string szRetHashString=BitConverter.ToString(byteHashValue);
    return szRetHashString;
    }

    public string CoumputeHash2(string szToHashString)
    {
    //将 szToHashString转换为字节数组
    Byte[] byteToHash = Encoding.Default.GetBytes(szToHashString);

    //使用由加密配置系统返回的 MD5 实例创建哈希值
    byte[] byteHashValue = ((HashAlgorithm) CryptoConfig.CreateFromName("MD5")).ComputeHash(byteToHash);
    //将byte数组转化为string
    string szRetHashString=BitConverter.ToString(byteHashValue);
    return szRetHashString;
    }
```