---
title: sql语句考题
date: 2018-11-14 19:22:47
tags:
category: mysql
---



现有表app_login，包含3个字段（uid、day、isguest),各uid在每一天可能有任意条记录,文后包含测试用例；
现求9月份（1至30日）中
1.每个uid在每天各有多少条记录
2.每个uid在9月份各登录了几天
3.每个uid每个月登录的次数
4.每个uid在9月份登录的前七次是哪几天，后7次是哪几天（当月不足7天的取全部）

第一题 ：每个uid在每天各有多少条记录？
每个uid每天联想到->group by uid,day
```
select uid,day,count(*)as count from app_login GROUP BY uid,day
```

第二题 ：每个uid在9月份各登录了几天？
MySQL 字符串截取函数：left(), right(), substring(), substring_index()
例：select left('sqlstudy.com', 3)->sql;
select right('sqlstudy.com', 3)->com;
substring(str, pos); substring(str, pos, len)->substring(day,1,7)->'2019-09'
```
select uid,count(day)as login_day from app_login where substring(day,1,7)='2019-09' GROUP BY uid
```

第三题 ：每个uid每个月登录的次数
每个uid每个月->group by uid,month
```
select uid,left(day,7) month,count(uid) login_times 
from app_login 
group by uid,month 
order by uid,month;
```

第四题 ：每个uid在9月份登录的前七次是哪几天，后7次是哪几天？（当月不足7天的取全部）

GROUP_CONCAT函数返回一个字符串结果，该结果由分组中的值连接组合而成。
substring_index:（“待截取有用部分的字符串”，“截取数据依据的字符”，截取字符的位置N）
这里的group_concat(day)先将day连接起来，再substring_index(group_concat(day),',',7)意思是取第七个逗号前面的字符串，也就是取前七天；substring_index(group_concat(day),',',-7)意思就是取倒数第七个后面的字符串

```
select uid,substring_index(group_concat(day),',',7) before_day,substring_index(group_concat(day),',',-7) after_day
from app_login
where left(day,7)='2019-09'
group by uid
order by uid,day;
```

测试用例：
```
INSERT INTO `app_login` VALUES (1, '2019-09-03', '是');
INSERT INTO `app_login` VALUES (2, '2020-01-03', '否');
INSERT INTO `app_login` VALUES (3, '2020-01-04', '否');
INSERT INTO `app_login` VALUES (4, '2020-01-07', '否');
INSERT INTO `app_login` VALUES (5, '2019-12-01', '否');
INSERT INTO `app_login` VALUES (6, '2019-12-02', '否');
INSERT INTO `app_login` VALUES (7, '2019-12-02', '否');
INSERT INTO `app_login` VALUES (8, '2019-12-12', '否');
INSERT INTO `app_login` VALUES (1, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-03', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-05', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-09', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-09', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-09', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-19', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-22', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-01', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-01', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-11', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-06', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-05', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-04', '否');
INSERT INTO `app_login` VALUES (2, '2019-09-03', '否');
INSERT INTO `app_login` VALUES (8, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (9, '2019-09-01', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-01', '否');
INSERT INTO `app_login` VALUES (9, '2019-09-01', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-18', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-15', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-13', '否');
INSERT INTO `app_login` VALUES (1, '2019-09-13', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-12', '否');
INSERT INTO `app_login` VALUES (1, '2019-09-15', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-14', '否');
INSERT INTO `app_login` VALUES (9, '2019-09-18', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-19', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (6, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (9, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (9, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (9, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (9, '2019-09-02', '否');
INSERT INTO `app_login` VALUES (9, '2019-09-02', '否');
```