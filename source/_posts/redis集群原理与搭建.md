---
title: redis集群原理与搭建
date: 2019-08-02 15:06:51
tags: redis集群
category: redis
---


## 前言
Redis 是我们目前大规模使用的缓存中间件，由于它强大高效而又便捷的功能，得到了广泛的使用。单节点的Redis已经就达到了很高的性能，为了提高可用性我们可以使用Redis集群。本文参考了Rdis的官方文档和使用Redis官方提供的Redis Cluster工具搭建Rdis集群。

Redis为什么这么快？

正常情况下，Redis执行命令的速度非常快，官方给出的数字是读写性能可以达到10万/秒，当然这也取决于机器的性能，但这里先不讨论机器性能上的差异，只分析一下是什么造就了Redis除此之快的速度，可以大致归纳为以下五点：
1.Redis是用C语言实现的，一般来说C语言实现的程序“距离”操作系统更近，执行速度相对会更快。
2.完全基于内存，绝大部分请求是纯粹的内存操作，非常快速。数据存在内存中，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都是O(1)；正因为 Redis 是单线程，所以要小心使用 Redis 指令，对于那些时间复杂度为 O(n) 级别的指令，一定要谨慎使用，一不小心就可能会导致 Redis 卡顿。
3.数据结构简单，对数据操作也简单，Redis中的数据结构是专门进行设计的；采用单线程，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗。
4.使用多路I/O复用模型，非阻塞IO，Redis 单线程处理大量的并发客户端连接的模型。使用底层模型不同，它们之间底层实现方式以及与客户端之间通信的应用协议不一样。
5.Redis直接自己构建了VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求；


流程架构

