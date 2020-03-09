---
title: Laravel 数据库迁移『Migrations』
date: 2019-11-13 16:43:59
categories: Laravel
---

# 创建迁移

## 创建新表

```bash
$ php artisan make:migration create_users_table --create=users
```

## 修改旧表

```bash
$ php artisan make:migration add_votes_to_users_table --table=users
```

## 指定路径

```bash
$ php artisan make:migration create_users_table --path=xxxxxx
```

<!-- more -->

# 运行迁移

## 执行迁移

```bash
$ php artisan migrate
$ php artisan migrate --force # 强制执行
```

## 回滚迁移

```bash
$ php artisan migrate:rollback # 回滚到最后一次
$ php artisan migrate:rollback --step=5 # 回滚指定次数
$ php artisan migrate:reset # 回滚所有
```

## 刷新迁移

```bash
$ migrate:refresh # 回滚你所有的迁移后执行 migrate 命令
$ php artisan migrate:refresh --seed # 刷新数据库并执行数据库填充
$ php artisan migrate:refresh --step=5 # 回滚并重新迁移指定数量的迁移
```

## 删除迁移

```bash
$ php artisan migrate:fresh # 删除数据中所有的数据表并在之后执行 migrate 命令
$ php artisan migrate:fresh --seed
```

# 数据表

## TODO

# 字段类型

## ID

### 自增ID

```php
$table->bigIncrements('id'); // UNSIGNED BIG INTEGER
```

```php
$table->increments('id'); // UNSIGNED INTEGER
```

```php
$table->tinyIncrements('id'); // UNSIGNED TINYINT
```

```php
$table->smallIncrements('id'); // UNSIGNED SMALLINT
```

```php
$table->mediumIncrements('id'); // UNSIGNED MEDIUM INTEGER
```

### UUID

```php
$table->uuid('id');	// UUID
```

## 数值

### 整数

```php
$table->bigInteger('votes'); // BIGINT
$table->unsignedBigInteger('votes'); // UNSIGNED BIGINT
```

```php
$table->integer('votes'); // INTEGER
$table->unsignedInteger('votes'); // UNSIGNED INTEGER
```

```php
$table->tinyInteger('votes'); // TINYINT
$table->unsignedTinyInteger('votes'); // UNSIGNED TINYINT
```

```php
$table->smallInteger('votes'); // SMALLINT
$table->unsignedSmallInteger('votes'); // UNSIGNED SMALLINT
```

```php
$table->mediumInteger('votes'); // MEDIUMINT
$table->unsignedMediumInteger('votes'); // UNSIGNED MEDIUMINT
```

### 浮点数

```php
$table->float('amount', 8, 2); // FLOAT 带精度与基数
```

```php
$table->double('amount', 8, 2);	// DOUBLE 带精度与基数
```

```php
$table->decimal('amount', 8, 2); // DECIMAL 带精度与基数
$table->unsignedDecimal('amount', 8, 2); // UNSIGNED DECIMAL 带精度与基数
```

## 字符串

```php
$table->char('name', 100); // CHAR 指定长度
```

```php
$table->string('name'); // VARCHAR
$table->string('name', 100); // VARCHAR 指定长度
```

```php
$table->longText('description'); // LONGTEXT
$table->text('description'); // TEXT
$table->mediumText('description'); // MEDIUMTEXT
```

```php
$table->lineString('positions'); // LINESTRING
$table->multiLineString('positions'); // MULTILINESTRING
```

```php
$table->rememberToken(); // 可空版本的 VARCHAR (100) 的 remember_token 字段
```

## 日期时间

```php
$table->year('birth_year'); // YEAR
```

```php
$table->date('created_at'); // DATE
```

``` php
$table->time('sunrise'); // TIME
$table->timeTz('sunrise'); // TIME 带时区
```

```php
$table->dateTime('created_at'); // DATETIME
$table->dateTimeTz('created_at'); // DATETIME 带时区
```

```php
$table->timestamp('added_on'); // TIMESTAMP
$table->timestampTz('added_on'); // TIMESTAMP 带时区
```

```php
$table->timestamps(); // TIMESTAMP 可空的 created_at 和 updated_at 
$table->timestampsTz(); // TIMESTAMP 可空且带时区的 created_at 和 updated_at
$table->nullableTimestamps(); // 可空版本的 timestamps() 字段
```

## BOOLEAN

```php
$table->boolean('confirmed'); // BOOLEAN
```

## IP

```php
$table->ipAddress('visitor'); // IP 地址
```

## MAC

```php
$table->macAddress('device'); // MAC 地址
```

## JSON

```php
$table->json('options'); // JSON
```

