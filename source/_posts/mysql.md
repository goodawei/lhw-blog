---
title: mysql锁机制
date: 2017-04-11 19:01:18
tags: mysql
categories: mysql
---

# 锁机制用于管理对共享资源的并发访问，提供数据的完整性和一致性

> 读锁是共享的，不影响并发的读取，但是会阻塞写操作（当然这只是默认情况，可以通过修改配置，使其可以进行末尾插入），写锁是排他的，阻塞读和写。

### 行锁

InnoDB行锁是通过给索引记录加锁来实现的，即：InnoDB会在它扫描到的每条索引记录上设置一个S或X锁，在MVCC模式下，简单的select语句不会加任何锁，除非手动添加。因此查询时，索引很重要 :100:

### 锁模式

共享锁S(Shared Lock)： 允许事务使用该锁去读取
加锁语句： select ... lock in share mode (手动加锁)
排它锁X(Exclusive Lock)： 允许事务使用该锁去update、insert或者delete
加锁语句： insert ... update ... delete ... select ... for update (手动加锁)

### 阻塞

因为不同锁之间的兼容性关系，在有些时刻一个事务中的锁需要等待另一事务中的锁释放它所占有的资源，阻塞可以确保事务正常进行

### 死锁

指两个或两个以上的事务在执行过程中，因争夺资源而造成的一种互相等待的现象 除了超时机制外（innodb_lock_wait_timeout），InnoDB还采用了wait-for graph来进行死锁检测，若检测到死锁，InnoDB会选择回滚undo量最小的事务

<!-- more -->

### 更新丢失

由于并发问题，在事务内部未正确使用锁定导致的更新丢失问题 zhangshan --> money = 20

Session A                     |               SessionB
-------------------------     | --------------------------------------
 begin <br> 查询money = 20 <br>select money from account where user='zhangshan'; |
                    <br>    |begin;<br>查询money = 20<br>select money from account where user='zhangshan';<br>
update account set money=money+10 where user='zhangshan';<br>commit;  |  <br>
                    <br>            | 	update account set money=money+50 where user='zhangshan';<br>commit;
                    
最终的结果是：70，少加10
解决办法：
select ... for update 锁定该条记录，加排它锁


----------
Session A                     |               SessionB
-------------------------     | --------------------------------------
begin<br>对该条记录加排它锁X<br>查询money = 20<br>select money from account where user='zhangshan' for update; |
                    <br>    |begin;<br>被阻塞，程序挂起，等待Session A完成<br>查询money = 20<br>select money from account where user='zhangshan' for update;<br>
update account set money=money+10 where user='zhangshan';<br>commit;  |  <br>
                    <br>            |update account set money=money+50 where user='zhangshan';<br>commit;


----------


                    
### 死锁产生的2种情况

```basn
 CREATE TABLE b (
    id int(11) NOT NULL,
    uid int(11) DEFAULT NULL,
    PRIMARY KEY (id),
    KEY uid (uid)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```
id                     |           uid
-------------------------     | --------------------------------------
1   | 10
2   | 20
3   | 30
4   | 40

#### 第一种情况：区间死锁（目前最多的情况）

Session A                     |               SessionB
-------------------------     | --------------------------------------
begin;<br>使用辅助索引筛选时，此时使用Next-key算法锁定区间为:(11,55)<br>select * from b where uid=22 for update; |
                    <br>    |begin;<br>此时Session B被阻塞<br>select * from b where uid<22 for update;<br>
插入成功...<br>insert into b values(9,20);<br>commit;  |  <br>
                    <br>            |死锁出现<br>Deadlock found，try restarting transaction<br>rollback;
                    
#### 第二种情况：不同记录锁定

Session A                     |               SessionB
-------------------------     | --------------------------------------
begin;<br>id为主键，使用Record Lock算法锁定锁定id=5该行记录<br>select * from b where id = 5 for update; |
                    <br>    |begin;<br>同理<br>select * from b where id=8 for update;<br>
select * from b where id = 8 for update;  |  <br>
                    <br>            |死锁出现<br>select * from b where id=5 for update;<br>Deadlock found，try restarting transaction;
                          commit  |<br>
                           <br> | rollback;
                           


----------


                         
### 如何避免阻塞和死锁：
update时，尽量使用主键或辅助索引作为where条件，切勿不使用索引进行查询，否则全表锁定
跑脚本或者批量任务时，每次只对一条数据加事务处理，切勿将一个大循环包裹在一个事务里面
严禁在update语句里面封装子查询，select语句，update ... set ... where ...就ok，其他的由程序处理
养成良好的事务习惯，事务一定要进行commit或rollback操作
涉及到修改同一张表数据的定时任务，应该间隔一定的时间，避免出现死锁
减少锁定范围，在跑定时任务时，筛选数据时，尤其是日期范围的，请务必使用between ... and ... 而不是end_time < now()

### 隐式转换

1. 查询时，类型不对，导致类型转换，结果不对
mysql> select * from test_2 where name=0;

id                    |             name
-------------------------     | --------------------------------------
        1        | lhw
        2        | cxx
        3        | 0
        
分析：很多开发在书写sql时，没有考虑到传入到数据库的值的类型，比如：上面name类型为char，而传入查询时，却输入的是0，而不是'0'
结果：mysql对匹配到行的字符串进行了类型转换，发现转换非法，转换成了0
注意：在查询时检查传入的查询条件类型

2. 全表扫描，查询慢
mysql> explain select * from xboss_account where mobile=15510337665;
![此处输入图片的描述][1]


  [1]: http://o8979n2hu.bkt.clouddn.com/extra.png
  
  
 
分析：mobile字段类型为char(11)，查询时，应该使用mobile='15510337665'
注意：在php端书写sql时，应正确传入查询值的类型
正确使用类型转换，防止SQL注入，其它的类型转换，感兴趣的可以自己研究，如：`select '11aa' + 11,select a+1，select '1'+1`等