---
title: redis 主从配置
date: 2020-01-01 22:12:34
tags: redis
---

- docker pull  redis


启动master并打开redis持久化配置

- docker run -p 6379:6379 -v $PWD/redis:/data  -d redis redis-server --appendonly yes

查看容器信息

- docker inspect d7c20d48d35c | grep IPAddress

内网IP： 172.17.0.2


启动slave1 
- docker run --name redis-6380 -p 6380:6379 -v $PWD/redis:/data -d redis redis-server

内网IP： 172.17.0.3


启动slave2
- docker run --name redis-6381 -p 6381:6379 -v $PWD/redis:/data -d redis redis-server

内网IP： 172.17.0.4


进入2个从节点，修改配置。
- docker exec -it c19b7c8c6ed1 /bin/bash

```php
info replication			#查看角色

SLAVEOF 172.17.0.2 6379		#设置master
```

#### 配置Sentinel

分别进入3个容器配置 创建 sentinel.conf

```php
sentinel monitor mymaster 172.17.0.2 6379 1
```

每个节点启动 `redis-sentinel ./sentinel.conf`

首先查看哨兵监控情况,然后尝试关闭主容器,再查看剩余2个从机，这里会自动选举产生新的主机，然后，我们再次将刚才停止的主机启动起来，发现启动后其自动成为从机。





