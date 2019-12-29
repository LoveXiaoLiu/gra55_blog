+++
title = "MySQL 事务隔离级别"
description = "MySQL 有四种事务隔离级别：RU、RC、RR（Default）、Serializable"
author = "gra55"
categories = ["数据库"]
tags = ["mysql", "db", "2019"]
date = "2019-12-29"
featured = "school-eos.jpg"
featuredalt = "媳妇练习摄影花絮，2019年11月10号拍摄于陕西师范大学"
featuredpath = "date"
linktitle = ""
type = "post"
+++

> 说隔离级别之前，先说说**并发**会带来什么问题？
> 
> 1. 脏读：事务 A 读取了事务 B 更新的数据，然后 B 回滚了操作，那么 A 就是读取到了脏数据
>
> 2. 不可重复读：事务 A 多次读取同一行记录，事务 B 在 A 多次读取过程中更新并提交了该记录，导致 A 多次读取的数据不一致（没有快照读）
> 
> 3. 幻读：事务 A 修改 id>10 的记录，使 name='cs'。但是事务 B 同时插入了 id=20 的一条记录，最后事务 A 发现 id=20 的记录没有被修改，仿佛出现了幻觉
> 
> **Note**：不可重复读和幻读很容易混淆。不可重复读针对的是 update（更新某几条记录），解决不可重复度只需要**锁住需要更新的某几条记录即可**；幻读针对的是 insert/delete（增加/删除几条记录），解决幻读则需要给**全表加锁**。

```sql
-- 查看 session 隔离级别：
select @@tx_isolation;
-- 修改 session 隔离级别：
set session transacton isolation level xxxxx;

-- 查看全局隔离级别：
select @@global.tx_isolation;
-- 修改全局隔离级别：
set global transacton isolation level xxxxx;
```

# 0x00 Read-uncommitted

RU 隔离级别下，上面三个问题都没有解决。

从下面两个并发事务来看，事务 B 还没有 commit，事务 A 就能读到更新的数据，可见 RU 隔离级别是最低的，脏读都没有解决，不可重复读和幻读就更加不能解决了。

```flow
+--------------------------------------------------------------------+------------------------------------------------------------------+
|                      Session A                                     |                         Session B                                |
+---------------------------------------------------------------------------------------------------------------------------------------+
|                                                                    |                                                                  |
| mysql> set session transaction isolation level read uncommitted;   | mysql> set session transaction isolation level read uncommitted; |
| Query OK, 0 rows affected (0.00 sec)                               | Query OK, 0 rows affected (0.00 sec)                             |
|                                                                    |                                                                  |
| mysql> start transaction;                                          | mysql> start transaction;                                        |
| Query OK, 0 rows affected (0.00 sec)                               | Query OK, 0 rows affected (0.00 sec)                             |
|                                                                    |                                                                  |
| mysql> select * from student;                                      | mysql> select * from student;                                    |
|                                                                    |                                                                  |
| +----+------+                                                      | +----+------+                                                    |
| | id | name |                                                      | | id | name |                                                    |
| +-----------+                                                      | +-----------+                                                    |
| |  1 | cs   |                                                      | |  1 | cs   |                                                    |
| +----+------+                                                      | +----+------+                                                    |
| 1 row in set (0.00 sec)                                            | 1 row in set (0.00 sec)                                          |
|                                                                    |                                                                  |
| mysql>                                                             | mysql> update student set name='yl' where id=1;                  |
|                                                                    | Query OK, 1 row affected (0.00 sec)                              |
| mysql>                                                             |                                                                  |
|                                                                    | mysql> select * from student;                                    |
| mysql> select * from student;                                      |                                                                  |
|                                                                    | +----+------+                                                    |
| +----+------+                                                      | | id | name |                                                    |
| | id | name |                                                      | +-----------+                                                    |
| +-----------+                                                      | |  1 | yl   |                                                    |
| |  1 | yl   |                                                      | +----+------+                                                    |
| +----+------+                                                      | 1 rows in set (0.00 sec)                                         |
| 1 rows in set (0.00 sec)                                           |                                                                  |
|                                                                    | mysql>                                                           |
| mysql>                                                             |                                                                  |
|                                                                    |                                                                  |
+--------------------------------------------------------------------+------------------------------------------------------------------+
```

