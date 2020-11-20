redis 为什么快。

redis 采用单线程，I/O多路复用技术架构。

单线程：没有锁，没有线程切换，存内存。

网络I/O：非阻塞IO，使用epoll多路复用，应用成调用系统命令 event_loop() 监听可用网络描述符。

简单可靠的传输协议。


redis 常用的5种数据结构其底层都至少提供了2种以上的内部编码。

查看一个key底层用的内部编码实现：object encoding key

string: raw,int,embstr

hash: ziplist,hashtable

list: ziplist,linklist,quicklist

set: hashtable,intset

zset: skiplist,ziplist


字符串： 最大存储512MB

内部编码：

int: 8个字节长整型
embstr: 小于等于39个字节的字符串
raw: 大于39字节的字符串

redis 会根据所要存储内容的类型和长度来决定使用哪种内部编码，所以要学会根据以上范围限制合理规划设计要存储的key的内容。


hashtable:  用hscan 渐进式遍历

内部编码：

ziplist: 元素个数且所有元素大小都要小于配置，元素个数默认：512个，大小默认64个字节。 优点内存连续，缺点扩容。

hashtable: 

list:

内部编码：元素个数且所有元素大小都要小于配置，元素个数默认：512个，大小默认64个字节。 优点内存连续，缺点扩容。

ziplist: 

linklist:

quicklist:


lpush + lpop = 栈

lpush + rpop = 队列

lpush + brpop = （listen1,listen2）消息队列



set: sadd O(1)  scan O(1) 常用去重，取交集，并集等。


内部编码： 

intset 当集合中元素类型都是整数型且小于默认配置512个的时候使用。

hashtable:


zset: zadd O(Log(n))  zrank()  zrevrank()  zincrby key 给key 增加分数

zadd() 为什么是log(n) 内部编码是采用跳表实现的，原理是: 每次在做增加的时候，会通过算法对该元素随机出一个层级，默认会有32层，每一层都是一个链表。



持久化： rds,aof

rds: 优点是二进制文件，占用存储空间小，恢复数据速度快，缺点是无法达到实时备份，容易丢失数据，因为bgsave每次运行都会调用fork，而fork属于系统调用，频繁调用成本很高，更适合做数据备份。

aof: 基本能保证不丢失数据，占用存储空间。

rds： 主线程fork 子线程利用写实复制 子线程拷贝主线程内存快照。

aof: 主线线将写命令写入aofbuf,由aofbuf到磁盘有很多种策略,单独一个线程每秒 一次 fsync 写入磁盘。aof 重写 aof 文件会fork 子线程，同理利用 copy on write 写新文件，成功后由主线程完成替换。




高可用方案：自身的复制，setnel, 加上一致hash

redis主从复制的实现原理:

CAP理论是分布式领域的牛顿定律，所有的分布式存储中间件都要使用它作为理论基石:网络分区发生时(分区容忍性)，一致性和可用性两难全。

分布式的redis，并不满足一致性，当网络断开是主节点要仍然能够对外提供服务，追求的是可用性，从节点会努力追赶主节点数据，尽量满足一致性。

互联网圈经常谈“三高”架构：高并发、高性能、高可用.



使用tips：

redis 提供了许多一次性获取多个key的值方法，其目的是为了减少对服务的网络请求提高效率，可以注意并利用这些批量操作。

前提是一定要了解key的元素数量，这些都属于比较重的命令，很容易导致redis阻塞。



redis 的生命线 噩梦: 阻塞。


每一条命令都要考虑一下，如过阻塞了，会对其他的服务有多大影响，可以采用分组形势，将不同等级的服务隔离开来。

添加报警机制，设置阀值。


找大key命令： client 下执行： --bigkeys  --stat 