redis的整体核心在服务端的处理,流程图如下：
![](https://www.starmoon.cloud/img/rediscluster.png)


epoll：多路复用模型的基础  
事件：多路复用的设计方案，包含文件事件和时间事件。 命令处理：连接过程、处理过程、响应过程

上述三点，相辅相成共同构成了redis服务的核心处理流程。


Redis事件

文件事件：  
Redis通过套接字与客户端（或者其他服务器）进行连接，而文件事件就是服务器对套接字操作的抽象。服务器与客户端的通信会产生相应的文件事件，而服务器则通过监听并处理这些事件来完成一系列的网络通信操作。  
Redis基于Reactor模式开发了自己的网络事件处理器：这个处理器则被称为文件时间处理器。  
文件事件处理器使用I/O多路复用程序来同时监听多个套接字，并根据套接字目前执行的任务来为套接字关联不同的时间处理器。  
当被监听的套接字准备好执行连接应答accept、读取read、写入write、关闭close等操作时，与操作相对应的文件事件就会产生，这时文件事件处理器就会调用套接字之前关联好的事件处理器来处理这些事件。

时间事件：  
Redis服务器中一些操作（比如serverCron的函数）则需要在给定的时间点执行，时间事件就是服务器对这类定时操作的抽象。默认一秒执行10次，即100ms执行一次。  
serverCron函数的主要工作：  
1.更新服务器的各类统计信息，比如内存占用、数据库占用情况等。  
2.清理数据库中的过期键值对。  
3.关闭和清理链接失效的客户端。  
4.尝试进行AOF或RDB持久化操作。  
5.如果服务器是主服务器，对从服务器进行定时同步。
集群模式，对集群进行定期同步和连接测试。

I/O 多路复用技术  
详解：http://blog.chinaunix.net/uid-24517549-id-4051156.html  
实现机制：https://blog.csdn.net/shenya1314/article/details/73691088

## Redis 集群
提供了以下两个好处：
将数据自动切分（split）到多个节点的能力。
当集群中的一部分节点失效或者无法进行通讯时， 仍然可以继续处理命令请求的能力。


## 主从复制模型
为了使得集群在一部分节点下线或者无法与集群的大多数（majority）节点进行通讯的情况下， 仍然可以正常运作， Redis 集群对节点使用了主从复制功能： 集群中的每个节点都有 1 个至 N 个复制品（replica）， 其中一个复制品为主节点（master）， 而其余的 N-1 个复制品为从节点（slave）。  
在之前列举的节点 A 、B 、C 的例子中， 如果节点 B 下线了， 那么集群将无法正常运行， 因为集群找不到节点来处理 5501 号至 11000号的哈希槽。  
另一方面， 假如在创建集群的时候（或者至少在节点 B 下线之前）， 我们为主节点 B 添加了从节点 B1 ， 那么当主节点 B 下线的时候， 集群就会将 B1 设置为新的主节点， 并让它代替下线的主节点 B ， 继续处理 5501 号至 11000 号的哈希槽， 这样集群就不会因为主节点 B 的下线而无法正常运作了。  
不过如果节点 B 和 B1 都下线的话， Redis 集群还是会停止运作。


## 主从复制简单配置
1.首先安装redis
```
sudo apt-get install redis-server  
sudo systemctl enable redis-server.service
```

中间遇到redis.conf权限问题，就chmod开一下权限  
执行redis-cli 报错,

```
27368:M 02 Mar 16:59:25.815 # Creating Server TCP listening socket ::1:6379: bind: Cannot assign requested address
```
修改redis.conf文件中的bind配置为 bind 127.0.0.1   
重启一下redis就ok了  
首先cp 一份redis.conf文件为redis-slave.conf (一主多从看情况cp多个配置文件),注意修改redis-slave.conf以下几处地方：
```
1.log文件存放地址 logfile /var/log/redis/redis-server-slave1.log  
2.dbfilename dump-slave1.rdb  
3.port 6380
```
在redis从服务器下执行slaveof 127.0.0.1 6379重启redis，info replication可以查看到以下信息： 

```
127.0.0.1:6380> info replication
# Replication
role:slave
master_host:127.0.0.1
master_port:6379
master_link_status:up
master_last_io_seconds_ago:7
master_sync_in_progress:0
slave_repl_offset:14
slave_priority:100
slave_read_only:1
connected_slaves:0
master_replid:6bf38f89f5bd68a8d5d0e2b7af58e93a975aecd4
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:14
second_repl_offset:-1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:1
repl_backlog_histlen:14
```


这样就完成了主从复制，读写分离，主服务器负责写入，从节点负责读取；


## 基准测试

基准的测试命令：
redis-benchmark -q -n 100000

```
PING_INLINE: 50890.59 requests per second
PING_BULK: 50226.02 requests per second
SET: 28153.15 requests per second
GET: 49751.24 requests per second
INCR: 27862.91 requests per second
LPUSH: 27270.25 requests per second
RPUSH: 28785.26 requests per second
LPOP: 27233.12 requests per second
RPOP: 27886.22 requests per second
SADD: 50632.91 requests per second
HSET: 27816.41 requests per second
SPOP: 52576.24 requests per second
LPUSH (needed to benchmark LRANGE): 25873.22 requests per second
LRANGE_100 (first 100 elements): 28113.58 requests per second
LRANGE_300 (first 300 elements): 11961.72 requests per second
LRANGE_500 (first 450 elements): 8402.66 requests per second
LRANGE_600 (first 600 elements): 6618.57 requests per second
MSET (10 keys): 31655.59 requests per second
```

这里可以看出，在我的服务器上单机版的redis每秒可以处理5w个请求，而redis的性能最快的配置可以达到12w/s
```
PING_INLINE: 48780.49 requests per second
PING_BULK: 47619.05 requests per second
SET: 48076.93 requests per second
GET: 48192.77 requests per second
INCR: 48780.49 requests per second
LPUSH: 47961.63 requests per second
RPUSH: 47303.69 requests per second
LPOP: 46728.97 requests per second
RPOP: 48661.80 requests per second
SADD: 47778.31 requests per second
HSET: 49261.09 requests per second
SPOP: 47619.05 requests per second
LPUSH (needed to benchmark LRANGE): 48216.01 requests per second
LRANGE_100 (first 100 elements): 27770.06 requests per second
LRANGE_300 (first 300 elements): 12561.24 requests per second
LRANGE_500 (first 450 elements): 8650.52 requests per second
LRANGE_600 (first 600 elements): 6771.86 requests per second
MSET (10 keys): 49358.34 requests per second
```


## 流水线测试  
使用流水线  
默认情况下，每个客户端都是在一个请求完成之后才发送下一个请求（基准会模拟50个客户端除非使用-c指定特别的数量），这意味着服务器几乎是按顺序读取每个客户端的命令。RTT也加入了其中。  
真实世界会更复杂，Redis支持/topics/pipelining，使得可以一次性执行多条命令成为可能。Redis流水线可以提高服务器的TPS  
redis-benchmark -n 1000000 -t set,get -P 16 -q 加入-P选项使用管道技术，一次执行多条命令，每秒处理get/sret请求达到了60/50W