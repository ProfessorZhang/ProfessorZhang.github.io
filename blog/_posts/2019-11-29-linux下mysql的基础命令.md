linux下的mysql数据库是完全的命令行模式，所以我们需要知道这个环境下的mysql的基础命令。这里的模式语言有DDL，DML，DQL，DCL。
在我看来，数据库的操作无非是增删查改，我们平时用的最多的也就是查询了。

----------

**DDL**（数据库模式定义语言）

所谓定义语言也就是对数据库及表格的创建，删除和修改（create ,alter,drop）
创建数据库

```
MariaDB [(none)]> create database dbstudent;
Query OK, 1 row affected (0.00 sec)
```
这样我们就创建好一个数据库，我们可以通过show来插口那我们有的数据库

```
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| #mysql50#.gnome2   |
| #mysql50#.mozilla  |
| db_student         |
| dbstudent          |
| hellodb            |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
8 rows in set (0.16 sec)
```
可以看到我们有好几个数据库
我们可以在这个数据库下创建一个表

```
MariaDB [dbstudent]> create table student(id tinyint unsigned primary key,name varchar(20) not null);
Query OK, 0 rows affected (0.12 sec)
```
修改就是alter

```
MariaDB [dbstudent]> alter table student add phone char(11) after name;
Query OK, 0 rows affected (0.02 sec)
Records: 0  Duplicates: 0  Warnings: 0
```
这时表就是这样的

```
MariaDB [dbstudent]> desc student;
+-------+---------------------+------+-----+---------+-------+
| Field | Type                | Null | Key | Default | Extra |
+-------+---------------------+------+-----+---------+-------+
| id    | tinyint(3) unsigned | NO   | PRI | NULL    |       |
| name  | varchar(20)         | NO   |     | NULL    |       |
| phone | char(11)            | YES  |     | NULL    |       |
+-------+---------------------+------+-----+---------+-------+
```
下面就是drop删除了

```
MariaDB [dbstudent]> drop table student;
Query OK, 0 rows affected (0.11 sec)
```
这样这个student表就删除了
这个语句同样可以删除一个库

```
MariaDB [(none)]> drop database dbstudent;
Query OK, 0 rows affected (0.10 sec)
```
这就是DDL，然后是DML。
**DML**（数据库操纵语言）
也就是对数据库中表的内容的修改了，相信有了解数据库的都知道（insert，update，delete）
这是对一个表的内容的插入，更改，和删除。

```
MariaDB [db_student]> insert into student values(5,'ming',20,default);
Query OK, 1 row affected (0.01 sec)
```
这样就插入student数据库中一条数据，有时候我们的数据库中的咧较多，并且不想打默认的时候我们可以这样插入

```
MariaDB [db_student]> insert into student(id,name,age) values(9,'hong',14);
Query OK, 1 row affected (0.00 sec)
```
这样我们这个表就是这样的

```
MariaDB [db_student]> select * from student;
+----+-------+------+------+
| id | name  | age  | sex  |
+----+-------+------+------+
|  5 | ming  |   20 | m    |
|  9 | hong  |   14 | m    |
| 18 | zhang |   18 | m    |
+----+-------+------+------+
3 rows in set (0.00 sec)
```
（这个查询语句是下一个要说的DQL。）
这个表中数据的更改怎么搞呢？？

```
MariaDB [db_student]> update student set sex='f' where id=5;
Query OK, 1 row affected (0.12 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```
删除表中的一条数据就是delete了

```
MariaDB [db_student]> delete from student where id=18;
Query OK, 1 row affected (0.01 sec）
```
我们的这个student表就变成这样的了

```
MariaDB [db_student]> select * from student;
+----+------+------+------+
| id | name | age  | sex  |
+----+------+------+------+
|  5 | ming |   20 | f    |
|  9 | hong |   14 | m    |
+----+------+------+------+
2 rows in set (0.01 sec)
```
**DQL**（数据库查询语言）
这个虽然只有一个select，但是这个却是用的最多的一个。
如上所示，查询一个表的所有内容

```
MariaDB [db_student]> select * from student;
+----+------+------+------+
| id | name | age  | sex  |
+----+------+------+------+
|  5 | ming |   20 | f    |
|  9 | hong |   14 | m    |
+----+------+------+------+
2 rows in set (0.01 sec)
```
也可以查询表的部分内容

```
MariaDB [db_student]> select name,age from student;
+------+------+
| name | age  |
+------+------+
| ming |   20 |
| hong |   14 |
+------+------+
2 rows in set (0.00 sec)
```
**DCL**（数据库控制语言）
这个是数据库授权用的，主要用于别人在查询我的数据库的时候，我通过授权方式可以让他可以做那些操作，及对哪些数据库操作（grand，revoke）

```
grant 权限 on 数据库对象 to 用户
```
授权

```
grant all on db_student.* to root@'192.168.188.7';
```
这是对这台主机进行db_student数据库的所有授权，就是这台主机可以登录我的数据库并且可以对db_student增删改查，为所欲为

```
grant select,insert on db_student to root@'192.168.188.7';
```
这种授权就限制了这台主机root登陆后只能对db_student数据库做查询和插入，其他的操作都做不了。

```
grant select(id,name) on db_student.student to root@'192.168.188.7';
```
这样是对字段进行授权，也就是在这个主机上登陆的root用户只能看到db_student库下student表的id和name两列。
查看授权就是

```
 show grants for root@'192.168.188.7'
```
这个是授权，那么撤回授权就简单了，只需要将授权的grant改为revoke，on改为from就行了

```
 revoke select(id,name) from db_student.student to root@'192.168.188.7';
```
这样就将赋予的权限撤回了。
这些是我在学习linux下的mysql的集中基础命令语句，很简单，当然用的也很多。

