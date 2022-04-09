---
title: leetcode633平方数之和
date: 2018-12-11 19:36:16
category: leetcode
language: zh-Hans
tags: 二分查找
---

给定一个非负整数 c ，你要判断是否存在两个整数 a 和 b，使得 a2 + b2 = c。
读题：非负整数包含0;

**思路：**我们可以首先枚举 a，（a的范围应该是小于c的），并保证 c - a^2 >= 0，随后我们通过二分查找的方法找出是否存在 b，满足 b^2 = c - a^2。二分查找的范围为 [0, c - a^2]。

**复习一下php的二分查找：**
二分查找的数组必须是排好序的，顺序查找没要求，php查找数组元素有内置的函数array_search和in_array
```
  //二分查找法（非递归方法）
  public function binSearch2(\Illuminate\Http\Request $request){
      $search=$request->search;
      $arr=array(1,2,3,4);
      $height=count($arr)-1;
      $low=0;
      while($low<=$height){
        //floor() 函数向下舍入为最接近的整数。
          $middle=floor(($low+$height)/2);
          if($arr[$middle]==$search){
            return $middle;
          }elseif($arr[$middle]<$search){
            $low=$middle+1;
          }elseif($arr[$middle]>$search){
            $height=$middle-1;
          }
      }
      //数组不存在,或者没有此数都算查找失败
      return '查找失败';
    } 
```
//二分查找法(递归方法)
```
  //二分查找法（递归法）
  public function binSearch2($arr,$low,$height,$search){
      while($low<=$height){
        //floor() 函数向下舍入为最接近的整数。
          $middle=floor(($low+$height)/2);
          if($arr[$middle]==$search){
            return $middle;
          }elseif($arr[$middle]<$search){
            return binSearch2($arr,$middle+1,$height,$search);
          }elseif($arr[$middle]>$search){
            return binSearch($arr,$low,$middle-1,$search);
          }
      }
      //数组不存在,或者没有此数都算查找失败
      return '查找失败';
    } 


    //顺序查找
function seqSearch($arr,$k){
  foreach($arr as $key=>$val){
    if($val==$k){
      return $key;
    }
  }
  return -1;
}

```

**看到java的解法：**
```
java解法一：
public class Solution {
    public boolean judgeSquareSum(int c) {
        for (long a = 0; a * a <= c; a++) {
            int b = c - (int)(a * a);
            if (binary_search(0, b, b))
                return true;
        }
        return false;
    }
    public boolean binary_search(long s, long e, int n) {
        if (s > e)
            return false;
        long mid = s + (e - s) / 2;
        if (mid * mid == n)
            return true;
        if (mid * mid > n)
            return binary_search(s, mid - 1, n);
        return binary_search(mid + 1, e, n);
    }
}
```
```
java解法二：public class Solution {
    public boolean judgeSquareSum(int c) {
        for (long a = 0; a * a <= c; a++) {
            double b = Math.sqrt(c - a * a);
            if (b == (int) b)
                return true;
        }
        return false;
    }
}
```
**php解法一：**
```
class Solution {

    /**
     * @param Integer $c
     * @return Boolean
     */
    function judgeSquareSum($c) {
        if($c==0){
            return true;
        }
        for($i=0;$i*$i<$c;$i++){
        $b=$c-$i*$i;
        if($this->binSearch2(0,$b,$b)){
            return true;
        }
        }
        return false;
    }

     //二分查找法（非递归方法）
    public function binSearch2($low,$height,$search){
       if($low>$height){
        return false;
       }
 
        //floor() 函数向下舍入为最接近的整数。
        $middle=intval($low+($height-$low)/2);
        if($middle*$middle==$search){
          return true;
        }
        if($middle*$middle>$search){
          return $this->binSearch2($low,$middle-1,$search);
        }else{
          return $this->binSearch2($middle+1,$height,$search);
        }
      
      //数组不存在,或者没有此数都算查找失败
      return true;
    } 
}
```
**php解法二：**
```
class Solution {

    /**
     * @param Integer $c
     * @return Boolean
     */
    function judgeSquareSum($c) {
                $l=0;
        $r=floor(sqrt($c));
        while($l<=$r){
            if($c-$l*$l==$r*$r){
                return true;
            }elseif($c-$l*$l>$r*$r){
//浮动的范围在[l,r]之间，只能l递增或者r递减
                $l++;
            }elseif($c-$l*$l<$r*$r){
                $r--;
            }
        }

        return false;
    } 
}
```
**ps:**
```
如果这样写就超出时间限制：因为这种解法是错误的,
//浮动的范围在[l,r]之间，只能l递增或者r递减

class Solution {

    /**
     * @param Integer $c
     * @return Boolean
     */
    function judgeSquareSum($c) {
               $l=0;
        $r=floor(sqrt($c));
        while($l<=$r){
            if($c-$l*$l==$r*$r){
                return true;
            }elseif($c-$l*$l>$r*$r){
                $r++;
            }elseif($c-$l*$l<$r*$r){
                $l--;
            }
        }

        return false;
    } 
}
```
