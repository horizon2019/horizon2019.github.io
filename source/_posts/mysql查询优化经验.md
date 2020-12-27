---
title: mysql查询优化经验
date: 2020-12-27 15:44:41
tags:
category: mysql
---

《阿里巴巴Java开发手册》提出单表行数超过500万行或者单表容量超过2GB，才推荐分库分表。性能由综合因素决定，抛开业务复杂度，影响程度依次是硬件配置、MySQL配置、数据表设计、索引优化。500万这个值仅供参考，并非铁律。

**性能下降的原因**

从程序员的角度
查询语句写的不好
没建索引，索引建的不合理或索引失效
关联查询有太多的join
从服务器的角度
服务器磁盘空间不足
服务器调优配置参数设置不合理


最大并发数
并发数是指同一时刻数据库能处理多少个请求，由max_connections和max_user_connections决定。max_connections是指MySQL实例的最大连接数，上限值是16384，max_user_connections是指每个数据库用户的最大连接数。
MySQL会为每个连接提供缓冲区，意味着消耗更多的内存。如果连接数设置太高硬件吃不消，太低又不能充分利用硬件。一般要求两者比值超过10%，计算方法如下：
max_used_connections / max_connections * 100% = 3/100 *100% ≈ 3%

查询耗时0.5秒
建议将单次查询耗时控制在0.5秒以内，0.5秒是个经验值，源于用户体验的3秒原则。如果用户的操作3秒内没有响应，将会厌烦甚至退出。响应时间=客户端UI渲染耗时+网络请求耗时+应用程序处理耗时+查询数据库耗时，0.5秒就是留给数据库1/6的处理时间。

充分利用但不滥用索引，须知索引也消耗磁盘和CPU。
不推荐使用数据库函数格式化数据，交给应用程序处理。
不推荐使用外键约束，用应用程序保证数据准确性。
写多读少的场景，不推荐使用唯一索引，用应用程序保证唯一性。
适当冗余字段，尝试创建中间表，用应用程序计算中间结果，用空间换时间。
不允许执行极度耗时的事务，配合应用程序拆分成更小的事务。
预估重要数据表（比如订单表）的负载和数据增长态势，提前优化。


**数据表设计优化**

数据类型
数据类型的选择原则：更简单或者占用空间更小。
如果长度能够满足，整型尽量使用tinyint、smallint、medium_int而非int。
如果字符串长度确定，采用char类型。
如果varchar能够满足，不采用text类型。
精度要求较高的使用decimal类型，也可以使用BIGINT，比如精确两位小数就乘以100后保存。
尽量采用timestamp而非datetime。
避免空值
MySQL中字段为NULL时依然占用空间，会使索引、索引统计更加复杂。从NULL值更新到非NULL无法做到原地更新，容易发生索引分裂影响性能。尽可能将NULL值用有意义的值代替，也能避免SQL语句里面包含is not null的判断。

text类型优化
由于text字段储存大量数据，表容量会很早涨上去，影响其他字段的查询性能。建议抽取出来放在子表里，用业务主键关联。

**索引优化**

索引分类
普通索引：最基本的索引。
组合索引：多个字段上建立的索引，能够加速复合查询条件的检索。
唯一索引：与普通索引类似，但索引列的值必须唯一，允许有空值。
组合唯一索引：列值的组合必须唯一。
主键索引：特殊的唯一索引，用于唯一标识数据表中的某一条记录，不允许有空值，一般用primary key约束。
全文索引：用于海量文本的查询，MySQL5.6之后的InnoDB和MyISAM均支持全文索引。由于查询精度以及扩展性不佳，更多的企业选择Elasticsearch。
索引优化
分页查询很重要，如果查询数据量超过30%，MYSQL不会使用索引。
单表索引数不超过5个、单个索引字段数不超过5个。
字符串可使用前缀索引，前缀长度控制在5-8个字符。
字段唯一性太低，增加索引没有意义，如：是否删除、性别。

**OR优化**
在Innodb引擎下or无法使用组合索引，比如：
select id，product_name from orders where mobile_no = '13421800407' or user_id = 100;

OR无法命中mobile_no + user_id的组合索引，可采用union，如下所示：
(select id，product_name from orders where mobile_no = '13421800407')
 union
(select id，product_name from orders where user_id = 100);


**IN优化**
IN适合主表大子表小，EXIST适合主表小子表大。由于查询优化器的不断升级，很多场景这两者性能差不多一样了。
尝试改为join查询，举例如下：
select id from orders where user_id in (select id from user where level = 'VIP');

采用JOIN如下所示：
select o.id from orders o left join user u on o.user_id = u.id where u.level = 'VIP';

不做列运算

通常在查询条件列运算会导致索引失效，如下所示：
查询当日订单
select id from order where date_format(create_time，'%Y-%m-%d') = '2019-07-01';
date_format函数会导致这个查询无法使用索引，改写后：
select id from order where create_time between '2019-07-01 00:00:00' and '2019-07-01 23:59:59';

避免Select all
如果不查询表中所有的列，避免使用SELECT *，它会进行全表扫描，不能有效利用索引。

Join优化

join的实现是采用Nested Loop Join算法，就是通过驱动表的结果集作为基础数据，通过该结数据作为过滤条件到下一个表中循环查询数据，然后合并结果。如果有多个join，则将前面的结果集作为循环数据，再次到后一个表中查询数据。
驱动表和被驱动表尽可能增加查询条件，满足ON的条件而少用Where，用小结果集驱动大结果集。
被驱动表的join字段上加上索引，无法建立索引的时候，设置足够的Join Buffer Size。
禁止join连接三个以上的表，尝试增加冗余字段。

Limit优化 

limit用于分页查询时越往后翻性能越差，解决的原则：缩小扫描范围，如下所示：
select * from orders order by id desc limit 100000,10 
耗时0.4秒
select * from orders order by id desc limit 1000000,10
耗时5.2秒
先筛选出ID缩小查询范围，写法如下：
select * from orders where id > (select id from orders order by id desc  limit 1000000, 1) order by id desc limit 0,10
耗时0.5秒
如果查询条件仅有主键ID，写法如下：
select id from orders where id between 1000000 and 1000010 order by id desc
耗时0.3秒
如果以上方案依然很慢呢？只好用游标了，感兴趣的朋友阅读JDBC使用游标实现分页查询的方法


**总结**

索引是排好序且快速查找的数据结构。其目的是为了提高查询的效率。
创建索引后，查询数据变快，但更新数据变慢。
性能下降的原因很可能是索引失效导致。
索引创建的原则，经常查询的字段适合创建索引，频繁需要更新的数据不适合创建索引。
索引字段频繁更新，或者表数据物理删除容易造成索引失效。
擅用 explain 分析sql语句
除了优化sql语句外，还可以优化表的设计。如尽量做成单表查询，减少表之间的关联。设计归档表等。