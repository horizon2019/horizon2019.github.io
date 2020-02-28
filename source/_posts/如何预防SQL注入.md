---
title: 如何预防SQL注入？
date: 2019-09-12 22:32:22
tags: mysql
category: web安全
---

## sql注入
SQL注入攻击指的是通过构建特殊的输入作为参数传入Web应用程序，而这些输入大都是SQL语法里的一些组合，通过执行SQL语句进而执行攻击者所要的操作，其主要原因是程序没有细致地过滤用户输入的数据，致使非法数据侵入系统。
根据相关技术原理，SQL注入可以分为平台层注入和代码层注入。前者由不安全的数据库配置或数据库平台的漏洞所致；后者主要是由于程序员对输入未进行细致地过滤，从而执行了非法的数据查询。
基于此，SQL注入的产生原因通常表现在以下几方面：
①不当的类型处理；
②不安全的数据库配置；
③不合理的查询集处理；
④不当的错误处理；
⑤转义字符处理不合适；
⑥多个提交处理不当。

## 解决sql注入原理
在知道了sql注入的原理之后，我们同样也了解到mysql有预编译的功能，指的是在服务器启动时，mysql client把sql语句的模板（变量采用占位符进行占位）发送给mysql服务器，mysql服务器对sql语句的模板进行编译，编译之后根据语句的优化分析对相应的索引进行优化，在最终绑定参数时把相应的参数传送给mysql服务器，直接进行执行，节省了sql查询时间，以及mysql服务器的资源，达到一次编译、多次执行的目的，除此之外，还可以防止SQL注入。
具体是怎样防止SQL注入的呢？实际上当将绑定的参数传到mysql服务器，mysql服务器对参数进行编译，即填充到相应的占位符的过程中，做了转义操作。

## PDO是如何预防sql注入的
我们使用传统的 mysql_connect 、mysql_query方法来连接查询数据库时，如果过滤不严，就有SQL注入风险，导致网站被攻击，失去控制。虽然可以用mysql_real_escape_string()函数过滤用户提交的值，但是也有缺陷。而使用PHP的PDO扩展的 prepare 方法，就可以避免sql injection 风险。
使用PDO访问MySQL数据库时，真正的real prepared statements 默认情况下是不使用的。为了解决这个问题，你必须禁用 prepared statements的仿真效果。下面是使用PDO创建链接的例子：
```
$dbh = new PDO('mysql:dbname=dbtest;host=127.0.0.1;charset=utf8', 'user', 'pass');
$dbh->setAttribute(PDO::ATTR_EMULATE_PREPARES, false);
```
setAttribute（）这一行是强制性的，它会告诉 PDO 禁用模拟预处理语句，并使用 real parepared statements 。这可以确保SQL语句和相应的值在传递到mysql服务器之前是不会被PHP解析的（禁止了所有可能的恶意SQL注入攻击）。
虽然你可以配置文件中设置字符集的属性(charset=utf8)，但是需要格外注意的是，老版本的 PHP（ < 5.3.6）在DSN中是忽略字符参数的。

```
$dbh = new PDO("mysql:host=localhost; dbname=demo", "user", "pass");
$dbh->setAttribute(PDO::ATTR_EMULATE_PREPARES, false); //禁用prepared statements的仿真效果
$dbh->exec("set names 'utf8'"); 
$sql="select * from test where name = ? and password = ?";
$stmt = $dbh->prepare($sql); 
$exeres = $stmt->execute(array($testname, $pass)); 
if ($exeres) { 
while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
    print_r($row);
}
}
$dbh = null;
```
上面这段代码就可以防范sql注入。为什么呢？
当调用 prepare() 时，查询语句已经发送给了数据库服务器，此时只有占位符 ? 发送过去，没有用户提交的数据；当调用到 execute()时，用户提交过来的值才会传送给数据库，他们是分开传送的，两者独立的，SQL攻击者没有一点机会。
但是我们需要注意的是以下几种情况，PDO并不能帮助你防范SQL注入
1、你不能让占位符 ? 代替一组值，如：
SELECT * FROM blog WHERE userid IN ( ? );
2、你不能让占位符代替数据表名或列名，如：
SELECT * FROM blog ORDER BY ?;
3、你不能让占位符 ? 代替任何其他SQL语法，如：
SELECT EXTRACT( ? FROM datetime_column) AS variable_datetime_element FROM blog;


## 如何预防sql注入？
1.永远不要信任用户的输入。对用户的输入进行校验，可以通过正则表达式，或限制长度；对单引号和双"-"进行转换等；
2.永远不要使用动态拼装sql，可以使用参数化的sql或者直接使用存储过程进行数据查询存取；
3.永远不要使用管理员权限的数据库连接，为每个应用使用单独的权限有限的数据库连接；
4.不要把机密信息直接存放，加密或者hash掉密码和敏感的信息；
5.应用的异常信息应该给出尽可能少的提示，最好使用自定义的错误信息对原始错误信息进行包装；
6.sql注入的检测方法一般采取辅助软件或网站平台来检测，软件一般采用sql注入检测工具jsky，网站平台就有亿思网站安全平台检测工具。MDCSOFT SCAN等。采用MDCSOFT-IPS可以有效的防御SQL注入，XSS攻击等。