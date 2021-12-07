---
title: mysql索引建立原则
date: 2019-12-27 16:28:43
tags:
---


单值索引：一个索引只包含单个列，一个表可以有多个单列索引 
唯一索引：索引列的值必须唯一，但允许有空值  
复合索引：一个索引包含多个列，实际开发中推荐使用  
实际开发中推荐使用复合索引，并且单表创建的索引个数建议不要超过五个

基本语法：  
创建：
create [unique] index indexName on tableName (columnName...)
alter tableName add [unique] index [indexName] on (columnName...)  
删除：
drop index [indexName] on tableName  
查看：  
show index from tableName  

哪些情况需要建索引：  
主键，唯一索引  
经常用作查询条件的字段需要创建索引  
经常需要排序、分组和统计的字段需要建立索引  
查询中与其他表关联的字段，外键关系建立索引  

哪些情况不要建索引：  
表的记录太少，百万级以下的数据不需要创建索引  
经常增删改的表不需要创建索引  
数据重复且分布平均的字段不需要创建索引，如 true,false 之类。  
频发更新的字段不适合创建索引  
where条件里用不到的字段不需要创建索引  