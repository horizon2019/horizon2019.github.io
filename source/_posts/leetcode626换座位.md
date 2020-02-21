---
title: leetcode626换座位
date: 2019-12-10 17:01:28
tags:
category: leetcode
---

1读题：这里需要如果counts数是偶数，则两两交换正好，如果counts数为奇数，那么除了最后一个不需要变动之外，其他都可以与偶数列替换。
这里需要考虑的情况可分为：1.为奇数列（是不是最后一列）2.偶数列


2.如何判断是不是奇偶数
case
      when mod(座位号, 2) != 0  then  '奇数'
      when mod(座位号, 2)  = 0  then  '偶数'
end


3.MYSQL中使用带有简单 CASE 函数的 SELECT 语句使用
```
SELECT  
CASE parent_id 
WHEN 0 THEN   '00'
WHEN 1 THEN  '11'
ELSE  'OTHERS' END AS parent_id_new ,
parent_id ,
type_id ,
type_name
FROM  tdb_goods_types。
```
![](https://www.blog.starmoon.tech/img/leetcode626.png)

4.
 ```
 (SELECT
        COUNT(*) AS counts
    FROM
        seat) AS seat_counts
```
sql答案中这一句理解应该只是提供一个临时变量counts使用

最终答案：
```
SELECT
    (CASE
        WHEN MOD(id, 2) != 0 AND counts != id THEN id + 1
        WHEN MOD(id, 2) != 0 AND counts = id THEN id
        ELSE id - 1
    END) AS id,
    student
FROM
    seat,
    (SELECT
        COUNT(*) AS counts
    FROM
        seat) AS seat_counts
ORDER BY id ASC;
```

### 延伸拓展：leetcode 627.交换工资（update与case结合使用）
```
UPDATE salary
SET
    sex = CASE sex
        WHEN 'm' THEN 'f'
        ELSE 'm'
    END;
```