---
title: SQL语法参考
date: 2006-05-16T01:48:43+00:00
layout: post
categories:
  - sql
---
一、数据定义语言 ｄｄｌ（data definition language)

数据定义语言是指对数据的格式和形态下定义的语言，它是每个数据库要建立时候时首先要面对的，举凡数据分哪些表格关系、表格内的有什麽字段主键、表格和表格之间互相参考的关系等等，都是在开始的时候所必须规划好的。

１、建立表格：

一、数据定义语言 ｄｄｌ（data definition language)

数据定义语言是指对数据的格式和形态下定义的语言，它是每个数据库要建立时候时首先要面对的，举凡数据分哪些表格关系、表格内的有什麽字段主键、表格和表格之间互相参考的关系等等，都是在开始的时候所必须规划好的。

１、建立表格：

create table table_name(

column1 datatype \[not null\] \[not null primary key\],

column2 datatype [not null],

…）

说明：　

datatype –是数据的格式，详见表。

not null –可不可以允许数据有空的（尚未有资料填入）。

primary key –是本表的主键。

２、更改表格　

alter table table_name

add column column_name datatype

说明：增加一个字段（没有删除某个字段的语法。）

alter table table_name

add primary key (column_name)

说明：更改表得的定义把某个字段设为主键。

alter table table_name

drop primary key (column_name)

说明：把主键的定义删除。

３、建立索引　

create index index_name on table_name (column_name)

说明：对某个表格的字段建立索引以增加查询时的速度。

４、删除表，删除索引

drop table_name

drop index_name

二、数据类型 datatypes

smallint

16 位元的整数。

interger

32 位元的整数。

decimal(p,s)

p 精确值和 s 大小的十进位整数，精确值p是指全部有几个数(digits)大小值，s是指小数

点後有几位数。如果没有特别指定，则系统会设为 p=5; s=0 。

float

32位元的实数。

double

64位元的实数。

char(n)

n 长度的字串，n不能超过 254。

varchar(n)

长度不固定且其最大长度为 n 的字串，n不能超过 4000。

graphic(n)

和 char(n) 一样，不过其单位是两个字元 double-bytes， n不能超过127。这个形态是为

了支援两个字元长度的字体，例如中文字。

vargraphic(n)

可变长度且其最大长度为 n 的双字元字串，n不能超过 2000。

date

包含了 年份、月份、日期。

time

包含了 小时、分钟、秒。

timestamp

包含了 年、月、日、时、分、秒、千分之一秒。

三、数据操作语言 ｄｍｌ （data manipulation language)

数据定义好之後接下来的就是数据的操作。数据的操作不外乎增加记录（insert)、查询数据（query）、更改记录（update) 、删除记录（delete）四种模式，以下分别介绍他们的语法：

１、增加记录：

insert into table_name (column1,column2,…)

values ( value1,value2, …)

说明：

1.若没有指定column 系统则会按表格内的字段顺序填入资料。

2.字段的数据类型和所填入的数据必须吻合。

3.table_name 也可以是视图 view_name。

insert into table_name (column1,column2,…)

select columnx,columny,… from another_table

说明：也可以经过一个子查询（subquery）把别的表格的数据填入。

２、查询数据：

基本查询

select column1,columns2,…

from table_name

说明：把table_name 的特定字段数据全部列出来

select *

from table_name

where column1 = xxx

\[and column2 > yyy\] \[or column3 zzz\]

说明：

1.&#8217;*&#8217;表示全部的字段都列出来。

2.where 之後是接条件表达式，把符合条件的数据列出来。

select column1,column2

from table_name

order by column2 [desc]

说明：order by 是指定以某个字段做排序，[desc]是指从大到小排列，若没有指明，则是从小到大

排列。

组合查询

组合查询是指所查询得数据来源并不只有单一的表格，而是联合一个以上的

表格才能够得到结果的。

select *

from table1,table2

where table1.colum1=table2.column1

说明：

1.查询两个表格中其中 column1 值相同的数据。

2.当然两个表格相互比较的字段，其数据类型必须相同。

3.一个复杂的查询其动用到的表格可能会很多个。

整合性的查询：

select count (*)

from table_name

where column_name = xxx

说明：

查询符合条件的数据有多少项。count(column_name),计算某个字段的记录数。

select sum(column1)

from table_name

说明：

1.计算出总和，所选的字段必须是可数的数字类型。

2.除此以外还有 avg() 是计算平均、max()、min()计算最大最小值的整合性查询。

select column1,avg(column2)

from table_name

group by column1

having avg(column2) > xxx

说明：

1.group by: 以column1 为一组计算 column2 的平均值必须和 avg、sum等整合性查询的关键字

一起使用。

2.having : 必须和 group by 一起使用作为整合性的限制。

复合性的查询

select *

from table_name1

where exists (

select *

from table_name2

where conditions )

说明：

1.where 的 conditions 可以是另外一个的 query。

2.exists 在此是指存在与否。

select *

from table_name1

where column1 in (

select column1

from table_name2

where conditions )

说明：　

1. in 後面接的是一个集合，表示column1 存在集合里面。

2. select 出来的资料形态必须符合 column1。

其他查询

select *

from table_name1

where column1 like &#8216;x%&#8217;

说明：like 必须和後面的&#8217;x%&#8217; 相呼应表示以 x为开头的字串。

select *

from table_name1

where column1 in (&#8216;xxx&#8217;,&#8217;yyy&#8217;,..)

说明：in 後面接的是一个集合，表示column1 存在集合里面。

select *

from table_name1

where column1 between xx and yy

说明：between 表示 column1 的值介於 xx 和 yy 之间。

３、更改记录：

update table_name

set column1=&#8217;xxx&#8217;

where conditoins

说明：

1.更改某个字段，设定其值为&#8217;xxx&#8217;。

2.conditions 是所要符合的条件、若没有 where 则整个 table 的字段都会全部被更改。

４、删除记录：

delete from table_name

where conditions

说明：删除符合条件的记录。

说明：关于where条件后面如果包含有日期的比较，不同数据库有不同的表达式。具体如下：

(1)如果是access数据库，则为：where mydate>#2000-01-01#

(2)如果是oracle数据库，则为：where mydate>cast(&#8216;2000-01-01&#8217; as date)

或：where mydate>to_date(&#8216;2000-01-01&#8242;,&#8217;yyyy-mm-dd&#8217;)

在delphi中写成：

thedate=&#8217;2000-01-01&#8242;;

query1.sql.add(&#8216;select * from abc where mydate>cast(&#8216;+&#8221;&#8221;+thedate+&#8221;&#8221;+&#8217; as date)&#8217;);

如果比较日期时间型，则为：

where mydatetime>to_date(&#8216;2000-01-01 10:00:01&#8242;,&#8217;yyyy-mm-dd hh24:mi:ss&#8217;)