# 0x01 Read-committed

RC 隔离级别下解决脏读，但是没有解决不可重复读和幻读。

从下面两个并发的事务来看，只有当事务 B 提交了事务以后，事务 A 才能读到数据，这也就是解决了脏读。

但是，在同一个事务 A 内，出现了不可重复读的现象，连续读取同一条记录，数据不一致。

```flow
+--------------------------------------------------------------------+------------------------------------------------------------------+
|                      Session A                                     |                         Session B                                |
+---------------------------------------------------------------------------------------------------------------------------------------+
|                                                                    |                                                                  |
| mysql> set session transaction isolation level read committed;     | mysql> set session transaction isolation level read committed    |
| Query OK, 0 rows affected (0.00 sec)                               | Query OK, 0 rows affected (0.00 sec)                             |
|                                                                    |                                                                  |
| mysql> start transaction;                                          | mysql> start transaction;                                        |
| Query OK, 0 rows affected (0.00 sec)                               | Query OK, 0 rows affected (0.00 sec)                             |
|                                                                    |                                                                  |
| mysql> select * from student;                                      | mysql> select * from student;                                    |
|                                                                    |                                                                  |
| +----+------+                                                      | +----+------+                                                    |
| | id | name |                                                      | | id | name |                                                    |
| +-----------+                                                      | +-----------+                                                    |
| |  1 | cs   |                                                      | |  1 | cs   |                                                    |
| +----+------+                                                      | +----+------+                                                    |
| 1 row in set (0.00 sec)                                            | 1 row in set (0.00 sec)                                          |
|                                                                    |                                                                  |
|                                                                    | mysql> update student set name='yl' where id=1;                  |
|                                                                    | Query OK, 1 row affected (0.00 sec)                              |
|                                                                    |                                                                  |
| mysql> select * from student;                                      | mysql> select * from student;                                    |
|                                                                    |                                                                  |
| +----+------+                                                      | +----+------+                                                    |
| | id | name |                                                      | | id | name |                                                    |
| +-----------+                                                      | +-----------+                                                    |
| |  1 | cs   |                                                      | |  1 | yl   |                                                    |
| +----+------+                                                      | +----+------+                                                    |
| 1 row in set (0.00 sec)                                            | 2 rows in set (0.00 sec)                                         |
|                                                                    |                                                                  |
| mysql> select * from student;                                      | mysql> commit;                                                   |
|                                                                    | Query OK, 0 row affected (0.00 sec)                              |
| +----+------+                                                      |                                                                  |
| | id | name |                                                      | mysql>                                                           |
| +-----------+                                                      |                                                                  |
| |  1 | yl   |                                                      |                                                                  |
| +----+------+                                                      |                                                                  |
| 2 rows in set (0.00 sec)                                           |                                                                  |
|                                                                    |                                                                  |
| mysql>                                                             |                                                                  |
|                                                                    |                                                                  |
+--------------------------------------------------------------------+------------------------------------------------------------------+
```

# 0x02 Repeatable-read

RR 隔离级别下解决脏读和不可重复读，但是没有解决幻读。

从下面两个并发的事务来看，即使事务 B 提交了事务以后，事务 A 还是不能读到数据，这就像开启事务 A 之前打了一个快照，读取到的数据还是开启事务之前的数据，所以也叫作快照读。

但是，当事务 B 插入一条数据的时候，事务 A 虽然查不到数据，但是插入相同 id 的记录会报主键冲突，就跟产生了幻觉一样，这就叫幻读（只加了行锁，没有加全表锁导致的）。

