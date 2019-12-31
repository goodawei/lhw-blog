---
title: OpenResty 搭建 Api Gateway
date: 2019-12-30 21:52:10
categories: OpenResty
---

### What

- 基于Nginx和lua的高性能Web平台，内部集成了大量精良的lua库，第三方模块以及大多数依赖项，方便搭建处理高并发、扩展性极高的动态web应用、web服务，动态网关。

- 扩展了Nginx的能力，将Nginx有效的变成了一个强大的web应用平台，web开发人员可以使用lua脚本调动nginx支持的各种c以及lua模块。快速构建出足以胜任c10k乃至c100k以上的单机并发连接的功性能web应用系统。

- 充分利用了Nginx的非阻塞的I/O模型，不仅对HTTP请求，甚至对于远程后端诸如`Mysql`、`PostgreSQL`,`Redis`等都进行了一致高性能相应。



### 使用场景

1. 网关&web防火墙：如数据前置校验，缓存前置，数据过滤，API请求聚合，ABtest,降级，监控等，可以进行 IP/URL/UserAgent/Referer 黑名单，限流等。

站内应用案例：

![iterm2][1]

[1]: http://cdn.nicexiangcun.com/Canvas%201.png


<!-- more -->

图中golang注册中心服务负责将后台提交的规则信息以插件化的形式进行打包处理后提交至gateway管理中心，gateway加载插件处理流量。


2. web应用：会进行一些业务逻辑处理，一般流程 mysql/redis/http获取数据返回前端，如京东商品详情页。

利用`OpenResty`对于后端的远程存储可以进行高性能相应，可以代替类似于`php-fpm`对后端远程存储访问瓶颈的限制，转为由`OpenResty`请求，获取结果后通知给应用后端服务。

```c
docker pull redis:latest
docker run -p 6379:6379 --name redis -v $PWD/redis.conf:/root/redis/redis.conf -v $PWD/data:/root/redis/data -d redis redis-server
```

```lua
         location /lua {
                content_by_lua_block {
                    local redis = require "resty.redis"
                    local red = redis:new()
                    red:set_timeout(1000) 
                    local ok, err = red:connect("127.0.0.1", 6379)
                    if not ok then
                        ngx.say("failed to connect: ", err)
                        return
                    end
                    ok, err = red:set("color", "red")
                    if not ok then
                        ngx.say("failed to set dog: ", err)
                        return
                    end
                    ngx.say(red:get("color"))
            }
        }
```

3. 缓存：可以对相应内容进行缓存，减少后端请求，以及规则下发。


`OpenResty` 提供了lru和shared纯内存缓存服务。

```lua
        lua_shared_dict books 1m;
        location /book {
            content_by_lua_block {
                local cache = ngx.shared.books 
                local len, err = cache:lpush("foo", "bar") 
                if len then
                     ngx.say("push success") 
                else
                     ngx.say("push err: ", err) 
                end
                cache:set("lua", "lua")
                ngx.print(cache:get("lua"))
                cache:set("kind", require("cjson").encode({php=7})) 
                ngx.print(require("cjson").decode(cache:get("kind")).php)
            }
        }
```

```lua
        location /lru {
              content_by_lua_block {
                local lrucache = require "resty.lrucache"
                local cache, err = lrucache.new(200)
                cache:set("dog", 32, 0.03)
                ngx.sleep(0.02)
                local data, stale_data = cache:get("dog")
                ngx.print(stale_data)
              }
        }
```

```lua
OpenResty -s quit -p `pwd`/ -c conf/nginx.conf

OpenResty -p `pwd`/ -c conf/nginx.conf
```






