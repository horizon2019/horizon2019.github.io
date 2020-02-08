---
title: leetcode179算法题解
date: 2020-02-08 15:51:33
tags:
category: leetcode
---


1.如果s1.s2>s2.s1那么s1就应该排在s2前面。实际为一道排序题，排序规则如此。
2.可以采用冒泡排序


``` 
class Solution {

    /**
     * @param Integer[] $nums
     * @return String
     */
    function largestNumber($nums) {
         for($i=0;$i<count($nums)-1;$i++){
        for($j=$i;$j<count($nums);$j++){
          $first=intval($nums[$i].$nums[$j]);
          $second=intval($nums[$j].$nums[$i]);
          $item=$nums[$j];
          if($first<$second){
            $item=$nums[$j];
            $nums[$j]=$nums[$i];
            $nums[$i]=$item;
          } 
        }
      
      }
  
      $nums = implode("", $nums);
      if($nums==0){
        return '0';
      }
      
      return $nums;
    
    }
}
```