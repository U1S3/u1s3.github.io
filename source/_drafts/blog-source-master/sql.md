---
title: SQL
comments: false
date: 2018-12-11 11:59:18
keywords: sql
description: sql学习笔记。
summary: sql学习笔记。
categories: sql
tags: [廖雪峰]
---

##  命令行

### 登录

```bash
$ mysql -u roor -p # 登录
$ winpty mysql -u root -p # windows下 如果上面的方法卡住
$ mysql -h 10.0.1.99 -u root -p # 登录远程数据库
```

### 退出

```bash
$ exit # 退出
```

###  管理

#### 数据库

#####  列出

```sql
SHOW DATABASES;
```

##### 创建

```sql
CREATE DATABASE database_name;
```

##### 删除

```sql
DROP DATABASE database_name;
```

##### 切换

```sql
USE database_name;
```

#### 表

#####  列出

```sql
SHOW TABLES;
```

##### 表结构

```sql
DESC table_name;
```

##### 创建语句

```sql
SHOW CREATE TABLE table_name;
```

##### 创建

```sql
CREATE TABLE table_name;
```

##### 删除

```sql
DROP TABLE table_name;
```

#### 列

##### 新增

```sql
ALERT TABLE table_name ADD COLUMN column_name VARCHAR(10) NOT NULL;
```

##### 修改

```sql
ALTER TABLE students CHANGE COLUMN column_name birthday VARCHAR(20) NOT NULL;
```

##### 删除

```sql
ALTER TABLE students DROP COLUMN birthday;
```

## 实用语句

### 插入或替换

```sql
REPLACE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
```

### 插入或更新

```sql
INSERT INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99) DUPLICATE KEY UPDATE name='小明', gender='F', score=99;
```

### 插入或忽略

```sql
INSERT IGNORE INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'F', 99);
```

### 快照

```sql
CREATE TABLE students_of_class1 SELECT * FROM students WHERE class_id=1;
```

### 写入查询结果集

```sql
CREATE TABLE statistics (
    id BIGINT NOT NULL AUTO_INCREMENT,
    class_id BIGINT NOT NULL,
    average DOUBLE NOT NULL,
    PRIMARY KEY (id)
);
INSERT INTO statistics (class_id, average) SELECT class_id, AVG(score) FROM students GROUP BY class_id;
```

确保`INSERT`语句的列和`SELECT`语句的列能一一对应。

## 引擎选择

总是选择`InnoDB`就完事了。

## 语法规范

SQL语言关键字不区分大小写。

但是要求**SQL关键字总是大写**，**表名和列名均使用小写**，这是一个好习惯。

## 数据类型

### 常用

| 名称 | 类型 | 说明                         |
| --- | --- | --------------------------- |
| INT  | 整形 | 4字节整数类型，范围约+/-21亿 |
| BIGINT | 长整型 | 8字节整数类型，范围约+/-922亿亿 |
| REAL | 浮点型 | 4字节浮点数，范围约+/-10<sup>38</sup> |
| DOUBLE | 浮点型 | 8字节浮点数，范围约+/-10<sup>308</sup> |
| DECIMAL(M,N) | 高精度小数 | 由用户指定精度的小数，例如，DECIMAL(20,10)表示一共20位，其中小数10位，通常用于财务计算 |
| CHAR(N) | 定长字符串 | 存储指定长度的字符串，例如，CHAR(100)总是存储100个字符的字符串 |
| VARCHAR(N) | 变长字符串 | 存储可变长度的字符串，例如，VARCHAR(100)可以存储0~100个字符的字符串 |
| BOOLEAN | 布尔类型 | 存储True或者False |
| DATE | 日期类型 | 存储日期，例如，2018-06-22 |
| TIME | 时间类型 | 存储时间，例如，12:20:59 |
| DATETIME | 日期和时间类型 | 存储日期+时间，例如，2018-06-22 12:20:59 |

除了表中列举的常用数据类型。很多数据类型还有别名，例如，`REAL`又可以写成`FLOAT(24)`。

还有一些不常用的数据类型，例如，`TINYINT`（范围在0~255）。

各数据库厂商还会支持特定的数据类型，例如`JSON`。

选择数据类型的时候，要根据业务规则选择合适的类型。

