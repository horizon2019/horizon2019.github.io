---
title: leetcode628三个数最大的乘积
date: 2020-02-10 17:30:52
tags:
---

整型数组包括正整数和负整数

`读题：`
一种情形是三个最大的数相乘,二是两个最小的数相乘再乘以最大的数；两者比较谁大取谁;

```
 public function maximumProduct($nums) {
   //先冒泡排序
      $len = count($nums);
      for ($i = 0; $i < $len - 1; $i++) {
          for ($j = 0; $j < $len - 1 - $i; $j++) {
              if ($nums[$j] > $nums[$j+1]) {
                  $tmp = $nums[$j];
                  $nums[$j] = $nums[$j+1];
                  $nums[$j+1] = $tmp;
              }
          }
      }
      // 第一种情形
      $first=$nums[$len-1]*$nums[$len-2]*$nums[$len-3];
      //第二种情形
      $second=$nums[0]*$nums[1]*$nums[$len-1];
      if($first>$second){
        return $first;
      }else{
        return $second;
      }
    }
    }
```
结果运行超出时间限制；

2.于是尝试使用快速排序递归算法， 但是说输出超出限制
```
 public function test($nums){
      // $nums=[1,-3,2,-4];
      $len = count($nums);
      $v = $nums[0];
      $low = $up = array();
      for ($i = 1; $i < $len; ++$i) {
          if ($nums[$i] > $v) {
              $up[] = $nums[$i];
          } else {
              $low[] = $nums[$i];
          }
      }
      $low = test($low);
      $up = test($up);
  
      $nums= array_merge(test($low), array($v), test($up));
      return $nums;
      // 第一种情形
      $first=$nums[$len-1]*$nums[$len-2]*$nums[$len-3];
      //第二种情形
      $second=$nums[0]*$nums[1]*$nums[$len-1];
      if($first>$second){
        return $first;
      }else{
        return $second;
      }
    }
```

3.php有他内置的排序函数，这里复习一下
```
 function maximumProduct($nums) {
      $len = count($nums);
      sort($nums);
  
      // 第一种情形
      $first=$nums[$len-1]*$nums[$len-2]*$nums[$len-3];
      //第二种情形
      $second=$nums[0]*$nums[1]*$nums[$len-1];
      if($first>$second){
        return $first;
      }else{
        return $second;
      }
    }
```
`索引数组`
sort() - 以升序对数组排序
rsort() - 以降序对数组排序
`关联数组`
asort() - 根据值，以升序对关联数组进行排序
ksort() - 根据键，以升序对关联数组进行排序
arsort() - 根据值，以降序对关联数组进行排序
krsort() - 根据键，以降序对关联数组进行排序
