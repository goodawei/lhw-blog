---
title: 常用sql总结
date: 2016-06-28 22:18:35
tags: sql
categories: mysql
---

### :id: sql查询前n天的日期 n>=1

```bash

	select UNIX_TIMESTAMP(date_sub(curdate(),interval 1 day) ) ;

	+-----------------------------------------------------+
	| UNIX_TIMESTAMP(date_sub(curdate(),interval 1 day) ) |
	+-----------------------------------------------------+
	|                                          1466956800 |
	-----------------------------------------------------+

	select date_sub(curdate(),interval 1 day) ;

	+------------------------------------+
	| date_sub(curdate(),interval 1 day) |
	+------------------------------------+
	| 2016-06-27                         |
	+------------------------------------+
```

### 查当天日期和时间

```bash
	select CURDATE();

	+------------+
	| CURDATE()  |
	+------------+
	| 2016-06-28 |
	+------------+

	select NOW();

	+---------------------+
	| NOW()               |
	+---------------------+
	| 2016-06-28 22:29:48 |
	+---------------------+
```

<!-- more -->

### 按月或按年

```bash

	select YEAR(NOW());
	+-------------+
	| YEAR(NOW()) |
	+-------------+
	|        2016 |
	+-------------+

	select MONTH(NOW());

	+--------------+
	| MONTH(NOW()) |
	+--------------+
	|            7 |
	+--------------+

```

### 一种不明白的sql insert 语句

```bash
         $sql="INSERT INTO fee"
            . "(`create_time`, `create_uid`, `update_time`, `update_uid` "
            . "SELECT "
            . "2016-06-28"
            . ",10"
            . ",2016-06-28"
            . ",10"
            . "FROM DUAL "
            . "WHERE NOT EXISTS(SELECT *  fee WHERE type=2 "
             . "and  id=1 "
  
         $pdo->query($sql);

```

