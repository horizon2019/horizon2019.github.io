---
title: rabc权限控制系统总结
date: 2020-02-06 16:20:57
tags:
---

rbac 权限控制是一个非常成熟的权限控制系统，其原理是给每个用户一个或多个角色 而每个角色对系统相应模块有访问权限。RBAC（Role-Based Access Control）模型
RBAC认为权限授权的过程可以抽象地概括为：Who是否可以对What进行How的访问操作，在RBAC模型里面，有3个基础组成部分，分别是：用户、角色和权限。
RBAC 模型可以分为：RBAC0、RBAC1、RBAC2、RBAC3 四种。其中 RBAC0 是基础，其它三种都是在 RBAC0 基础上的变种。大部分情况下，使用 RBAC0 模型就可以满足常规的权限管理系统设计了。不过一定不要拘泥于模型，要以业务需要为先导。接下来简单对四种模型进行简单的介绍一下。
RBAC的优缺点
优点：简化了用户和权限的关系，易扩展、易维护
缺点：RBAC模型没有提供操作顺序的控制机制，这一缺陷使得RBAC模型很难适应哪些对操作次序有严格要求的系统
![](https://www.blog.starmoon.tech/img/rbac1.png)


总共5张表，user,role,user_role,permission,role_permission，用户和角色是多对多关系，角色和权限也是多对多关系。当用户访问某个action的时候，检查user对应的role包含的permission中是否含有这个action。对于laravel框架而言现在也有很多laravel rbac的权限拓展包，laravel/permission等等可以实现

![](https://www.blog.starmoon.tech/img/rbac2.png)
这里以yii框架为例：
1.写一个BaseadminController放在component文件夹下面，BaseadminController继承自Controller，其他的controller又继承BaseadminController.
BaseadminController要做的工作有：
1.里面以数组的方式存放所有的
 ```
   public $Menu = array(
        'site' => [
            'title' => '首页管理',
            'icon' => 'fa fa-desktop',
            'actions' => [
                'index' => '首页',
                'findcode' => '查询验证码',
            ]
        ],
   ...
）
```
的菜单目录，这些是针对管理员的，因为管理拥有所有的目录权限。其次前端根据数组结构显示相应的页面；


2.BaseadminController里面beforeaction方法里面根据用户传入的role和id判断用户是否拥有该路由访问权限，如果没有重定向到相关页面，并且提示联系管理员开通权限；有的话则进入相关页面
   ```
        if (!\Yii::$app->admin->isGuest  ) {
            //判断权限
            //略
             {
                $homeurl = Url::toRoute('site/index');
                die("{$action->id}无权限访问，请联系管理员！<a href='" . $homeurl . "'>返回</a>");
            }
        }

  ```
3.对于管理员而言，可以编辑添加角色，同时可以给角色添加相应的权限。
可以单独另写一个AdminController,在里面editrole,addrole做相关操作