```flow
+--------------------------------------------------------------------+------------------------------------------------------------------+
|                      Session A                                     |                         Session B                                |
+---------------------------------------------------------------------------------------------------------------------------------------+
|                                                                    |                                                                  |
| mysql> set session transaction isolation level repeatable read;     | mysql> set session transaction isolation level repeatable read    |
| Query OK, 0 rows affected (0.00 sec)                               | Query OK, 0 rows affected (0.00 sec)                             |
|                                                                    |                                                                  |
| mysql> start transaction;                                          | mysql> start transaction;                                        |
| Query OK, 0 rows affected (0.00 sec)                               | Query OK, 0 rows affected (0.00 sec)                             |
|                                                                    |                                                                  |
| mysql> select * from student;                                      | mysql> select * from student;                                    |
|                                                                    |                                                                  |
| +----+------+                                                      | +----+------+                                                    |
| | id | name |                                                      | | id | name |                                                    |
| +-----------+                                                      | +-----------+                                                    |
| |  1 | cs   |                                                      | |  1 | cs   |                                                    |
| +----+------+                                                      | +----+------+                                                    |
| 1 row in set (0.00 sec)                                            | 1 row in set (0.00 sec)                                          |
|                                                                    |                                                                  |
|                                                                    | mysql> update student set name='yl' where id=1;                  |
|                                                                    | Query OK, 1 row affected (0.00 sec)                              |
|                                                                    |                                                                  |
| mysql> select * from student;                                      | mysql> select * from student;                                    |
|                                                                    |                                                                  |
| +----+------+                                                      | +----+------+                                                    |
| | id | name |                                                      | | id | name |                                                    |
| +-----------+                                                      | +-----------+                                                    |
| |  1 | cs   |                                                      | |  1 | yl   |                                                    |
| +----+------+                                                      | +----+------+                                                    |
| 1 row in set (0.00 sec)                                            | 1 rows in set (0.00 sec)                                         |
|                                                                    |                                                                  |
|                                                                    | mysql> commit;                                                   |
|                                                                    | Query OK, 0 row affected (0.00 sec)                              |
|                                                                    |                                                                  |
| mysql> select * from student;                                      | mysql>                                                           |
|                                                                    |                                                                  |
| +----+------+                                                      |                                                                  |
| | id | name |                                                      |                                                                  |
| +-----------+                                                      |                                                                  |
| |  1 | cs   |                                                      |                                                                  |
| +----+------+                                                      |                                                                  |
| 1 rows in set (0.00 sec)                                           |                                                                  |
|                                                                    |                                                                  |
| mysql>                                                             | mysql> start transaction;                                        |
|                                                                    | Query OK, 0 rows affected (0.00 sec)                             |
|                                                                    |                                                                  |
|                                                                    | mysql> insert into student (name) values ('loVe');               |
|                                                                    | Query OK, 1 row affected (0.00 sec)                              |
|                                                                    |                                                                  |
| mysql> select * from student;                                      | mysql> select * from student;                                    |
|                                                                    |                                                                  |
| +----+------+                                                      | +----+------+                                                    |
| | id | name |                                                      | | id | name |                                                    |
| +-----------+                                                      | +-----------+                                                    |
| |  1 | cs   |                                                      | |  1 | cs   |                                                    |
| +----+------+                                                      | |  5 | loVe |                                                    |
| 1 rows in set (0.00 sec)                                           | +----+------+                                                    |
|                                                                    | 2 rows in set (0.00 sec)                                         |
|                                                                    |                                                                  |
|                                                                    | mysql> commit;                                                   |
|                                                                    | Query OK, 0 row affected (0.00 sec)                              |
| mysql> insert into student (id,name) values (5,'like');            |                                                                  |
| ERROR 1062 (23000): Duplicate entry '5' for key 'PRIMARY'          | mysql>                                                           |
|                                                                    |                                                                  |
+--------------------------------------------------------------------+------------------------------------------------------------------+
```

# 0x03 Serializable

Serializable 隔离级别是最严格的级别，所有事务串行执行，即所有读写都加锁，虽然数据一致性的问题都解决了，但是这样效率很差，一般不使用。

---
参考：

:pushpin: [MySQL的四种事务隔离级别](https://www.cnblogs.com/huanongying/p/7021555.html)
