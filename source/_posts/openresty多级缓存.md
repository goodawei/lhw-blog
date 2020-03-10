---
title: OpenResty的多级缓存库
date: 2020-01-07 21:52:10
categories: OpenResty
---

#### lua-resty-mlcache

这个库可以作为 key/value存储来处理，但是可以缓存Lua类型和表，但是构建在lua_shared_dict，lua-resty-lrucache上 ，这种组合可以实现高性能和灵活的缓存。

功能：

- 使用TTL缓存和负缓存，
- 通过lua-resty-lock构建互斥体，以防止在缓存失败时将狗堆影响到数据库/后端，
- 内置worker通信，传播缓存失效，并允许worker更新他们的L1 (lua-resty-lrucache )缓存的更改(set()，delete() )，
- 可以创建多个独立实例来保存各种类型的数据，同时依赖同一lua_shared_dict L2缓存，

此库中内置的各种缓存级别的说明：

![lrucache][1]

[1]: http://cdn.nicexiangcun.com/lua-resty-lrucache.jpg


 缓存级别层次结构为：

 - L1 ：使用Least-Recently-Used land缓存lua-resty-lrucache ，在填充时提供最快的查找，并避免耗尽worker的Lua虚拟机内存，

 - L2 ：所有员工共享的lua_shared_dict内存区域，只有L1未命中时才能访问此级别，并防止工作人员请求L3缓存，

 - L3 ：自定义函数，只由单个worker运行，以避免数据库/后端(通过lua-resty-lock )上的狗堆效应，通过L3获取的值将被设置为L2缓存，供其他员工访问，



