---
title: leetcode601体育馆的流量
date: 2020-02-11 14:50:33
category: leetcode
tags:
---

1.and (里面可以加上or条件)
2.临时表
我们要取出t1,t1的顺序只可能是以下情况
t1>t2>t3
t2>t1>t3
t2>t3>t1
```
# Write your MySQL query statement below

select distinct t1.*
from stadium t1, stadium t2, stadium t3
where t1.people >= 100 and t2.people >= 100 and t3.people >= 100
and
(
    (t1.id - t2.id = 1 and t1.id - t3.id = 2 and t2.id - t3.id =1) 
    or
    (t2.id - t1.id = 1 and t2.id - t3.id = 2 and t1.id - t3.id =1) 
    or
    (t3.id - t2.id = 1 and t2.id - t1.id =1 and t3.id - t1.id = 2)
)
order by t1.id;
```


