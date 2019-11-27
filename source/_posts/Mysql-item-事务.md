---
title: 命令行下mysql事务控制方法
date: 2017-04-10 21:18:02
tags: mysql
categories: mysql
---

查看mysql系统的事务隔离级别 :relaxed:

```bash
mysql> SELECT @@global.tx_isolation;
+-----------------------+
| @@global.tx_isolation |
+-----------------------+
| REPEATABLE-READ       |
+-----------------------+
1 row in set (0.00 sec)

```

查看mysql会话的事务隔离级别

```bash
mysql> SELECT @@tx_isolation;
+-----------------+
| @@tx_isolation  |
+-----------------+
| REPEATABLE-READ |
+-----------------+
1 row in set (0.00 sec)

mysql> SELECT @@session.tx_isolation;
+------------------------+
| @@session.tx_isolation |
+------------------------+
| REPEATABLE-READ        |
+------------------------+
1 row in set (0.00 sec)
```


<!-- more -->

设置系统的事务隔离级别

```bash
mysql> set global transaction isolation level read committed;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @@global.tx_isolation;
+-----------------------+
| @@global.tx_isolation |
+-----------------------+
| READ-COMMITTED        |
+-----------------------+
1 row in set (0.00 sec)

mysql> SELECT @@tx_isolation;
+-----------------+
| @@tx_isolation  |
+-----------------+
| REPEATABLE-READ |
+-----------------+
1 row in set (0.00 sec)
```

设置会话的事务隔离级别

```bash
mysql> set session transaction isolation level read committed;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT @@global.tx_isolation;
+-----------------------+
| @@global.tx_isolation |
+-----------------------+
| READ-COMMITTED        |
+-----------------------+
1 row in set (0.00 sec)

mysql> SELECT @@tx_isolation;
+----------------+
| @@tx_isolation |
+----------------+
| READ-COMMITTED |
+----------------+
1 row in set (0.00 sec)

```

mysql默认是自动提交事务的，查看autocommit变量

```bash
mysql> select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            1 |
+--------------+
1 row in set (0.01 sec)

```

设置mysql不自动提交事务

```bash
mysql> set autocommit = 0;
Query OK, 0 rows affected (0.00 sec)

mysql> select @@autocommit;
+--------------+
| @@autocommit |
+--------------+
|            0 |
+--------------+
1 row in set (0.01 sec)
```

使用rollback回滚事务

```bash
mysql> select * from t1;
Empty set (0.00 sec)

mysql> insert into t1 values (51, 3000);
Query OK, 1 row affected (0.00 sec)

mysql> select * from t1;
+----+------+
| a  | b    |
+----+------+
| 51 | 3000 |
+----+------+
1 row in set (0.00 sec)

mysql> rollback;
Query OK, 0 rows affected (0.03 sec)

mysql> select * from t1;
Empty set (0.00 sec)

mysql>
```

使用start transaction;或begin;显示的开启一个事务

```bash
mysql> start transaction;
Query OK, 0 rows affected (0.00 sec)

mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into t1 values (51, 3000);
Query OK, 1 row affected (0.00 sec)

mysql> select * from t1;
+----+------+
| a  | b    |
+----+------+
| 51 | 3000 |
+----+------+
1 row in set (0.00 sec)

mysql> rollback;
Query OK, 0 rows affected (0.04 sec)

mysql> select * from t1;
Empty set (0.00 sec)

mysql> commit;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from t1;
Empty set (0.00 sec)

```

使用savepoint在事务中创建一个保存点（可以在一个事务中创建多个保存点）

```bash
mysql> begin;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into t1 values (51, 3000);
Query OK, 1 row affected (0.00 sec)

mysql> select * from t1;
+----+------+
| a  | b    |
+----+------+
| 51 | 3000 |
+----+------+
1 row in set (0.00 sec)

mysql> savepoint tx1;
Query OK, 0 rows affected (0.00 sec)

mysql> insert into t1 values (52, 3000);
Query OK, 1 row affected (0.00 sec)

mysql> select * from t1;
+----+------+
| a  | b    |
+----+------+
| 51 | 3000 |
| 52 | 3000 |
+----+------+
2 rows in set (0.00 sec)

mysql> rollback to tx1;
Query OK, 0 rows affected (0.00 sec)

mysql> select * from t1;
+----+------+
| a  | b    |
+----+------+
| 51 | 3000 |
+----+------+
1 row in set (0.00 sec)

mysql> commit;
Query OK, 0 rows affected (0.04 sec)

```

以上是mysql命令行是对事务隔离级别和事务提交的基本操作。现在说明以下操作基于可重复复读的隔离级别，事务不自动提交。

```bash
mysql> set global transaction isolation level repeatable read;
Query OK, 0 rows affected (0.00 sec)

mysql> set session transaction isolation level repeatable read;
Query OK, 0 rows affected (0.00 sec)

mysql> set autocommit = 0;
Query OK, 0 rows affected (0.00 sec)
```