通常来说，`BIGINT`能满足整数存储的需求，`VARCHAR(N)`能满足字符串存储的需求，这两种类型是使用最广泛的。

### NULL

`NULL`表示字段数据不存在。一个整型字段如果为`NULL`不表示它的值为`0`，同样的，一个字符串型字段为`NULL`也不表示它的值为空串`''`。

通常情况下，字段应该**避免**允许为NULL。不允许为NULL可以简化查询条件，加快查询速度，也利于应用程序读取数据后无需判断是否为NULL。

## 关系模型

### 主键

身份证号、手机号、邮箱地址这些看上去可以唯一的字段，均**不可**用作主键，但可以设为**索引**。

选取主键的一个基本原则是：**不使用**任何业务相关的字段作为主键。

一般把主键字段命名为`id`。常见的可作为`id`字段的类型有：

- 自增整数类型：数据库会在插入数据时自动为每一条记录分配一个自增整数，这样我们就完全不用担心主键重复，也不用自己预先生成主键；

- 全局唯一GUID类型：使用一种全局唯一的字符串作为主键，类似`8f55d96b-8acc-4636-8cb8-76bf8abc2f57`。GUID算法通过网卡MAC地址、时间戳和随机数保证任意计算机在任意时间生成的字符串都是不同的，大部分编程语言都内置了GUID算法，可以自己预算出主键。

对于大部分应用来说，通常自增类型的主键(`BIGINT NOT NULL AUTO_INCREMENT`)就能满足需求。

如果使用`INT`自增类型，那么当一张表的记录数超过2147483647（约21亿）时，会达到上限而出错。使用`BIGINT`自增类型则可以最多约922亿亿条记录。

### 联合主键

关系数据库实际上允许通过多个字段唯一标识记录，即两个或更多的字段都设置为主键。

对于联合主键，允许一列有重复，只要不是所有主键列都重复即可。

没有必要的情况下，我们**尽量不使用**联合主键，因为它给关系表带来了复杂度的上升。

###  外键

#### 一对多

在`students`表中，通过`class_id`的字段，可以把数据与另一张表关联起来，这种列称为`外键`。

外键并**不是**通过列名实现的，而是通过定义外键约束实现的。

由于外键约束会**降低**数据库的性能，大部分互联网应用程序为了追求速度，并**不设置**外键约束，而是仅靠应用程序自身来保证逻辑的正确性。这种情况下，`class_id`仅仅是一个普通的列，只是它起到了外键的作用而已。

```sql
ALTER TABLE students
ADD CONSTRAINT fk_class_id
FOREIGN KEY (class_id)
REFERENCES classes (id);
```

外键约束的名称`fk_class_id`可以任意，`FOREIGN KEY (class_id)`指定了`class_id`作为外键，`REFERENCES classes (id)`指定了这个外键将关联到`classes`表的`id`列（即`classes`表的主键）。

通过定义外键约束，关系数据库可以保证无法插入无效的数据。即如果`classes`表不存在`id=99`的记录，`students`表就无法插入`class_id=99`的记录。

要删除一个外键约束，也是通过`ALTER TABLE`实现的：

```sql
ALTER TABLE students
DROP CONSTRAINT fk_class_id;
```

注意：删除外键约束并没有删除外键这一列。删除列是通过`DROP COLUMN ...`实现的。

#### 多对多

多对多关系实际上是通过两个一对多关系实现的，即通过一个中间表，关联两个一对多关系，就形成了多对多关系。

#### 一对一

一对一关系是指，一个表的记录对应到另一个表的唯一一个记录。

### 索引

索引是关系数据库中对某一列或多个列的值进行预排序的数据结构。

使用索引可以让数据库系统不必扫描整个表，而是直接定位到符合条件的记录，这样就大大加快了查询速度。

```sql
ALTER TABLE students
ADD INDEX idx_score (score);
```

使用`ADD INDEX idx_score (score)`就创建了一个名称为`idx_score`，使用列`score`的索引。

索引名称是任意的，索引如果有多列，可以在括号里依次写上，例如：

```sql
ALTER TABLE students
ADD INDEX idx_name_score (name, score);
```

索引的效率取决于索引列的值**是否散列**。该列的值如果越互不相同，那么索引效率越高。如果记录的列存在大量相同的值，对该列创建索引就没有意义。

