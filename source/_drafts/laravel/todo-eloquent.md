---
title: Eloquent ORM『数据库交互』
date: 2018-12-22 10:43:39
categories: Laravel
tags: [数据库交互]
---

{% fullimage /uploads/laravel/eloquent/eloquent.jpg %}

<!-- more -->

## CURD

### 创建『Create』

```php
Model::create([data]);	# 单个数据插入
Model::insert([data]);	# 批量数据插入
```

当写入的值不在 `$fillable` 中，需要使用 `save`，因为 `save` 不会触发 `$fillable`。

```php
$topic->fill($request->all());
$topic->user_id = Auth::id();
$topic->save();
```

### 更新『Update』

`update()`

```php
// 更为常用
$data = Model::first();
$data->update([data]);
```

`save()` 

```php
$data = Model::first();
$data->field = 'value';
$data->save();
```

`update` 是属于批量修改，所改字段必须存在于 `fillable` 属性。

`save` 是创建或修改一条数据，字段不必存在 `fillable` 属性里面。


### 读取『Retrieve』

```php
Model::find(1);	// 查找指定id数据
Model::findOrFail(1);	// 查找指定id数据，未找到抛出404异常
Model::first();	// 查找第一条数据
Model::all();	// 查找全部数据
```

### 删除『Delete』

```php
$topic->delete();
```

## 查询构造器

### whereIn

## 预加载

