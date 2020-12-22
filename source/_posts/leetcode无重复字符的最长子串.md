---
title: leetcode无重复字符的最长子串
date: 2019-12-11 23:39:44
tags:
category: leetcode
---

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

**php解法如下：**
 ```
 public function lengthOfLongestSubstring($s) {
      $strLength=strlen($s);
      if($strLength<=1){
        return $strLength;
      }else{
        $subStrlen = [];
        for($i=0;$i<$strLength;$i++){
          $strArr=[];
          $strArr[]=$s[$i];
          for($j=$i+1;$j<$strLength;$j++){
              $strArr[]=$s[$j];
              if(count(array_unique($strArr))!=count($strArr)){
                array_pop($strArr);
                 break;
              }
          }
        $subStrlen=count($strArr)>count($subStrlen)?$strArr:$subStrlen;
        }  
        return count($subStrlen);
      }
      
}

```
![](http://119.29.18.20/img/draft.png)
**思路：**
比较它的子字符串最大的长度，子字符串中要求没有重复元素，用双层循环找出它的子字符串。
**array_unique以及array_pop的用法:**
array_unique — 移除数组中重复的值
注意键名保留不变。array_unique() 先将值作为字符串排序，然后对每个值只保留第一个遇到的键名，接着忽略所有后面的键名。这并不意味着在未排序的 array 中同一个值的第一个出现的键名会被保留。
```
<?php
$input = array("a" => "green", "red", "b" => "green", "blue", "red");
$result = array_unique($input);
print_r($result);
?>
```
会输出：
```
Array
(
    [a] => green
    [0] => red
    [1] => blue
)
```

array_pop — 弹出数组最后一个单元（出栈）array_pop ( array &$array ) : mixed
array_pop() 弹出并返回 array 数组的最后一个单元，并将数组 array 的长度减一。