可以对一张表创建多个索引。索引的优点是提高了查询效率，缺点是在插入、更新和删除记录时，需要同时修改索引，因此，索引越多，插入、更新和删除记录的速度就越慢。

对于主键，关系数据库会自动对其创建主键索引。使用主键索引的效率是最高的，因为主键会保证绝对唯一。

#### 唯一索引

如果一些列具有唯一性约束，可以给该列添加一个唯一索引。

```sql
-- 假设students表的name不能重复
ALTER TABLE students
ADD UNIQUE INDEX uni_name (name);
```

也可以只对某一列添加一个唯一约束而不创建唯一索引：

```sql
ALTER TABLE students
ADD CONSTRAINT uni_name UNIQUE (name);
```

## 查询数据

### SELECT

不带`FROM`子句的`SELECT`语句可以用来判断当前到数据库的连接是否有效。

许多检测工具会执行一条`SELECT 1;`来测试数据库连接。

### 别名

```sql
-- name是title的别名, student是user的别名
SELECT id, title name, age FROM user as student;
```

### 分页

```sql
-- 从第0条开始获取3条
SELECT * FROM students LIMIT 3 OFFSET 0;
SELECT * FROM students LIMIT 3 0;
```

### 聚合

#### 总数

```sql
SELECT COUNT(*) number FROM students;
```

#### 总和

```sql
SELECT SUM(score) number FROM students;
```

#### 平均数

```sql
SELECT AVG(score) average FROM students;
```

#### 最大值

```sql
SELECT MAX(score) number FROM students;
```

#### 最小值

```sql
SELECT MIN(score) number FROM students;
```

`MAX()`和`MIN()`函数并不限于数值类型。如果是字符类型，`MAX()`和`MIN()`会返回排序最后和排序最前的字符。

如果聚合查询的`WHERE`条件没有匹配到任何行，`COUNT()`会返回0，而`AVG()`、`MAX()`和`MIN()`会返回`NULL`。

#### 分组

先根据指定列分组，再计算。

##### 单列

```sql
SELECT COUNT(*) num FROM students GROUP BY class_id;
```

##### 多列

```sql
SELECT class_id, gender, COUNT(*) num FROM students GROUP BY class_id, gender;
```

### 多表

```sql
SELECT * FROM students, classes;
```

又叫笛卡尔查询，返回所有表的乘积。

多表查询中使用别名可以避免结果集列明重复：

```sql
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c
WHERE s.gender = 'M' AND c.id = 1;
```

### 连接

连接查询需要先确定主表，然后把另一个表的数据“附加”到结果集上。

#### INNER JOIN

返回同时存在于两张表的行。

```sql
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
FROM students s
INNER JOIN classes c
ON s.class_id = c.id;
```

#### RIGHT OUTER JOIN

返回右表都存在的行，左表不存在数据填充`NULL`。

#### LEFT OUTER JOIN

返回左表都存在的行，右表不存在数据填充`NULL`。

#### FULL OUTER JOIN

返回所有记录，对方表不存在数据填充`NULL`。

## 修改数据

### 插入

#### 单条

```sql
INSERT INTO students (class_id, name, gender, score) VALUES (2, '大牛', 'M', 80);
```

#### 多条

```sql
INSERT INTO students (class_id, name, gender, score) VALUES
	(1, '大宝', 'M', 87),
	(2, '二宝', 'M', 81);
```

列名和数据要一一对应，如果列有默认值可不插入。

### 更新

使用`UPDATE`时务必先测试影响行数。

#### 单条

```sql
UPDATE students SET name='大牛', score=66 WHERE id=1;
```

#### 多条

```sql
UPDATE students SET name='小牛', score=77 WHERE id>=5 AND id<=7;
```

#### 表达式

```sql
UPDATE students SET score=score+10 WHERE score<80;
```

### 删除

使用`DELETE`时务必先测试影响行数。

#### 单条

```sql
DELETE FROM students WHERE id=1;
```

#### 多条

```sql
DELETE FROM students WHERE id>=5 AND id<=7;
```

## 事务

### 显式事务

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

### 主动失败

```sql
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
ROLLBACK;
```

## Links

- [SQL教程 - 廖雪峰](https://www.liaoxuefeng.com/wiki/001508284671805d39d23243d884b8b99f440bfae87b0f4000)