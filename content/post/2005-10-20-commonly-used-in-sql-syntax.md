---
title: 常用的SQL语法
date: 2005-10-20T14:56:07+00:00
layout: post
categories:
  - 我的世界
tags:
  - SQL
---
说明：复制表(只复制结构,源表名：a 新表名：b)

SQL: `select * into b from a where 1<>1`

说明：拷贝表(拷贝数据,源表名：a 目标表名：b)

SQL: `insert into b(a, b, c) select d,e,f from b;`

说明：显示文章、提交人和最后回复时间

SQL: `select a.title,a.username,b.adddate from table a,(select max(adddate) adddate from table where table.title=a.title) b`

说明：外连接查询(表名1：a 表名2：b)

SQL: `select a.a, a.b, a.c, b.c, b.d, b.f from a LEFT OUT JOIN b ON a.a = b.c`

说明：日程安排提前五分钟提醒

SQL: `select * from 日程安排 where datediff('minute',f开始时间,getdate())>5`

说明：两张关联表，删除主表中已经在副表中没有的信息

SQL:

`delete from info where not exists ( select * from infobz where info.infid=infobz.infid )`

说明：–

SQL:

```sql
SELECT A.NUM, A.NAME, B.UPD_DATE, B.PREV_UPD_DATE
FROM TABLE1,
 (SELECT X.NUM, X.UPD_DATE, Y.UPD_DATE PREV_UPD_DATE
 FROM (SELECT NUM, UPD_DATE, INBOUND_QTY, STOCK_ONHAND
 FROM TABLE2
 WHERE TO_CHAR(UPD_DATE,'YYYY/MM') = TO_CHAR(SYSDATE, 'YYYY/MM')) X,
 (SELECT NUM, UPD_DATE, STOCK_ONHAND
 FROM TABLE2
 WHERE TO_CHAR(UPD_DATE,'YYYY/MM') =
 TO_CHAR(TO_DATE(TO_CHAR(SYSDATE, 'YYYY/MM') ¦¦ '/01','YYYY/MM/DD') – 1, 'YYYY/MM') ) Y,
 WHERE X.NUM = Y.NUM （+）
 AND X.INBOUND_QTY + NVL(Y.STOCK_ONHAND,0) X.STOCK_ONHAND ) B
WHERE A.NUM = B.NUM
```

说明：–

SQL:
```sql
select * from studentinfo where not exists(select * from student where studentinfo.id=student.id) and 系名称='”&strdepartmentname&”' and 专业名称='”&strprofessionname&”' order by 性别,生源地,高考总成绩
```

说明：

从数据库中去一年的各单位电话费统计(电话费定额贺电化肥清单两个表来源）

SQL:
```sql
SELECT a.userper, a.tel, a.standfee, TO_CHAR(a.telfeedate, 'yyyy') AS telyear,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '01', a.factration)) AS JAN,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '02', a.factration)) AS FRI,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '03', a.factration)) AS MAR,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '04', a.factration)) AS APR,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '05', a.factration)) AS MAY,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '06', a.factration)) AS JUE,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '07', a.factration)) AS JUL,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '08', a.factration)) AS AGU,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '09', a.factration)) AS SEP,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '10', a.factration)) AS OCT,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '11', a.factration)) AS NOV,
 SUM(decode(TO_CHAR(a.telfeedate, 'mm'), '12', a.factration)) AS DEC
FROM (SELECT a.userper, a.tel, a.standfee, b.telfeedate, b.factration
 FROM TELFEESTAND a, TELFEE b
 WHERE a.tel = b.telfax) a
GROUP BY a.userper, a.tel, a.standfee, TO_CHAR(a.telfeedate, 'yyyy')
```

说明：四表联查问题：

SQL:
```sql
select * from a left inner join b on a.a=b.b right inner join c on a.a=c.c inner join d on a.a=d.d where …..
```

说明：得到表中最小的未使用的ID号

SQL:
```sql
SELECT (CASE WHEN EXISTS(SELECT * FROM Handle b WHERE b.HandleID = 1) THEN MIN(HandleID) + 1 ELSE 1 END) as HandleID
FROM Handle
WHERE NOT HandleID IN (SELECT a.HandleID – 1 FROM Handle a)
```

相同的记录：
```sql
select * from a join b on a.id=b.id
```

删除相同的记录:
```sql
delete a from b ,a where a.id=b.id;
```

不同的记录
```sql
select * from a where a.id not in(select id from b)
union
select * from b where b.id not in(select id from a)
```

查找某段时间内的用户资料

```sql
select * from dbms_user_info_def t where crbt_Status='N' and to_char(reg_date,'yyyy-mm-dd hh24:mm:ss') between '2005-01-01 00:00:00' and '2005-01-26 00:00:00';
```

合并表

```sql
insert into a select * from b;
```