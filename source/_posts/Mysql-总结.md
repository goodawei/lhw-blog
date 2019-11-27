---
title: 关于Mysql的一些总结
date: 2017-04-10 21:34:15
tags: mysql
categories: mysql
---

:bug:

### 事务概念：

事务是由一组SQL组成，可以认为是一个执行单元，在事务中，所有操作要么全部成功，要么全部回滚，建表统一使用InnoDB存储引擎


----------


### 事务的ACID的特性：

原子性(atomicity): 事务中的所有操作要么全部提交成功，要么全部失败回滚一致性(consistency): 在事务开始和完成时，数据都必须保持一致状态隔离性(isolation): 一个事务所做的修改在提交之前对其它事务是不可见的持久性(durability):一旦事务提交，其所做的修改便会永久保存在数据库中


----------
### 事务使用场景：

例如一个金融平台，凡是涉及到核心业务，比如：金额、积分等操作都必须使用事务，事务保证了数据的一致性和完整性

比如用户购买，此时需要使用事务，同时需要使用select ... for update进行数据验证，确保数据的一致性和完整性


----------
### 不需要使用事务的场景：
用户登录
用户资产，钱包，积分等查询，允许一定的延时
凡是不涉及到交易的查询


----------
### NULL的问题

NULL：可以插入空值或NULL，NULL会占用数据库空间，数据库会请求额外的空间记录该行为NULL

NOT NULL：不可以插入NULL，可以插入空值，空值不占用数据库空间

NULL占用空间，空值不占用空间，B树索引不会存储NULL值，如果索引字段可以为NULL，则效率下降很多

通常字段设置(NOT NULL) 如时间字段 NOT NULL DEFAULT 0000-00 00:00:00


<!-- more -->

任何数值的运算和字符组合，结果都是NULL

`mysql> select 1+NULL, CONCAT('Invisible',NULL);`

1+NULL| CONCAT('Invisible',NULL)
----- |------
NUL   |  NULL  


NULL允许插入和NULL，查询时容易产生歧义
`mysql> select * from test_1 where name <> ;`

id    | name
----- |------
1     |  lhw

`mysql> select * from test_1 where name is not null;`

id    | name
----- |------
1     |  lhw
2     |  

count问题，忽略非主键外的null值
`mysql> select count(1),count(name) from test_1;`

count(1)    | count(name)
-----       |------
6           |  3


排序问题
在order by排序的时候，如果存在NULL值，那么NULL是最小的，ASC正序排序的话，NULL值是在最前面的 `mysql> select * from test_1 order by name asc;`

id    | name | create_at
----- |------| -------
3     | null | null
2     |      | null
5     |  lhw | null

解决办法：
```bash
select * from test_1 order by isnull(name) asc;
select *,name is null as username from test_1 order by username asc;
```

----------

### 流控制函数

### case语法：
```bash
CASE value WHEN [compare_value] THEN result [WHEN [compare_value] THEN result ...] [ELSE result] END

<=等价于=>

CASE WHEN [condition] THEN result [WHEN [condition] THEN result ...] [ELSE result] END
```
### if语法：

```bash
IF(expr1,expr2,expr3)

如果expr1为True，返回expr2，否则返回expr3

```
### ifnull语法：

```bash
IFNULL(expr1,expr2)

如果expr1为null，返回expr2

```
### nullif语法：

```bash
NULLIF(expr1,expr2)

如果expr1=expr2，返回null，否则返回expr1

```
----------

### 隐式转换

查询时，类型不对，导致类型转换，结果不对
`mysql> select * from test_2 where name=0;`

id    | name
----- |------
1     |  lhw
2     |  cxx
3     |  0

分析：很多开发在书写sql时，没有考虑到传入到数据库的值的类型，比如：上面name类型为char，而传入查询时，却输入的是0，而不是'0'
结果：mysql对匹配到行的字符串进行了类型转换，发现转换非法，转换成了0
注意：在查询时检查传入的查询条件类型

全表扫描，查询慢
`mysql> explain select * from xboss_account where mobile=15510337665;`

分析：mobile字段类型为char(11)，查询时，应该使用mobile='15510337665'
注意：在php端书写sql时，应正确传入查询值的类型
正确使用类型转换，防止SQL注入，其它的类型转换，感兴趣的可以自己研究，如：select '11aa' + 11,select a+1，select '1'+1等

