---
title: leetcode262数据库题解
date: 2020-02-07 23:09:46
category: leetcode
tags:
---

#### 此处撰写解题思路 ：
1.读题： 取消率的计算方式如下：(被司机或乘客取消的非禁止用户生成的订单数量) / (非禁止用户生成的订单总数) 
2.涉及知识点： sum ,group by,round的使用

#### 知识点归纳：
`1.数据库表的连接(Left join , Right Join, Inner Join)用法详解`
Left Join, Inner Join 的相关内容，非常实用，对于理解原理和具体应用都很有帮助！left join 是left outer join的简写，left join默认是outer属性的。
#### Inner Join
Inner Join 逻辑运算符返回满足第一个（顶端）输入与第二个（底端）输入联接的每一行。这个和用select查询多表是一样的效果，所以很少用到；
outer join则会返回每个满足第一个（顶端）输入与第二个（底端）输入的联接的行。它还返回任何在第二个输入中没有匹配行的第一个输入中的行。关键就是后面那句，返回的多一些。所以通常意义上的left join就是left outer join
`先看一些最简单的例子`
Table A
aid   adate
1      a1
2      a2
3      a3
TableB
bid bdate
1    b1
2   b2
4    b4
两个表a,b相连接,要取出id相同的字段
select * from a inner join b on a.aid = b.bid这是仅取出匹配的数据.
此时的取出的是:
1 a1 b1
2 a2 b2

那么left join 指:
select * from a left join b on a.aid = b.bid
首先取出a表中所有数据,然后再加上与a,b匹配的的数据
此时的取出的是:
1 a1 b1
2 a2 b2
3 a3 空字符

同样的也有right join
指的是首先取出b表中所有数据,然后再加上与a,b匹配的的数据
此时的取出的是:
1 a1 b1
2 a2 b2
4 空字符 b4

`LEFT JOIN 或 LEFT OUTER JOIN`。
左向外联接的结果集包括 LEFT OUTER 子句中指定的左表的所有行，而不仅仅是联接列所匹配的行。如果左表的某行在右表中没有匹配行，则在相关联的结果集行中右表的所有选择列表列均为空值

#### 2.GROUP BY 语句
GROUP BY 语句用于结合合计函数，根据一个或多个列对结果集进行分组。

#### 3.SQL ROUND() 语法
SELECT ROUND(column_name,decimals) FROM table_name
column_name必需。要舍入的字段。
decimals必需。规定要返回的小数位数。
原答案：
select Trips.Request_at as Day ,Round(sum(Status='cancelled_by_client' or Status='cancelled_by_driver')/count(*),2) as 'Cancellation Rate' from Trips inner join Users on Trips.Client_Id=Users.Users_Id where Users.Banned='No' and Trips.Request_at>='2013-10-01' and Trips.Request_at <='2013-10-03' Group by Trips.Request_at 

纠正：sum关系错误

修改后：
select Trips.Request_at as Day ,Round(sum((Status='cancelled_by_client' or Status='cancelled_by_driver')/count(*)),2) as Cancellation_Rate from Trips inner join Users on Trips.Client_Id=Users_Id where Users.Request_at>'2013-10-01' and Users.Request_at <'2013-10-03' Group by Request_at 