---
title: php中魔术方法
date: 2017-05-14 20:04:00
tags:
category: php基础
---

魔术方法：

__construct() :实例化对象时被调用；

__destuct()：当删除一个对象或者对象操作终止是被执行；

__call()：调用对象不存在方法时被调用；

__get()：调用对象不存在的属性时被调用；

__set()：设置对象不存在的属性时被调用；

__toString()：打印一个对象时被调用，比如echo obj,print(obj,print(obj);

__clone():克隆对象时被调用，比如t=newTest();t=newTest();tt = clone $t;

__sleep():serialize之前被调用，若对象比较大，想做一些删除在序列化，可以考虑使用该方法；

__wakeup()：unserialize之前被调用，做些对象的初始化；

__isset()：检测对象是否存在属性的时候被调用，如 isset($c->name)；

__unset():unset一个对象属性时被调用，如：unset($c->name);

__set_state()：调用var_export时被调用，用__set_state的返回值作为 var_export的返回值；

__autoload()：实例化一个对象时，如果对应的类不存在，在该方法被调用。

__invoke()，调用函数的方式调用一个对象时的回应方法
 

魔术常量：

__LINE__：返回当前行号；

__FILE__：返回文件的完整路径和文件名，如果用在包含文件里面，则返回包含文件名，自 php4.0.2后，__FILE__总是包含一个绝对路径，而在此前的版本有时候会包含一个相对路径；

__FUNCTION__：返回函数名称（自 php4.3.0新加的）。自php5起本常量返回该函数被定义时的名称，区分大小写，在php4中该值总是小写；

__CLASS__：返回类的名称，自 php4.3.0新加的，自php5起本常量返回该类被定义时的名称，区分大小写，在php4中该值总是小写的；

__METHOD__：返回类的方法名。 php5新加的