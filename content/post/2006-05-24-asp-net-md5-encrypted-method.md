---
title: ASP.NET中MD5加密码的方法
date: 2006-05-24T02:16:29+00:00
layout: post
categories:
  - .Net
---
```
    <%@ Page Language="C#" ContentType="text/html"%>
    <%@ Import Namespace="System"%>
    <script language="C#" runat="server">
    void Page_Load(Object sender,EventArgs e){
    //获取要加密的字段，并转化为Byte[]数组
    }
    <script language="C#" runat="server">
    void Page_Load(Object sender,EventArgs e){
    //获取要加密的字段，并转化为Byte[]数组
    byte[] data=System.Text.Encoding.Unicode.GetBytes(source.Text.ToCharArray());
    //建立加密服务
    System.Security.Cryptography.MD5 md5 = new System.Security.Cryptography.MD5CryptoServiceProvider();
    //加密Byte[]数组
    byte[] result= md5.ComputeHash(data);
    //将加密后的数组转化为字段
    string sResult=System.Text.Encoding.Unicode.GetString(result);
    //显示出来
    sha1_1.Text="MD5普通加密："+sResult.ToString()+"<br />";
    //作为密码方式加密
    string EnPswdStr=System.Web.Security.FormsAuthentication.HashPasswordForStoringInConfigFile(source.Text.ToString(),"MD5");
    //显示出来
    sha1_2.Text="MD5密码加密："+EnPswdStr+"<br />";
    }
    </script>
```