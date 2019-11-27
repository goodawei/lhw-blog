---
title: For a interview
date: 2013-12-04 10:49:07
tags: interview
categories: interview
---

### Experience of myself

I begin whit php language technology since 2013, My first company is belong to Tv media , in this company I do something about App ,for example game , shoping.

My responsibility of woke is provide Api interface for App,and other technology support.

Used technology and key point:

design a rule of awards are be selected a key field weight scope zero to one 

and rand one to this ceil weight.

shop...I used httpsqs queue or Redis sub and pub ....

下单如队列减库存，前端轮询结果成功后导向支付网关。

-------

Wangcaigu is a finance and investment platform,since 2014 join this company.

server 服务提取，重构平台核心服务：充值，提现，投资，放款，还款

client soap 

------

2016 join eking technology,I do something include data migrate.

microservice : 微服务拆分前端解藕工具

rest：unit , floor , facde 

controll version : 版本管理原则上新需求都是新增接口，设计到老接口，严重bug强制更新，老的需要改进升级会在文档鲜明标注内容变化。对于准备废弃的接口，友好提示各个调用方并说明摒弃原因和支持调用方时间范围。


路由中间件, 验证器,加解密服务,数字签名,日志管理

API Gateway搭建, 引入接口防刷中间件

使用dingo/api

------

### About Mysql

索引原理：B+tree 的数据结构（多路搜索树）充分利用的上磁盘的预读性，因为磁盘的读写通常上代价比较高的因为它的存储分为inode，blocks,block,sector,磁盘每次读取最少要读以个 block 4kb,那么对于多路搜索树来讲，它渴望的是每读到一个节点关键字，要尽可能的多带出一些关键字出来，因为读一个也是4kb，所以多路搜索树的特点在分型上要比二叉要多的。

无论上二叉搜索 还是 多路搜索 ，总之为了查询效率，都要维持树的平衡。

B tree: 维持平衡的方法上让叶子节点都这同一层：

以一个4路多路搜索举例：

4路的话，那么叶子节点的关键字理论上是不超过4个，所以如果新增的节点上超过4个那么就要重新分型，分型的方法是把5的这个节点从中间的一分为2，左边两个单独一个节点，右边两个单独一个节点，而中间的分到父节点，如果父节点超过4个同样从中间一分为2，一次类推。以这样的分型方式让树达到平衡。

而mysql的索引是一种变种的B-tree,他在建立分型的过程中，不断的将所有的索引值都一一的放到了叶子节点上，并且从左到右是有序的。对于mysim来讲，它的所有叶子节点存储的都是数据这磁盘的索引位置。而对于innodb来讲它的数据就存储在叶子节点上，找到叶子节点就直接取出数据，所以说索引也是非常占用磁盘空间的。尤其说innodb引擎。

面试关键词：Mysim 索引说两个文件 InnoDb 聚簇索引一个文件。

------

sql 优化相关：

事务使用的合理性，使用不当或者滥用事务。避免大事务。

建立适合的索引

避免隐式转化，数据类型要对应。

尽量使用主键。

------



### About Php,Web

php 加载规范 命名空间，我的理解是，为了不污染重名的变量和类名，从防御式编程角度理解 namesapce就上一个盒子，所有的声明的class 和变量只用在自己的盒子内有作用。如果想使用其它盒子的class要通过use关键字加载过来。

需要PHP框架加载方式一般都采用了composer，而composer 也是遵循 psr-4,同时还兼容了psr-0的加载规范，通常composer会缓存所有namesapce下的所有class到一个array中。

PHP 脚本执行原理：web 客户端 和服务端ngixn or appache遵循http协议，而nginx会和php解析器有一个cgi协议，当然现在一般都上fastcgi协议。当一个url通过dns找到服务端80端口后会交给nginx的mast进程启用的work进程,然后在交给9000端口的pfm进程池，最终fpm处理后返回。

---

进程，线程，协程

----

PHP7 new feture

----

memcache:内存型，内存维护一个巨大的hash，最大存储1m （slap 页），不支持持久化没有磁盘落地。最长存30天。

reids: 事务 mulit 开启 ， exec 执行， watch=>key 的变化。


### About Nginx , Linux 

操作系统提供的socket api,是统一的，所以各个语言实现的socket编程也都上大同小异。

PHP socket 创建步骤：

```php
    set_time_limit(0);
    
    //AF_INET 直定交互方式为ip,SOCK_STREAM 指定tcp流。返回的是一个数组下标
    $socket = socket_create(AF_INET,SOCK_STREAM,0);
    
    socket_connect($socket , '127.0.0.1' ,8888);
    
    while(1){
        $data = readline();
        
        socket_write($socket,$data,strlen($data))
        
        if($data == 'quit'){
            socket_close($socket);
            die('over');
        }
        
        $buf = socket_read($socket , 1024);
        
        echo $buf;
    }

```

```php
 set_time_limit(0);
 
 $listenfd = socket_create(AF_INET,SOCKET_STREAM,0);
 
 //阻塞方式
 socket_set_block($listenfd);

 // 与某一个 ip进行绑定
 socket_bind($listenfd, '127.0.0.1' , 8888);
 // 等待client链接，15 指的是最多放15 个 client 到队列
 socket_listen($listenfd , 15);
 // 从队列取出一个执行，如果队列没有就阻塞到这,IO 的多路复用就是针对这种情况产生的 分为 epoll 和 select ,操作系统提供的api.
 $connfd = socket_accept($listenfd);
 
 while(1){
    $buf = socket_read($connfd,1024);
 
    if($buf == 'quit'){
        socket_close($listenfd);
        
        socket_close($connfd);
        
        die('over');
    }
    
    
 }
 
 $msg = 'get:'.$buf."\r\n";
 
 socket_write($connfd,$msg,strlen($msg));
```


ab -c 100 -n 100 

### Data struct

数据结构，要充分考虑硬件资源包括磁盘，内存等。其目的上读写速度快，复杂度低。

链表 和 数组 的一些区别：

首先数组以c语言举例，在声明一个数组时必须指定其大小和类型，然后会在内存中开辟一个连续的此大小的内存空间来存储，注意是连续的空间，这样带来的问题是扩容的复杂度大大提高，因为当你需要扩大这个存储的时候，计算机要重新申请一个更大的连续空间，然后将原来的copy到新的存储空间，在将原来的销毁掉。在java中声明数组可以使用其ArrayList.

链表:链表的区别在于，其内部结构的指针域指向的并不是连续的内存地址，只要知道其中一个node,就知道下一个地址，所以扩展方便。但是链表取值的效率上不如array效率高。

树

二叉平衡树：最底层以上的叶子节点都是完整的

满二叉树：叶子节点都上满的

搜索树：左子树 和 右子树 有规律的

binary search ： 对于有序的组数据采用二分查找，时间复杂度log/n

非波拉妾数列：