----------

### Group by
group by一般和聚合函数一起使用才有意义，在MySQL5.7引入了ONLY_FULL_GROUP_BY对语法合法性进行检测

使用group by的两个要素：
出现在select后面的字段，要么是聚合函数中的，要么就是group by中的，若是该字段既不出现在聚合函数中，也不出现在后面的group by中，则该条select语句是错误的
若是需要筛选结果，可以先使用where，再用group by；或者先用group by，再用having

为什么要求使用聚合函数：
在未使用聚合函数的情况下，如果group by后面的字段不唯一，则会出现查询出来的数据不对

----------

### Sql mode问题

sql_mode通过检测sql的合法性来保证数据的一致性

常用到的设置：
STRICT_TRANS_TABLES
如果不能将给定的值插入到事务表中，则放弃该语句，对于非事务表，如果出现在单行语句或多行语句的第一行，则放弃该语句

NO_ENGINE_SUBSTITUTION
当使用create table或者alter table时，自动控制替换存储引擎

ONLY_FULL_GROUP_BY
实现sql语句合法性检测
对于group by column表达式，select列必须使用聚合函数，否则报错，如：
`select group_concat(id1),id2 from test_1 group by id2;`

----------

### 没有开启严格模式，引起的问题：

一 比如：freeze_score不为负数，即：unsigned
update `xboss_integrals` set `freeze_score` = 500-1000, `updated_at` = '2016-07-04 16:51:20' where `id` = '562';
此时freeze_score会被设置为0，warning警告不会被返回给api端，导致数据不一致

二 比如：mobile字段为int(11)
update zz set phone='188qwewq' where id=8;
此时phone字段会被设置为188，warning警告不会被返回给api端，导致数据不一致

----------

### JOIN语法

此处只说MySQL最常用的两种join类型，inner join 和 {LEFT|RIGHT} [OUTER] join 
在MySQL中的JOIN，CROSS JOIN和INNER JOIN和','在句法上相同,可以互相替换使用。但是在标准的SQL中，它们是不同的

inner join(内连接)：
将A表和B表中存在连接关系的字段，组成的结果集(A表和B表的交集)

left join(左连接,属于外连接):
以左表为准，去右表找数据，如果没有匹配的数据，则以null补位右表的数据，所以输出结果数为左表原数据集(right join同理)

tips：left join 可以转换为 inner join
```bash
select * from a inner join b on a.id=b.id;
<==>
select * from a left join b on a.id=b.id where b.id is not null;
```
----------

### 常用字符处理函数

`concat(str1,str2,...)`
把给定字符串合并为一个字符串,适用于多个数据列
`select concat(firstname,lastname) as fullname from user;`

----------

`concat_ws(separator,str1,str2,...)`
使用指定的分隔符将给定的字符串或列合并为一个字符串
`select concat_ws('-',id,name) from pp;`

----------


`ELT(N,str1,str2,str3,...)`
返回字符串列表的第几个元素，当N为1时，返回str1，为2时，返回str2，当N大于字符串列表长度时，返回NULL
`select elt(2,'hello','world');`
-->world


----------

`SUBSTRING(str,pos),SUBSTRING(strFROMpos),SUBSTRING(str,pos,len),` `SUBSTRING(str FROM pos FOR len)`
截取字符串，默认起始位置为1，结束位置为-1

从第五个字符开始截取
`SELECT SUBSTRING('Quadratically',5);`
-->ratically

从第四个字符开始截取
`SELECT SUBSTRING('foobarbar' FROM 4);`
-->barbar

从第五个字符开始截取，截取长度为6个字符
`SELECT SUBSTRING('Quadratically',5,6);`
-->ratica

从倒数第三个字符截取
`SELECT SUBSTRING('Sakila', -3);`
-->ila

从倒数第4个字符开始，截取长度为2个字符
`SELECT SUBSTRING('Sakila' FROM -4 FOR 2);`
-->ki


----------

`substring_index(str,delim,count)`
截取字符串，`substring_index('www.mysql.com','.',2)`，以逗号分隔，截取：www.mysql

`select substring_index(sitename,'.',-2) from siteinfo where id=2;` 截取结果为:mysql.com


