---
title: mysql数据库存储引擎底层实现
date: 2019-04-09 18:08:51
tags:
category: mysql
---



## InnoDB

InnoDB支持事务，且支持四种隔离级别，即原子性（atomicity）、一致性（consistency）、隔离性（isolation）、持久性（durability）。  
原子性：一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被恢复（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。  
一致性：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。  
隔离性：数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。    
持久性：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

## MyISAM
MyISAM的存储文件有三个，后缀名分别是.frm、.MYD、.MYI，其中.frm是表文件，.MYD是数据文件，.MYI是索引文件。
MyISAM只支持表锁，且不支持事务。MyISAM由于有单独的索引文件，在读取数据方面性能很高。

## InnoDB和MyISAM的区别
MyISAM强调的是性能，每次查询都具有原子性，其执行速度比InnoDB类型更快，但不提供事务支持。InnoDB：提供事务支持事务，外部键等高级数据库功能。 具有事务(commit)、回滚(rollback)和崩溃修复能力(crash recovery capabilities)的事务安全(transaction-safe (ACID compliant))型表。
MyISAM只支持表锁，用户在操作其表时，select、update、delete、insert语句都会自动给表加锁，如果加锁以后的表满足insert并发的情况下，可以在表的尾部插入新的数据。

***
## 存储引擎
MySQL常用的存储引擎有InnoDB和MyISAM，其中MyISAM是MySQL的默认存储引擎。  
选择存储引擎的基本原则
采用MyISAM: 
1.R/W > 100:1 且update相对较少   
2.并发不高  
3.表数据量小      
4.硬件资源有限  
采用InooDB引擎:  
1.R/W比较少，频繁更新大字段  
2.表数据量超过1000万，并发高  
3.安全性和可用性要求高



## 索引存储结构
InnoDB和MyISAM都是用B+Tree来存储数据的。

B-Tree作为索引结构，如果存储的数据量很大时会导致B-Tree的深度较大，增大查询的磁盘IO次数，从而影响到查询效率。
而在B+Tree中，所有数据记录节点都是按照值大小放在一层的叶子节点上，而非叶子节点上只存储key值信息，这样可以大大加大每个节点存储的key值数量，降低B+Tree的高度。
B+Tree相对B-Tree的基础有两大变化：
数据是存在叶子节点上的  
数据节点之间是有指针指向的

![](https://www.blog.starmoon.tech/img/bplustree.png)


聚集索引  
InnoDB 是以 ID 为索引的数据存储。
采用 InnoDB 引擎的数据存储文件有两个，一个定义文件，一个是数据文件。
InnoDB 通过 B+Tree 结构对 ID 建索引，然后在叶子节点中存储记录。


非聚集索引  
Myisam 引擎也是采用的 B+Tree 结构来作为索引结构。
由于 Myisam 中的索引和数据分别存放在不同的文件，所以在索引树中的叶子节点中存的数据是该索引对应的数据记录的地址，由于数据与索引不在一起，所以 Myisam 是非聚簇索引。
