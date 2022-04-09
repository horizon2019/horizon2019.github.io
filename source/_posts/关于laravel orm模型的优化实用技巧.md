---
<<<<<<< HEAD:source/_posts/关于laravel orm模型的优化实用技巧.md
title: 关于laravel orm模型的优化实用技巧
date: 2019-12-15 14:36:35
=======
title: laravel orm 优化实用技巧
date: 2018-12-15 14:36:35
>>>>>>> f425fe099f2175e43cc0104e0c90a9d6ca472170:source/_posts/laravel-orm-优化实用技巧.md
tags: orm模型
category: laravel
---

### 1.如果要实现某个整型字段的递增或者递减

```
Article::find($article_id)->increment('read_count');
Article::find($article_id)->increment('read_count',10);
Article::find($article_id)->decrement('read_count');
```

代替

```
$article = Article::find($article_id);
$article->read_count++;
$article->save();
```

### 2.先执行 X 方法，X 方法执行不成功则执行 Y 方法

```
$user = User::findOrFail($id);
```

代替

```
$user = User::find($id);
if (!$user) { abort (404); }
```

### 3.在一个 Eloquent 模型中，有个神奇的地方，叫 boot()，用它可以覆盖默认的行为：

```
class User extends Model
{
    public static function boot()
    {
        parent::boot();
        static::updating(function($model)
        {
            // 写点日志啥的
            // 覆盖一些属性，类似这样 $model->something = transform($something);
        });
    }
}
```

### 4. 带条件与排序的关联关系

```
public function approvedUsers() {
    return $this->hasMany('App\User')->where('approved', 1)->orderBy('email');
}
```

### 5.通过 ID 查询多条记录

```
$users = User::find([1,2,3]);
```

这个方法可以接受多个 ID 的数组作为参数.

### 6.WhereX

```
$users = User::whereApproved(1)->get();
```

代替

```
$users = User::where('approved', 1)->get();
```

### 7.一对多返回默认模型对象

```
public function author()
{
    return $this->belongsTo('App\Author')->withDefault([
        'name' => 'Guest Author'
    ]);
}
```

### 8.复制：复制一行的副本

```
$task = Tasks::find(1);
$newTask = $task->replicate();
$newTask->save();
```

### 9.php artisan make:model Company -mcr

-m 将创建一个迁移文件

-c 将创建一个控制器

-r 表示控制器应该是一个资源控制器

### 10. update () 的结果是什么？

```
$result 会包含受影响的行。 因此如果你想检查多少行受影响， 你不需要额外调用其他任何内容 -- update() 方法会给你返回此数字。

```

### 补充 11.find()、findOrFail()、first()、firstOrFail()、get()、list()、toArray()的区别是什么？

find(\$id) takes an id and returns a single model. If no matching model exist, it returns null.

findOrFail(\$id) takes an id and returns a single model. If no matching model exist, it throws an error1.

first() returns the first record found in the database. If no matching model exist, it returns null.

firstOrFail() returns the first record found in the database. If no matching model exist, it throws an error1.

get() returns a collection of models matching the query.

pluck(\$column) returns a collection of just the values in the given column. In previous versions of Laravel this method was called lists.

toArray() converts the model/collection into a simple PHP array.