----------


`mid(str,pos,len)`截取字符串,`mid('helloworld',7,12)`，截取第7个到第12个字符之间的字符串，结果为：world
`select mid(username,6,5) from userinfo;`


----------


`left(str,len)<=>right(str,len)`
截取字符串，`left('hello world',5)`，截取左边5个字符
`select right(username,5) from userinfo;`


----------


`replace(str,from_str,to_str)`
替换字符串
将username列中的zhang姓全部改为li姓
`update user set username=replace(username,'zhang','li');`


----------


`instr(str,substr)`
返回指定字符在字符串中的位置，`instr('welcome you baby,','you'),`返回位置为：9
`select instr(username,'shan') as pos from userinfo where uid=1;`


----------


`find_in_set(str,strlist)`
返回字符串在字符串列表中的位置，字符串列表必须以逗号','分隔，起始值为：1
`select find_in_set('shi','wo,shi,chinese') as pos;` 返回值为2


----------


`TRIM([{BOTH | LEADING | TRAILING} [remstr] FROM] str), TRIM([remstr FROM] str)`
移除指定的字符，both表示前后，leading：前面，trailing：后面
移除字符串前后的空格，如果不指定，默认为both，移除空格
`select trim(' bar ');`
-->bar


----------


移除字符串前面的x
`SELECT TRIM(LEADING 'x' FROM 'xxxbarxxx');`
-->barxxx


----------


移除字符串中的x
`SELECT TRIM(BOTH 'x' FROM 'xxxbarxxx');`
-->bar


----------


移除字符串后面的xyz
`SELECT TRIM(TRAILING 'xyz' FROM 'barxxyz');`
-->barx

###	 常用时间函数

CURDATE(),CURTIME()
以'YYYY-MM-DD'格式返回当前日期
select curdate();
-->2016-07-05


----------


以'YYYYMMDD'格式返回当前日期，+0表示+0天
select curdate()+0;
-->20160705


----------


以'HH:MM:SS'格式返回当前时间
select curtime();
-->15:17:30


----------


以'HHMMSS'格式返回当前时间,+0表示+0秒
select curtime()+0;
-->151855


----------


DATE(),TIME()
返回时间表达式的日期部分
select date(now());
-->2016-07-05


----------


返回时间表达式的时间部分
select time(now());
-->15:14:16


----------


DATEDIFF(expr1,expr2),TIMEDIFF(expr1,expr2)
返回expr1-expr2相差的天数，忽略时间部分
select datediff(now(),'2016-6-12 12:00:00');
-->23


----------


返回expr1-expr2相差的时间
select timediff(now(),'2016-6-12 12:00:00');
-->555:24:52


----------


FROM_UNIXTIME(unix_timestamp),FROM_UNIXTIME(unix_timestamp,format)
格式化unix时间戳成'YYYY-MM-DD HH:MM:SS' or YYYYMMDDHHMMSS格式

select from_unixtime(1467703739,'%Y %M %D %h:%i:%s')
-->2016 July 5th 03:28:59

select from_unixtime(1467703739);
-->2016-07-05 15:28:59


----------


TIMESTAMP(expr),TIMESTAMP(expr1,expr2)
当一个参数时，格式化为datetime类型，当为2个参数时，expr2将会对expr1的日期或时间部分进行相加

select timestamp('2016-07-03');
-->2016-07-03 00:00:00

SELECT TIMESTAMP('2003-12-31 12:00:00','09:00:00');
-->2003-12-31 21:00:00


----------


UNIX_TIMESTAMP()返回unix时间戳

select unix_timestamp('2015-08-12 13:57:51');
-->1439359071

DATE_FORMAT(date,format)，TIME_FORMAT(time,format)
格式化date成指定类型

select date_format(now(),'%Y-%m-%d');
-->2016-07-05


----------


DATE_ADD(date,INTERVAL expr unit), DATE_SUB(date,INTERVAL expr unit)
时间加减

加10分钟
select date_add(now(), interval 10 minute);
-->2016-07-05 15:57:17

减10分钟
select date_sub(now(), interval 10 minute);
-->2016-07-05 15:37:47

设置时间为24小时之前
set @time_before_24= (select date_sub(now(),interval 24 hour));