```php
$table->jsonb('options'); // JSONB
```

## BLOB

```php
$table->binary('data'); // BLOB
```

## SET

``` php
$table->set('flavors', ['strawberry', 'vanilla']); // SET
```

## ENUM

```php
$table->enum('level', ['easy', 'hard']); // ENUM
```

## GEOMETRY

```php
$table->geometry('positions'); // GEOMETRY
```

```php
$table->geometryCollection('positions'); // GEOMETRYCOLLECTION
```

## POINT

```php
$table->point('position'); // POINT
```

```php
$table->multiPoint('positions'); // MULTIPOINT
```

## POLYGON

```php
$table->polygon('positions'); // POLYGON
```

```php
$table->multiPolygon('positions'); // MULTIPOLYGON
```

## morphs

```php
$table->morphs('taggable');	// 加入递增的 taggable_id 与字符串 taggable_type
```

```php
$table->uuidMorphs('taggable');	// 加入 taggable_id 与字符串 taggable_type UUID 列
```

```php
$table->nullableMorphs('taggable'); // 可空版本的 morphs () 字段
```

```php
$table->nullableUuidMorphs('taggable'); // 可空版本的 uuidMorphs() 字段
```

## 软删除

```php
$table->softDeletes(); // 为软删除添加一个可空的 deleted_at 字段
$table->softDeletesTz(); // 为软删除添加一个可空的 带时区的 deleted_at 字段
```

# 索引字段

## 主键

```php
$table->primary('id'); // 添加主键
$table->primary(['id', 'parent_id']); // 复合索引
```

## 唯一索引

```php
$table->string('email')->unique(); // 字段创建时指定
$table->unique('email'); // 字段创建后指定
```

## 普通索引

```php
$table->string('view_count')->index();
$table->index(['account_id', 'created_at']); // 复合索引
```

## 空间索引

```php
$table->spatialIndex('location'); // 添加空间索引（不支持 SQLite）
```

## 自定义索引名称

```php
$table->unique('email', 'unique_email'); // 第二个参数是 自定义索引名称
```

## 索引长度

### TODO

## 重命名索引

```php
$table->renameIndex('from', 'to')
```

## 删除索引

若要删除索引，则必须指定索引的名称。Laravel 默认会自动将数据库名称、索引的字段名及索引类型简单地连接在一起作为名称。

```php
$table->dropPrimary('users_id_primary'); // 从 users 表中删除主键
```

```php
$table->dropUnique('users_email_unique'); // 从 users 表中删除唯一索引
```

```php
$table->dropIndex('geo_state_index'); // 从 geo 表中删除基本索引
```

```php
$table->dropSpatialIndex('geo_location_spatialindex'); // 从 geo 表中删除空间索引（不支持 SQLite）
```

如果将字段数组传给 dropIndex 方法，会删除根据表名、字段和键类型生成的索引名称：

```php
Schema::table('geo', function (Blueprint $table) {
    $table->dropIndex(['state']); // Drops index 'geo_state_index'
});
```

# 外键约束

## TODO

# 字段修饰符

## 字段位置

```php
->first() // 将此字段放置在数据表的 "首位" (MySQL)
```

```php
->after('column') // 将此字段放置在其它字段 "之后" (MySQL)
```

## 注释

```php
->comment('my comment') // 为字段增加注释 (MySQL)
```

## 默认值

```php
->default($value) // 为字段指定 "默认" 值
```

## 允许 NULL

```php
->nullable($value = true) // 此字段允许写入 NULL 值（默认情况下）
```

## UNSIGNED

```php
->unsigned() // 设置 INTEGER 类型的字段为 UNSIGNED (MySQL)
```

## 自增主键

```php
->autoIncrement() // 将 INTEGER 类型的字段设置为自动递增的主键
```

## 字符集

```php
->charset('utf8') // 指定一个字符集 (MySQL)
```

## 排序规则

```php
->collation('utf8_unicode_ci') // 指定列的排序规则 (MySQL/SQL Server)
```

## CURRENT_TIMESTAMP

```php
->useCurrent() // 将 TIMESTAMP 类型的字段设置为使用 CURRENT_TIMESTAMP 作为默认值
```

## 存储生成

```php
->storedAs($expression) // 创建一个存储生成的字段 (MySQL)
```

## 虚拟生成

```php
->virtualAs($expression) // 创建一个虚拟生成的字段 (MySQL)
```

## 序列生成

```php
->generatedAs($expression) // 使用指定的序列生成标识列（PostgreSQL）
```

## 序列值优先

```php
->always() // 定义序列值优先于标识列的输入 (PostgreSQL)
```

# 修改字段

## TODO

