---
title: '在C#中应用哈希表'
date: 2006-06-11T04:54:58+00:00
layout: post
categories:
  - .Net
---

一,哈希表(Hashtable)简述

在.NET Framework中，Hashtable是System.Collections命名空间提供的一个容器，用于处理和表现类似key/value的键值对，其中key通常可用来快速查找，同时key是区分大小写；value用于存储对应于key的值。Hashtable中key/value键值对均为object类型，所以Hashtable可以支持任何类型的key/value键值对.

二,哈希表的简单操作

在哈希表中添加一个key/value键值对：HashtableObject.Add(key,value);

在哈希表中去除某个key/value键值对：HashtableObject.Remove(key);

从哈希表中移除所有元素： HashtableObject.Clear();

判断哈希表是否包含特定键key： HashtableObject.Contains(key);

下面控制台程序将包含以上所有操作：
```
    using System;
    using System.Collections; //使用Hashtable时，必须引入这个命名空间
    class hashtable
    {
      public static void Main()
      {
      Hashtable ht=new Hashtable(); //创建一个Hashtable实例
      ht.Add(“E”,”e”);//添加key/value键值对
      ht.Add(“A”,”a”);
      ht.Add(“C”,”c”);
      ht.Add(“B”,”b”);
      string s=(string)ht[“A”];
      if(ht.Contains(“E”)) //判断哈希表是否包含特定键,其返回值为true或false
        Console.WriteLine(“the E key:exist”);
      ht.Remove(“C”);//移除一个key/value键值对
      Console.WriteLine(ht[“A”]);//此处输出a
      ht.Clear();//移除所有元素
      Console.WriteLine(ht[“A”]); //此处将不会有任何输出
      }
    } 
```

三,遍历哈希表

遍历哈希表需要用到DictionaryEntry Object，代码如下：
```
    for(DictionaryEntry de in ht) //ht为一个Hashtable实例
     {
       Console.WriteLine(de.Key);//de.Key对应于key/value键值对key
       Console.WriteLine(de.Value);//de.Key对应于key/value键值对value
     } 
```

四,对哈希表进行排序

对哈希表进行排序在这里的定义是对key/value键值对中的key按一定规则重新排列，但是实际上这个定义是不能实现的，因为我们无法直接在Hashtable进行对key进行重新排列，如果需要Hashtable提供某种规则的输出，可以采用一种变通的做法：
```
     ArrayList akeys=new ArrayList(ht.Keys); //别忘了导入System.Collections
     akeys.Sort(); //按字母顺序进行排序
     for(string skey in akeys)
     {
       Console.Write(skey + “:”);
       Console.WriteLine(ht[skey]);//排序后输出
     }
```