### 架构组件

### MaxScale

 1. 自动读写分离
 2. 自动剔除延迟超过30s的从库 
 3. 读负载 
 4. 最小化宕机时间
 
### MHA
 5. 自动故障探测，恢复
 6. 差异日志应用，保证数据一致性
 
### Pacemaker + Corosync

 7. 提供心跳检测和自动故障切换，保证MaxScale的高可用
 
### Box Anemometer

 8. 慢查询分析系统，每5分钟自动采集慢查询日志并汇总
 
### Binlog Server

 1. 负责中继Binlog，并加密复制给海口机房
 
### 数据安全

### 数据审核

明御数据库审计与风险控制系统提供准实时的数据操作审核记录

### 数据备份

 1. 每天全量备份并加密
 2. 每天单表备份并加密 
 3. 实时binlog增量备份 
 4. 灾备系统
 
<!-- http://o8979n2hu.bkt.clouddn.com/JBH%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9E%B6%E6%9E%84-v2.png -->

### Sphinx

### 规范

 1. 手机号：长度>=7个字符 
 2. 姓名：长度>=2个字符 
 3. 模糊查询结果统一最大取1W：limit=10000,maxmatches=10000
 
表：`xboss_sphinx_counts`
1. 解决后台分页（包含where条件的count，请直接去原库查询），例句：
`select count(*) from xboss_account;`
2. 已支持表：
`xboss_account,xboss_user_orders`
3. 例子（需要对xboss_account表进行count，并计算后台显示页数）：
`select table_rows from xboss_sphinx_counts where query='*xboss_account*'; `
xboss_account必须跟上*号

表：`xboss_account_sphinx`
1. 解决模糊查询和模糊join查询：

索引xboss_account表的true_name，mobile字段

### SphinxSE SQL语法：

语法：limit,offet,maxmatches

limit默认为20，offset默认为0，maxmatches默认为1000（rows）
例子：

```bash
select * from xboss_account_sphinx where query='*18810*;limit=2000;maxmatches=100';

-->输出100行结果

select * from xboss_account_sphinx where query='*18810*;limit=2000';

-->输出1000行结果
```


----------
引用索引字段

 1. '@'引用索引字段,如：@mobile，@true_name，| 表示 and，必须加上：mode=extended
 2. 如果没有指定，则检索所有的索引字段
 3. 下面查询true_name包含‘付’，且手机号包含‘18622’的用户
 
```bash
select b.true_name,b.mobile  from xboss_account_sphinx a join xboss_account b on a.uid=b.uid where b.is_auth='1' and a.query='@mobile *18622* | @true_name  *付*;mode=extended;limit=10000;maxmatches=10000' limit 15 offset 0;
```


----------

filter

 1. filter 只支持int类型
 2. 采用filter=字段名称,值就相当于where中的字段名=值，filter提到的字段必须在sphinx的source部分的字段属性定义中定义
 3. 例句：查询is_auth为1的用户，不等于：!filter=is_auth,1，范围：filter=is_auth,1,2,3
 
```bash
select b.true_name,b.mobile,b.created_at,b.updated_at  from xboss_account_sphinx a join xboss_account b on a.uid=b.uid where a.query='@mobile *18622* | @true_name *付*;mode=extended;limit=10000;maxmatches=10000;filter=is_auth,1' order by b.created_at limit 15 offset 0;
```

### 分页（在外部实现）

```bash
select a.*,b.mobile,b.true_name from xboss_wallet_records a join xboss_account b join xboss_account_sphinx c on a.uid = b.uid and a.uid = c.uid where a.`type_new` != '4' and c.query='*1881048*;limit=10000;maxmatches=10000' order by a.id desc limit 15 offset 0;
```

### and或or查询 

and查询，用|分隔

```bash
set names utf8;

select * from xboss_account_sphinx a join xboss_wallet_records b on a.uid=b.uid where a.query='@mobile *18622* | @true_name *付*;mode=extended;limit=10000;maxmatches=10000'\G;
```

### or查询

```bash
select b.username from xboss_account_sphinx a join xboss_wallet_records b on a.uid=b.uid where a.query ='*1881048*;limit=10000;maxmatches=10000' or a.query='*付*;limit=10000;maxmatches=10000'\G;
```

