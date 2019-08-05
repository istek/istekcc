---
title: 数据库开发总结(ADO.NET小结)
date: 2006-06-28T14:36:58+00:00
layout: post
categories:
  - .Net
---
**一 用SqlConnection连接SQL Server**

**1.加入命名空间**
```
    using System.Data.SqlClient;
```

**2.连接数据库**
```
    SqlConnection myConnection = new SqlConnection();
    myConnection.ConnectionString = "user id=sa;password=sinofindb;initial catalog=test;data source=127.0.0.1;Connect Timeout=30";
    myConnection.Open();
```

**改进（更通用）的方法：**
```
    string MySqlConnection="user id=sa;password=sinofindb;Database =test;data source=127.0.0.1;Connect Timeout=30";
    SqlConnection myConnection = new SqlConnection(MySqlConnection);
    myConnection.Open();
```

**二 用OleDbConnection连接**

**1.加入命名空间**
```
    using System.Data.OleDb;
```

**2.连接sql server**
```
    string MySqlConnection="Provider=SQLOLEDB;Data Source=localhost;Initial Catalog=test;Integrated Security=SSPI;";

    SqlConnection myConnection = new SqlConnection(MySqlConnection);
    myConnection.Open();
```

**3.连接Access(可通过建立.udl文件获得字符串)**
```
    string MySqlConnection="Provider=Microsoft.Jet.OLEDB.4.0;Data Source=c:db2000.mdb;

    Persist Security Info=False;
```

**4.连接Oracle(也可通过OracleConnection连接)**
```
    string MySqlConnection="Provider=MSDAORA;Data Source=db; user id=sa;password=sinofindb";
```

**三.创建Command对象**


**1．SqlCommand 构造函数**

①初始化 SqlCommand 类的新实例。
```
    public SqlCommand();
    SqlCommand myCommand = new SqlCommand();
```

②初始化具有查询文本的 SqlCommand 类的新实例。
```
    public SqlCommand(string);
    String mySelectQuery = "Select * FROM mindata";
    SqlCommand myCommand = new SqlCommand(mySelectQuery);
```

③初始化具有查询文本和 SqlConnection 的SqlCommand类实例。
```
    Public SqlCommand(string, SqlConnection);
    String mySelectQuery = "Select * FROM mindata";
    string myConnectString = "user id=sa;password=;database=test;server=mySQLServer";
    SqlConnection myConnection = new SqlConnection(myConnectString);
    SqlCommand myCommand = new SqlCommand(mySelectQuery,myConnection);
```

④初始化具有查询文本、SqlConnection 和 Transaction 的 SqlCommand 类实例。
```
    public SqlCommand(string, SqlConnection, SqlTransaction);

    SqlTransaction myTrans = myConnection.BeginTransaction();
    String mySelectQuery = "Select * FROM mindata";
    string myConnectString = "user id=sa;password=;database=test;server=mySQLServer";
    SqlConnection myConnection = new SqlConnection(myConnectString);
    SqlCommand myCommand = new SqlCommand(mySelectQuery,myConnection, myTrans);
```

**2.建立SqlCommand与SqlConnection的关联。**
```
    myCommand.Connection = myConnection;
```

或者：
```
    SqlCommand myCommand = myConnection.CreateCommand;
```

**3．设置SqlCommand的查询文本。**
```
    myCommand.CommandText = "Select * FROM mindata";
```

或者第2种构造：
```
    SqlCommand myCommand = new SqlCommand(mySelectQuery);
```

给SqlCommand对象提供两个查询字符串，每个查询字符串访问不同的表，返回不同的结果集。两个查询语句用分号分隔。

**4. 执行命令。**

`ExecuteReader`

返回一行或多行

`ExecuteNonQuery`

对 Connection 执行 Transact-SQL 语句并返回受影响的行数(int)

`ExecuteScalar`

返回单个值(如一个聚合值).返回结果集中第一行的第一列。忽略额外的列或行

`ExecuteXmlReader`

将 CommandText 发送到 Connection 并生成一个 XmlReader 对象。
```
    SqlDataReader myReader = myCommand.ExecuteReader();

    或SqlDataReader myReader = myCommand.ExecuteReader(CommandBehavior.CloseConnection);

       while(myReader.Read()) //循环读取数据
       {
          Console.WriteLine(myReader.GetString(0));// 获取指定列的字符串形式的值
          Console.WriteLine(myReader. GetValue(1));// 获取以本机格式表示的指定列的值
        }
     

    CommandText = "select count(*) as NumberOfRegions from region";
    Int count = (int) myCommand.ExecuteScalar();
```
关于OleDbCommand对象的使用。

**四．DataReader的使用**

**1．遍历结果集**
```
    while (myReader.Read())
      Console.WriteLine(" {0} {1}", myReader.GetInt32(0), myReader.GetString(1));
    myReader.Close();
```

**2．使用序数索引器。**
```
    while (myReader.Read())
      Console.WriteLine(" {0} {1}", myReader[0].ToString(), myReader[1].ToString());
    myReader.Close();
```

**3．使用列名索引器。**
```
    while (myReader.Read())
      Console.WriteLine(" {0} {1}", myReader["code].ToString(), myReader["name"].ToString());
    myReader.Close();
```

