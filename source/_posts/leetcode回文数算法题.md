---
title: leetcode回文数算法题
date: 2019-02-23 13:38:26
tags:
---

1.题目：判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。
示例 1:
输入: 121
输出: true

示例 2:
输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```

class Solution {

    /**
     * @param Integer $x
     * @return Boolean
     */
    function isPalindrome($x) {

        if($x<0){
            return false;
        }
        $result=strval($x);
        if(strrev($result)==$result){
            return true;
        }else{
            return false;
        }
    }
}
```

2.给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。
示例 1：
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。

示例 2：
输入: "cbbd"
输出: "bb"

```
class Solution 
{
    /**
     * @param String $s
     * @return String
     */
    function longestPalindrome($s) 
    {
        $s_length = strlen($s);
        if ($s_length < 1) {
            return '';
        } elseif ($s_length <= 2) {
            return $s{0} == $s{1} ? $s : $s{0}; 
        }
        
        $start = 0;
        $offset = 0;
        
        for ($i = 0; $i < $s_length; $i ++) {
            
            if ($offset/2 > $s_length - $i) {
                break;
            }
            
            $len1 = $this->around($s, $s_length, $i, $i);
            $len2 = $this->around($s, $s_length, $i, $i + 1);
            
            if ($len1 > $len2) {
                if ($len1 > $offset) {
                    $start = $i - ($len1 - 1)/2;
                    $offset = $len1;
                }
            } else {
                if ($len2 > $offset) {
                    $start = $i - $len2/2 + 1;
                    $offset = $len2;
                }
            }
        }
        
        return substr($s, $start, $offset);
    }
    
    function around($s, $s_length, $left, $right)
    {
        while ($left >= 0 && $right < $s_length && $s{$left} == $s{$right}) {
            $left --;
            $right ++;
        }
        
        return $right - $left - 1;
    }
}


```