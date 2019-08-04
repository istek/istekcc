---
title: ASP.NET与SQL数据库连接方面的笔记
date: 2006-05-13T03:58:26+00:00
layout: post
categories:
  - .Net
tags:
  - c#
---
ASP.NET连接数据库可以使用VS提供的空间DATASOURCE,包括Sqldatasource，objectdatasource，xmldatasource，AccessDatasource。

连接数据库一般是三步，先建立连接，执行SQL命令，关闭连接。

当然，可以先把连接数据库字符串写入`Web.Config`文件，然后调用。

方法如下：
```
       SqlConnection aconn = new SqlConnection(ConfigurationManager.AppSettings.Get(“Conn”));
            aconn.Open();
            string sql1=”Insert into table(colum1,colum2,colum3) values ('Value1','Value2','value3')”;
            SqlCommand command = new SqlCommand(sql1,aconn);
            command.ExecuteNonQuery(); //执行查询，返回成功或者失败。
            aconn.Close();
```

而Web.Config文件的Conn字符串则如下：
```
    <appSettings>
      <add key=”Conn” value=”Data Source=.SQLEXPRESS;AttachDbFilename=|DataDirectory| able.mdf;Integrated Security=True;User Instance=True” />
     </appSettings>
```

这样就是一个基本的连接数据库的例子。