**4.使用类型访问器。**
```
    public char GetChar(int i); 获取指定列的单个字符串形式的值
    public DateTime GetDateTime(int i); 获取指定列的 DateTime 对象形式的值
    public short GetInt16(int i); 获取指定列的 16 位有符号整数形式的[C#]
    public string GetString(int i); 获取指定列的字符串形式的值
```

**5．得到列信息。**
```
    myReader.FieldCount     获取当前行中的列数
    myReader.GetFieldType(序号)   获取是对象的数据类型的 Type
    myReader.GetDataTypeName(序号)  获取源数据类型的名称
    myReader.GetName(序号)     获取指定列的名称
    myReader.GetOrdinal(序号)   在给定列名称的情况下获取列序号
```

**6.得到数据表的信息。**
```
    myReader.GetSchemaTable()   返回一个 DataTable
```

**7．操作多个结果集。**
```
    myReader.NextResult()     使数据读取器前进到下一个结果集
    do
    {
      while (myReader.Read())
      Console.WriteLine(" {0} {1}", myReader.GetInt32(0), myReader.GetString(1));
    }
    while(myReader.NextResult());
```

**五．DataAdapter**

**1.创建SqlDataAdapter**

初始化 SqlDataAdapter 类的新实例。

`public SqlDataAdapter();`

将指定的 SqlCommand 作为SelectCommand 属性，初始化 SqlDataAdapter 类的新实例。

`public SqlDataAdapter(SqlCommand);`

用 selectcommand字符串 和 SqlConnection对象初始化SqlDataAdapter 类的新实例。

`public SqlDataAdapter(string, SqlConnection);`

用 selectcommand字符串 和 一个连接字符串 初始化SqlDataAdapter 类的新实例。

`public SqlDataAdapter(string, string);`

**2．DataAdapter和SqlConnection，SqlCommand建立关联。**

1.DataAdapter在构造参数时建立

2.
```
SqlDataAdapter adapter = new SqlDataAdapter();
adapter.SelectCommand = new SqlCommand(query, conn);
``` 

3．`DataAdapter.Fill()`方法。

在 DataSet 中添加或刷新行以匹配使用 DataSet 名称的数据源中的行，并创建一个名为“Table”的 DataTable。

```
public override int Fill(DataSet);
```
在 DataSet 中添加或刷新行以匹配使用 DataSet 和 DataTable 名称的数据源中的行。

`public int Fill(DataSet, string);`

在 DataSet 的指定范围中添加或刷新行以匹配使用 DataSet 和 DataTable 名称的数据源中的行。

`public int Fill(DataSet, int, int, string);`

在 DataTable 中添加或刷新行以匹配使用 DataTable 名称的数据源中的行。
```
 public int Fill(DataTable);
```

在 DataTable 中添加或刷新行以匹配使用指定 DataTable 和 IDataReader 名称的数据源中的行。

`protected virtual int Fill(DataTable, IDataReader);`

在 DataTable 中添加或刷新行以匹配使用 DataTable 名称、指定的 SQL Select 语句和 CommandBehavior 的数据源中的行。

`protected virtual int Fill(DataTable, IDbCommand, CommandBehavior);`
 

**六．DataTable 类**

**七．DataColumn 类**

**八．DataRow 类** 

**九．DataSet 类**

1．创建DataSet 对象

初始化 DataSet 类的新实例。

`public DataSet();`

 

用给定名称初始化 DataSet 类的新实例。

`public DataSet(string);`

 

2．用DataAdapter填充DataSet
```
    DataSet myds=new DataSet();
    adapter.fill(myds)
    adapter.fill(myds,”表名”);  用一个表去填充DataSet.
```
**十. DataTableCollection 类**

表示 DataSet 的表的集合。
```
    DataTableCollection dtc = ds.Tables;
    DataTable table = dtc[“表名”];
    String strExpr = "id > 5";
    String strSort = "name DESC";
    DataRow[] foundRows = customerTable.Select( strExpr, strSort,);
```

进行动态的筛选和排序。

DataTable.Select() 方法 ：  获取 DataRow 对象的数组，

①获取所有 DataRow 对象的数组。
```
    public DataRow[] Select();
```

②按主键顺序(如没有主键,则按照添加顺序)，获取与筛选条件相匹配的所有 DataRow 对象的数组。
```
    public DataRow[] Select(string);
```

③获取按照指定的排序顺序且与筛选条件相匹配的所有 DataRow 对象的数组。
```
    public DataRow[] Select(string, string);
```

④获取与排序顺序中的筛选器以及指定的状态相匹配的所有 DataRow 对象的数组。
```
    public DataRow[] Select(string, string, DataViewRowState);
```

**十一。DataView 类**

是DataTable内容的动态视图。

**1．  创建对象**

初始化 DataView 类的新实例。
```
      public DataView();
```

用指定的 DataTable 初始化 DataView 类的新实例。
```
      public DataView(DataTable);
```

用指定的 DataTable、RowFilter、Sort 和 DataViewRowState 初始化 DataView 类的新实例。
```
    public DataView(DataTable, string, string, DataViewRowState);

    DataView myview = new DataView(ds.Tables["Suppliers"], "id > 5", "name DESC"，
    DataViewRowState.CurrentRows);
```

2_* .得到DataView的行数据。_*
```
       foreach (DataRowView myrowview in myview)
       {
         for (int i = 0; i < myview.Table.Columns.Count; i++)
            Console.Write(myrowview [i] + " ");
         Console.WriteLine();
       }
```