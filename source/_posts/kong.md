title: 基于docker搭建kong分布式集群
date: 2020-01-07 21:52:10
categories: OpenResty
---


#### 创建自定义网络

`docker network create kong-net`


#### 安装kong依赖的数据库

```c
docker run -d --name kong-database \
     --network=kong-net \
     -p 5432:5432 \
     -e "POSTGRES_USER=kong" \
     -e "POSTGRES_DB=kong" \
     postgres:9.6
```


```c
docker run --rm \
     --network=kong-net \
     -e "KONG_DATABASE=postgres" \
     -e "KONG_PG_HOST=kong-database" \
     kong:latest kong migrations bootstrap
```

<!-- more -->


#### 安装2台kong节点


```c
docker run -d --name kong-node1 \
     --network=kong-net \
     -e "KONG_DATABASE=postgres" \
     -e "KONG_PG_HOST=kong-database" \
     -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
     -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
     -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
     -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
     -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
     -p 8000:8000 \
     -p 8443:8443 \
     -p 8001:8001 \
     -p 8444:8444 \
     kong:latest



docker run -d --name kong-node2 \
     --network=kong-net \
     -e "KONG_DATABASE=postgres" \
     -e "KONG_PG_HOST=kong-database" \
     -e "KONG_PROXY_ACCESS_LOG=/dev/stdout" \
     -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout" \
     -e "KONG_PROXY_ERROR_LOG=/dev/stderr" \
     -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" \
     -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl" \
     -p 8446:8000 \
     -p 8447:8443 \
     -p 8448:8001 \
     -p 8449:8444 \
     kong:latest
```


#### 安装两台nginx代理

kong 默认的管理端是口8001，对外的端口是8000和8443，这里使用一台nginx代理转发到管理端口,一台转发到对外的访问端口.


`docker run --name nginx-node1 -p 8085:80 -d nginx`

`docker run --name nginx-node2 -p 8086:80 -d nginx`


修改nginx-node1的location

```c
    location / {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Bitz_Client_IP $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_ignore_client_abort on;
        proxy_pass  http://henry;
    }
```

henry.conf

```c
upstream henry {
    server 10.0.208.62:8001; //admin port 
    server 10.0.208.62:8448; //admin port
    #check interval=3000 rise=2 fall=5 timeout=1000 type=http;
    #check_http_send "HEAD /op/status HTTP/1.0\r\n\r\n";
    #check_http_expect_alive http_2xx http_3xx;
}
```


修改nginx-node2的location

```c
    location / {
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header Bitz_Client_IP $remote_addr;
        proxy_set_header X-Forwarded-For $remote_addr;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_ignore_client_abort on;
        proxy_pass  http://proxy_for_api;
    }
```

proxy_for_api.conf
```c
upstream proxy_for_api {
     server 10.0.208.62:8000;
     server 10.0.208.62:8446;
     #check interval=3000 rise=2 fall=5 timeout=1000 type=http;
     #check_http_send "HEAD /op/status HTTP/1.0\r\n\r\n";
     #check_http_expect_alive http_2xx http_3xx;
}
```

#### 缓存


#### 扩展 admin api 




#### 查看容器状态

 
 
```c
docker container list -a

docker logs d402e041de4f

docker restart d402e041de4f

docker cp  ./http_headers.lua d402e041de4f:/usr/local/openresty/lualib/resty/

docker cp d402e041de4f:/usr/local/share/lua/5.1/kong/constants.lua ./

```