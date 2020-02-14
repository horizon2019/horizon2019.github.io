---
title: php设计模式
date: 2020-02-14 14:59:51
tags:
---


php设计模式可以分为三类：创建性模式，结构性模式，行为性模式

创建性模式：单例模式、简单工厂模式、工厂方法模式、抽象工厂模式、对象池模式、原型模式

结构性模式：适配器模式、桥接模式、组合模式、装饰器模式、依赖注入、门面模式、链式操作、代理模式、注册器模式

行为性模式：观察者模式、责任链模式、模板方法、策略模式、访问者模式、遍历模式、空对象模式、状态模式、命令模式

五种常见设计模式：
单例模式、工厂模式、注册器模式、适配器模式、观察者模式


### 单例模式(singleton)
有三个特点
1、一个类只能有一个实例
2、它必须自行创建这个实例
3、它必须自行向整个系统提供这个实例
```
<?php
class Singleton
{
    private static $instance;
    private function _construct(){}
    private function _clone(){}
    public static function getInstance()
    {
        if(is_null(self::$instance)) {
            self::$instance = new self();
        }
        return self::$instance;
    }
}

/**
 * 客户端
 */
class Client {
 
  /**
  * Main program.
  */
 public static function main() {
  $instance = Singleton::getInstance();
  $instance->test();
 }
}
 
Client::main();

```
### 工厂类模式：

**简单工厂模式（Simple Factory）**
本来我们要获取一个类的实例，需要用到new关键字。但是如果new 直接写到业务代码里，一个类在很多地方都实例化过，以后要是这个类出了什么问题，比如要改个名字(实际中，你更多的可能是修改构造函数方法)，那么就尴尬了，需要改很多地方。工厂模式，顾名思义，就是不用new来获得实例，而是把业务类放进一个工场类里，由工厂（类）『生产』出对应的实例。
```
<?php
namespace DesignPatterns\Creational\SimpleFactory;
class SimpleFactory
{
    public function createBicycle(): Bicycle
    {
        return new Bicycle();
    }
}
```
```
<?php
namespace DesignPatterns\Creational\SimpleFactory;
class Bicycle
{
    public function driveTo(string $destination)
    {
    }
}
```


```
$factory = new SimpleFactory();
 $bicycle = $factory->createBicycle();
 $bicycle->driveTo('Paris');

```
**工厂方法模式 和 简单工厂模式非常接近，唯一不同的是，允许有多个工厂存在，相当于给工厂分组。**
1.每个工厂必须继承一个抽象类或接口类, 使之成为多态。
2.每个产品也必须继承一个抽象类或接口类,也使之成为多态。
3.每个工厂必须有一个工厂方法返回产品的实例。

```
/* 工厂和产品接口 */
interface CarFactory 
{
    public function makeCar();
}
interface Car 
{
    public function getType();
}
/* 工厂和产品实现 */
/*继承了接口就要实现里面的方法*/
class SedanFactory implements CarFactory 
{
    public function makeCar() 
    {
        return new Sedan();
    }
}
class Sedan implements Car 
{
    public function getType() 
    {
        return 'Sedan';
    }
}
/* 客户端 */
$factory = new SedanFactory();
$car = $factory->makeCar();
print $car->getType();

```

### 注册器模式
注册器模式是一种基础常见的设计模式，它的主要意思是把多个类的实例注册到一个注册器类中去，然后需要哪个类，由这个注册器类统一调取。
```
class Register{
 protected static $objects;
 public static function set($alias,$object){
     if(!isset($objects[$alias])){
         self::$objects[$alias]=$object;
     }
 }
 public static function get($alias){
  return self::$objects[$alias];
 }
 public static function _unset($alias){
  unset(self::$objects[$alias]);
 }
}
```
```
Register::set('rand',stdClass::class);
Register::get('rand');
```

就是把很多类的实例，起个别名，然后按照key，value的形式放在注册器类里，以便之后统一调用。
Laravel的Service Container，对，容器本质上也是种注册器，但laravel中的实现要比这个例子复杂得多.

### 适配器模式
正常接口的实现：
```
interface Database{
    public function connect();
    public function query();
    public function close();
}
class Mysql implements Database{
    public function connect(){
        //mysql 的逻辑
    };
    public function query(){
        //mysql 的逻辑
    };
    public function close(){
        //mysql 的逻辑
    };
}
class Pdo implements Database{
    public function connect(){
        //Pdo 的逻辑
    };
    public function query(){
        //Pdo 的逻辑
    };
    public function close(){
        //Pdo 的逻辑
    };
}
//使用
$database = new Mysql(); //切换数据库只要改这一行就行了，因为后面的都是标准接口方法，不管哪个数据库都一样。
$database->connect();
$database->query();
$database->close();
```
issue:有些第三方的 数据库类并没有按照我的接口来实现，而是有自己不同的方法，这个时候我们就需要有一个适配器类，来先处理一下这个异类。 作用有点像把110v电源转换成为220v（电源适配器）。
```
//第三方数据库类
class Oracle {
    public function oracleConnect(){
        //Oracle 的逻辑
    };
    public function oracleQuery(){
        //Oracle 的逻辑
    };
    public function oracleClose(){
        //Oracle 的逻辑
    };
}

```

solve:
```
class Adapter implements Database {  
    private $adaptee;  
    function __construct($adaptee) {  
        $this->adaptee = $adaptee;   
    } 
    //这里把异类的方法转换成了 接口标准方法，下同
    public function connect(){
        $this->adaptee->oracleConnect();
    };
    public function query(){
        $this->adaptee->oracleQuery();
    };
    public function close(){
        $this->adaptee->oracleClose();
    };
}

    $adaptee = new Oracle();  
    $adapter = new Adapter($adaptee);//只要改这个类就行了，后面的都可以不用改；
    $database = $adapter;
    $database->connect();
    $database->query();
    $database->close();
```

### 观察者模式
观察者是一种非常常用的模式，具体在 事件的设计上 体现最明显。
在laravel的事件设计中，我们知道有一个listener 和一个handler, 当listener侦听到一个事件发生时，可能有多个handler会与之对应自动处理各自的业务逻辑。
```
//声明一个抽象的事件发生者基类
abstract class EventGenerator{
    private $observers = array();
    //添加观察者方法
    function addobserver(Observer $observer)
    {
        $this->observers[] = $observer;
    }
    //对每个添加的观察者进行事件通知
    function notify()
    {
       //对每个观察者逐个去更新
       foreach($this->observers as $observer)
       {
         $observer->update();
       }
    }
}
```

```
//声明一个观察者接口
interface observer
{
   function update($event_info = null);
}
```
```
//声明具体事件类，继承了主事件
class Event extends EventGenerator
{
   function trigger()
   {
      echo "Event<br/>";
      $this->notify();
   }
}
```

参考文章：http://larabase.com/collection/5/post/143
