## SQL的分类

### DDL

数据定义语言，关键字是create、alter、drop

### DML

数据操作语言，关键字是insert、delete、update、select

### DCL

数据控制语言，关键字是grant、revoke

## DDL

对数据库和表的创建和添加数据

### 数据库层面

查看数据库/表

```sql
show databases/*tables*/;
```

选择数据库

```sql
use databasesname;
```

查看当前数据库（返回当前数据库名）

```sql
select database();
```

**创建数据库**

```sql
create database [if not exists]库名;
```

**查看数据库的结构（得到创建时的语句）**

```sql
show create database databasename;
```

**查看数据库所用的字符集**

```sql
show variables like 'character_%';
```

like表示像后面的

**指定数据库所用的字符集**

```sql
create database databasename[if not exists] character set 'utf8mb4';
```

set表示设置为后面的

**修改数据库（不能修改数据库的名字）**

```sql
alter database databasename character set 'utf8mb4';
```

**删除数据库**

```sql
drop database databasename;
```

mysql
sys
performance_schema
information_schema
这4个系统库不可删除

### 表层面

**创建表**

```sql
create table if not exists library.books
(id  int,
name varchar(20),
price double,
author_id int,
publish_date  data
);
```

需要默认值的话在类型后面加default跟默认值

**基于现有表创建表**

library数据库中创建emp2

```sql
create table emp2
as
select employee_id,first_name,salary
from northwind.employees;
```

**只要表结构不要数据**

```sql
create table emp3
as
select *
from northwind.employees
where 0;
```

where 0 就可以不要任何数据

**修改表**

添加属性

```sql
alter table emp2
add column hire_date date;
```

修改属性名 (字段长度）

将employee_id修改为id

```sql
alter table emp2
change column employee_id id int(6);
```

删除列

```sql
alter table emp2
drop column email;
```

**删除整个表**

```sql
drop table tablename;
```

## DML

**添加数据**

逐行添加

```sql
insert into emp values(2,'lina2','2019-7-2',1000);

```

```sql
insert into emp(id,hire_date,name) values (2,'2019-7-2','lina2'),(2,'2019-7-2','lina2'),(2,'2019-7-2','lina2'),(2,'2019-7-2','lina2');
```

查询结构添加

```sql
insert into emp(id,name,salary,hire_date)
select employee_id,first_name,salary,hire_date
from northwind.employees
where department_id in (60,70);
```

**更新数据**

```sql
update emp
set name='trista',salary=200000*1.1
where id=1;
```

**删除数据**

```sql
delete from emp where id=1;
```

**计算列**

```sql
create table tb(
a int,
b int,
c int,
d int generated always as (a+b+c) virtual
);
```

**数据类型**

- 整型（8.0版本默认不用定义长度）

tinyint 1个字节

smallint 2个字节 

mediumint 3个字节

int 4个字节

bigint 8个字节

```sql
create table tb_int2(
f1 int unsigned,
f2 int(5),
f3 int(5) zerofill
);
```

zerofill 当宽度小于定义位数时，在数字前用0填满

- 浮点数

double

float

精度不同，存储空间不同

通常double比float大两倍，更精确

- 定点数

decimal(M, D)

共M位，小数位占D位

- 位类型

bit

bit（8）  11111111~ 00000000 255~0
bit（1）  1  0
bit （2）  11 00

- 时间类型

year(n)

date

time

datetime

timestamp

```sql
create table dt.tb_time(
  d1 datetime,
  d2 timestamp
);
insert into dt.tb_time values('2021-9-2 14:45:52', '2021-9-2 14:45:52');
```

看起来和datetime差不多，timestamp的精度更大而且可以包含时区

- text类型

文本、主键不能是text类型

char类型会去除尾部的空格，text不会

**修改当前时区**

```sql
set time_zone = '+9:00'
```

- enum类型

从可以选择的对象中选择一个

```sql
create table tb_enum(
	season  enum('春','夏','秋','冬')
);
```

```sql
insert into tb_enum values ('春'),('夏');
```

- set类型

从可以选择的对象中选择任意个

```sql
create table tb_set(a set('a','b','c'));
```

```sql
insert into tb_set values ('a'),('a,b'),('a,c'),('a,c,b');
```

## DCL

### 用户管理

- 创建用户

```sql
create user '用户名' @ '权限' identified by '密码';
```

权限中用**localhost**表示只能从自己的计算机连接数据库服务器

用**%**表示可以从任何计算机连接到数据库服务器

- 修改用户

```sql
update mysql.user set user='trista' where user='lina';
```

这样修改是不安全的

安全的是创建一个新的用户，然后将之前不需要的用户删除

这样将

- 删除用户

```sql
drop user 'trista'@'localhost';
```

### 权限管理

- 授权

格式：

```sql
grant operation on object to user;
```

例子：

```sql
grant select on example_db.customers to 'lina'@'localhost';
```

想对某个数据库中的所有表授权可以**用数据库名.***

- 查看权限

```sql
show grants;
show grants for '用户名'@'主机名';
```

- 回收权限

```sql
revoke 权限1，2，… on [数据库名.]表名 from '用户名'@'主机名';
```

与上面的授权类似。
