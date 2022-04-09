---
title: 如何使用explain分析sql语句
date: 2018-03-16 21:43:32
tags: sql优化
categories: mysql
---

## 关于慢日志查询工具
Explain命令在解决数据库性能上是第一推荐使用命令，大部分的性能问题可以通过此命令来简单的解决，Explain可以用来查看SQL语句的执行效果，可以帮助选择更好的索引和优化查询语句，写出更好的优化语句。

```
[mysqld]  
long_query_time=2  

#5.5以前版本配置如下选项  
log-slow-queries="mysql_slow_query.log"  
#5.5及以上版本配置如下选项  
slow-query-log=On  
slow_query_log_file="mysql_slow_query.log"  

log-query-not-using-indexes  
```

第一句：定义超过多少秒的查询算是慢查询，这里定义的是2秒  
第二句：定义慢查询日志的路径（注意如果是 Linux 或 Mac 系统要考虑权限问题）  
第三句：记录下没有使用索引的query  

Explain语法：explain select … from … [where …]


输出：  
```
输出：

+----+-------------+-------+-------+-------------------+---------+---------+-------+------
| id | select_type | table | type | possible_keys | key | key_len | ref | rows | Extra |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------
```

id：这是SELECT的查询序列号  
select_type：select_type就是select的类型，可以有以下几种：  
```
SIMPLE：简单SELECT(不使用UNION或子查询等)
 
PRIMARY：最外面的SELECT
 
UNION：UNION中的第二个或后面的SELECT语句
 
DEPENDENT UNION：UNION中的第二个或后面的SELECT语句，取决于外面的查询
 
UNION RESULT：UNION的结果。
 
SUBQUERY：子查询中的第一个SELECT
 
DEPENDENT SUBQUERY：子查询中的第一个SELECT，取决于外面的查询
 
DERIVED：导出表的SELECT(FROM子句的子查询)

```

table：显示这一行的数据是关于哪张表的

type：这列最重要，显示了连接使用了哪种类别,有无使用索引，是使用Explain命令分析性能瓶颈的关键项之一。

结果值从好到坏依次是：  
system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL
 
一般来说，得保证查询至少达到**range**级别，最好能达到**ref**，否则就可能会出现性能问题。  
 
 ```
•system: const 的一种特例，表中只有一行数据
•const: 当确定最多只会有一行匹配的时候，MySQL优化器会在查询前读取它而且只读取一次，因此非常快。使用主键查询往往就是**const** 级别的，非常高效
•eq_ref: 最多只返回一条符合条件的记录，通过使用在两个表有关联字段的时候
•ref: 通过普通索引查询匹配的很多行时的类型
•fulltext: 全文索引
•ref_or_null: 跟 ref 类似的效果，不过多一个列不能 null 的条件
•index_merge: 此连接类型表示使用了索引合并优化。在这种情况下，输出行中的 key 列包含使用的索引列表，key_len包含所用索引的最长 key 部分列表
•unique_subquery: 在使用 in 查询的情况下会取代 eq_ref
•range: 范围扫描，一个有限制的索引扫描。key 列显示使用了哪个索引。当使用=、 <>、>、>=、<、<=、IS NULL、<=>、BETWEEN 或者 IN 操作符，用常量比较关键字列时,可以使用 range
•index: 类似全表扫描，只是扫描表的时候按照索引次序进行而不是行。主要优点就是避免了排序, 但是开销仍然非常大。如在Extra列看到Using index，说明正在使用覆盖索引，只扫描索引的数据，它比按索引次序全表扫描的开销要小很多
•ALL: 全表扫描
```

possible_keys：列指出MySQL能使用哪个索引在该表中找到行  
key：显示MySQL实际决定使用的键（索引）。如果没有选择索引，键是NULL  
key_len：显示MySQL决定使用的键长度。如果键是NULL，则长度为NULL。使用的索引的长度。在不损失精确性的情况下，长度越短越好  
ref：显示使用哪个列或常数与key一起从表中选择行。  
rows：显示MySQL认为它执行查询时必须检查的行数。  
Extra：包含MySQL解决查询的详细信息，也是关键参考项之一:

```
Distinct
一旦MYSQL找到了与行相联合匹配的行，就不再搜索了 
Not exists
MYSQL 优化了LEFT JOIN，一旦它找到了匹配LEFT JOIN标准的行，就不再搜索了
Range checked for each 
Record（index map:#）
没有找到理想的索引，因此对于从前面表中来的每一 个行组合，MYSQL检查使用哪个索引，并用它来从表中返回行。这是使用索引的最慢的连接之一 
Using filesort
看到这个的时候，查询就需要优化了。MYSQL需要进行额外的步骤来发现如何对返回的行排序。它根据连接类型以及存储排序键值和匹配条件的全部行的行指针来排序全部行
Using index
列数据是从仅仅使用了索引中的信息而没有读取实际的行动的表返回的，这发生在对表 的全部的请求列都是同一个索引的部分的时候
Using temporary
看到这个的时候，查询需要优化了。这 里，MYSQL需要创建一个临时表来存储结果，这通常发生在对不同的列集进行ORDER BY上，而不是GROUP BY上
Using where
使用了WHERE从句来限制哪些行将与下一张表匹配或者是返回给用户。如果不想返回表中的全部行，并且连接类型ALL或index， 这就会发生，或者是查询有问题
```


其他一些Tip：  
当type 显示为 “index” 时，并且Extra显示为“Using Index”， 表明使用了覆盖索引。