预加载用于避免 N+1 问题 [查看文档](https://learnku.com/docs/laravel/6.x/eloquent-relationships/5177#eager-loading)

```php
$topics = Topic::with('user', 'category')->paginate(30);
```

## 数据分页

查找数据

```php
// 每页10条数据
$users = User::paginate(10);
return view('users.index', compact('users'));
```

渲染分页

```php
{!! $users->render() !!}
{!! $topics->appends(Request::except('page'))->render() !!}
{{ $replies->appends(['tab' => 'replies'])->render() }} 
```

## 排序

### orderBy

根据指定字段排序

```php
->orderBy('updated_at', 'desc');
```

## 分组

### groupBy



### 常见错误

#### 1055

错误代码

```
Illuminate\Database\QueryException
SQLSTATE[42000]: Syntax error or access violation: 1055 Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'peach.diaries.id' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by (SQL: select * from `diaries` where `diaries`.`user_id` = 1 and `diaries`.`user_id` is not null and `date` between 2019-01-01 00:00:00 and 2019-12-31 23:59:59 group by `date` order by `date` desc)
```

错误原因

MySQL5.7之后默认开启了严格模式

解决方法1

获取结果集合后使用集合方法分组

```php
$diaries = Auth::user()
    ->diaries()
    ->with('diary_category')
    ->whereBetween('date', [$from, $to])
    ->orderBy('date', 'desc')
    ->get()
    ->groupBy(function($date) {
        return Carbon::parse($date->created_at)->format('Y-m');
    });
```

解决方法2

`config/database.php`

```php
'connections' => [
    'mysql' => [
        'strict' => false,
    ],
```







## 事件监听

`app/Models/User.php`

```php
public static function boot()
{
    parent::boot();

    static::creating(function($user) {
        ...
    });
}
```

## 关联模型

### 一对一

```php
// 一条数据只可属于一个用户
public function user()
{
    return $this->belongsTo(User::class);
}
```

### 一对多

```php
// 一个用户可拥有多条数据
public function statuses()
{
    return $this->hasMany(Status::class);
}
```

### 关联写入

```php
// 创建数据时会自动关联用户
$user->statuses()->create();
```

### 关联取值

```php
$user->statuses();	// 返回 Eloquent集合
$user->statuses;	// 返回 数据库请求构建器
```

> `$user->statuses` 与 `$user->statuses()` 的区别在于以属性访问时会自动调用get()，以方法形式调用时是返回一个查询构建器，后面还可以继续调用其他的查询构建器。


### 多对多

```php
// 粉丝列表
public function followers()
{
    // followers 自定义关联表名
    // user_id 定义在关联中的模型外键名
    // follower_is 要合并的模型外键名
    return $this->belongsToMany(User::class, 'followers', 'user_id', 'follower_id');
}

// 关注列表
public function followings()
{
    return $this->belongsToMany(User::class, 'followers', 'follower_id', 'user_id');
}


// belongsToMany() 方法用于定义一个多对多的关联，第一个参数是关联的模型类名，第二个参数是中间表的表名。
// withTimestamps() 代表中间表带有时间戳字段。
// orderBy('user_favorite_products.created_at', 'desc') 代表默认的排序方式是根据中间表的创建时间倒序排序。
public function favoriteProducts()
{
    return $this->belongsToMany(Product::class, 'user_favorite_products')
        ->withTimestamps()
        ->orderBy('user_favorite_products.created_at', 'desc');
}
```

### 关联动作

在多对多关联中，可以使用`attach` 、`sync` 创建记录，`detach` 移除记录，这些操作并不会影响到两个模型各自的数据，所有的数据变动都在中间表上进行。

`attach`

```php
// 关注 user_id 为1和2的用户
$this->followings()->attach([1, 2]);
```

使用 `attach` 可能会创建重复数据，为了解决这个问题可以使用 `sync`

`sync`

```php
// 关注用户，不重复创建数据，保留之前的关注关系
public function follow($user_ids)
{
    if (!is_array($user_ids)) {
        $user_ids = compact('user_ids');
    }
    // 第二个参数指明是否要移除其它不包含在关联的 id 数组中的 id
    // true 表示移除，false 表示不移除，默认值为 true
    $this->followings()->sync($user_ids, false);
}
```

`detach`

```php
// 取消关注
public function unfollow($user_ids)
{
    if (!is_array($user_ids)) {
        $user_ids = compact('user_ids');
    }
    $this->followings()->detach($user_ids);
}
```

`contains` 

```php
// 判断当前登录的用户 A 是否关注了用户 B
public function isFollowing($user_id)
{
    // 判断用户 B 是否包含在用户 A 的关注人列表上
    return $this->followings->contains($user_id);
}
```

获取关联模型的 ID 集合

```php
// 查看关注的用户
// allRelatedIds 是 Eloquent 关联关系提供的 API，用来获取关联模型的 ID 集合
$user->followings()->allRelatedIds()->toArray();
```

## 集合操作

### 获取第一条数据

```php
$users->first();
```

### 移除指定数据

```php
$users->slice(1);
```

### 获取指定字段

```php
$users->pluck('id')->toArray();
```

## 本地作用域

[本地作用域](https://learnku.com/docs/laravel/6.x/eloquent/5176#local-scopes)总是返回查询构建器。

```php
public function scopeWithOrder($query, $order)
{
    switch ($order) {
        case 'recent':
            $query->recent();
            break;

        default:
            $query->recentReplied();
            break;
    }
    return $query->with('user', 'category');
}

public function scopeRecentReplied($query)
{
    return $query->orderBy('updated_at', 'desc');
}

public function scopeRecent($query)
{
    return $query->orderBy('created_at', 'desc');
}
```

调用时不需要加上 `scope`

```php
$topic->withOrder($request->order)->paginate(20);
```







## 修改器

[Eloquent 修改器](https://learnku.com/docs/laravel/6.x/eloquent-mutators) 

## 访问器

### 创建

```php
// 拼接完整地址
public function getFullAddressAttribute()
{
    return "{$this->province}{$this->city}{$this->district}{$this->address}";
}
```

### 取值

```php
// 输出完整地址
$model->full_address;
```





https://learnku.com/docs/laravel/6.x/eloquent-mutators/5179#defining-an-accessor 

## 方法

### 传参模型填充

```php
public function store(TopicRequest $request, Topic $topic)
{
    $topic->fill($request->all());
}
```

### 取部分字段

```php
User::all()->pluck('id')->toArray();
```

### 显示隐藏字段

确保数据入库时不会报错

```php
$user->makeVisible(['column_name1', 'column_name2']);
```



## 未分类

### +1

```php
$reply->topic->increment('reply_count', 1);
```