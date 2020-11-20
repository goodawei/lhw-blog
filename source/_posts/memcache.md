---
title: memcache
date: 2016-05-29 19:20:44
tags: memcache
categories: memcache
---
---

# memcache 一致性hash

---

```php
// memcache一致性hash实现,为了解决多台memcache服务器setcache的时候增加或删除节点导致,缓存瞬间命不中问题 通话hash算法将节点映射到一个圆环上

// 定义循环链表 长度 为2的32 次方
$circle = array(0,1,2,3,4,5,6,7,8,9,10,11);

//将3台memcache 服务器节点映射的圆环上
$circle['3'] = '192.168.10.1';
$circle['6'] = '192.168.10.2';
$circle['9'] = '192.168.10.3';

//建立cache请求
$key = 2;
foreach ($circle as $key=>$val){
	if(strlen($circle[$key])!=1){
		$cache->connect($circle[$key],'11211');
		$cache->set($key,'val');break;
	}
}
```
<!-- more -->
1、一致性hash算法只是帮我们减少cache集群中的机器数量增减的时候，cache的数据能进行最少重建。只要cache集群的server数量有变化，必然产生数据命中的问题
2、对于数据的分布均衡问题，通过虚拟节点的思想来达到均衡分配。当然，我们cache server节点越少就越需要虚拟节点这个方式来均衡负载。
3、我们的cache客户端根本不会维护一个map来记录每个key存储在哪里，都是通过key的hash和cacheserver（也许ip可以作为参数）的hash计算当前的key应该存储在哪个节点上。
4、当我们的cache节点崩溃了。我们必定丢失部分cache数据，并且要根据活着的cache server和key进行新的一致性匹配计算。有可能对部分没有丢失的数据也要做重建...
5、至于正常到达数据存储节点，如何找到key对应的数据，那就是cache server本身的内部算法实现了，此处不做描述。
这里只是针对数据的存储方式以及提取方式进行了流程展示。
