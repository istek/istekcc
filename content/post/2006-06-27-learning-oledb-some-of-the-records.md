---
title: 学习OleDB的一些纪录
date: 2006-06-27T12:56:56+00:00
layout: post
categories:
  - DotNet
---
上周五把我自己写的一个简单的留言本给一个搞开发的朋友看,结果发现了2个严重的bug,一个是登录Bug,一个是Sql注入漏洞.

说实话,在进行写留言本的时候,我根本就不知道SQL注入漏洞是怎么样的，后来朋友给我讲了原理，做了示范，我发现要写出安全的代码真的需要注意很多问题。朋友给我的建议是：

第一条就是使用OleDBParameters,即参数，不要使用拼凑的SQL语句（动态SQL语句），而是使用参数，将变量给参数。

第二条就是控制用户输入，即任何时候不信任用户的输入，这个需要正则表达式来完成。

第三条，过滤HTML文本，即使用HttpUtility.HTMLEncode()或者Server.HTMLEcode()

下面的这些关于参数的代码，可能比较笨拙，但是很管用。因为oledb不像SQL Server那样，SQL SERVER使用命名参数，而OLEDB使用的是定位参数，即需要参数的地方使用‘?’。

下面是我写的一段代码：
```
        public static string filename = “~//App_Data//test.mdb”;
        public static string ConnString = “Provider=Microsoft.Jet.OLEDB.4.0;Data Source=” + HttpContext.Current.Server.MapPath(filename);//定义连接字符串

    OleDbConnection conn = new OleDbConnection(ConnString); //实例化一个oledbconnection对象
            OleDbDataAdapter oleda = new OleDbDataAdapter();//实例化一个DataAdapter对象
            DataSet ds = new DataSet();//实例化一个数据集
            string insertSql = ” insert into [test] (name,email,weburl,Content,ip) VALUES (?,?,?,?,?)”;//定义需要执行的sql语句，注意问号
            OleDbCommand olecmd = new OleDbCommand(insertSql, conn);//实例化一个oledbcommand对象，以sql语句和conn对象作为参数传递给构造函数

            olecmd.Parameters.Add(“@name”, OleDbType.Variant).Value = HttpUtility.HtmlEncode(nameTxt.Text);
            olecmd.Parameters.Add(“@email”, OleDbType.Variant).Value = HttpUtility.HtmlEncode(emailTxt.Text);
            olecmd.Parameters.Add(“@weburl”, OleDbType.Variant).Value = HttpUtility.HtmlEncode(weburlTxt.Text);
            olecmd.Parameters.Add(“@Content”, OleDbType.Variant).Value = HttpUtility.HtmlEncode(ContentTxt.Text);
            olecmd.Parameters.Add(“@ip”, OleDbType.Variant).Value = Session[“IP”].ToString();
    //上面5条开始添加SQL语句中的参数，注意顺序。
            oleda.InsertCommand = olecmd;//将olecmd对象赋值给InsertCommand属性
            conn.Open();//打开数据库
            oleda.InsertCommand.ExecuteNonQuery();//执行命令。
          conn.Close();//关闭数据库
```

**注意：**如果在编译的时候,得到错误“至少有一个参数没有被指定值”，请将@符号去掉。即`olecmd.Parameters.Add(“Content”, OleDbType.Variant).Value = HttpUtility.HtmlEncode(ContentTxt.Text);`。这个问题让我困扰了3个小时。

大家注意到了吧，参数的数据类型，我全部选择的是`OleDbType.Variant`，它映射到.net框架的object类型。我没有注意到什么ntext之类的数据类型。

以上代码主要是将参数用户DataAdapter，如果大家有更方便，更安全的方法，也请告诉我。谢谢。

关于其他的详细信息，比如SQL的，oracle的，大家可以看看MSDN。

ms-help://MS.VSCC.v80/MS.MSDN.v80/MS.VisualStudio.v80.chs/WD_ADONET/html/f21e6aba-b76d-46ad-a83e-2ad8e0af1e12.htm

我写的留言本程序的地址是 <http://et.bestzhou.org/>