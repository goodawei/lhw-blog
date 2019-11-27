---
title: nginx
date: 2016-05-15 13:19:51
tags: nginx
categories: webserver
---

# nginx 信号控制

---

### 查看服务端口列表项
`netstat -antp` (p是显示那个进程 和进程号)

### 查看进程
`ps -aux | grep 80 ` （httpd）

### 信号控制
   使用参数`INT`是紧急杀掉进程非常暴力（特殊场景不推荐），`QUIT` 优雅的杀掉进程，等待`work`主进程接收处理所有请求后在杀掉。`HUP`平滑的读取配置文件，不用重启nginx就能使配置文件生效（很重要），`USR1` 告诉nginx 写入log 往新建的文件中写（在备份log日志时必须要用，因为`linux`中的文件存放地址是通过`innode`号存储的，当mv 备份文件的时候只是变化名字而已，而`innode`号依然不变，所有nginx依然往原始的`innode`号文件写入日志，此时需要用信号量`USR1`）。
  
<!-- more -->
   
`kill -INT 进程号`  杀掉nginx进程，`ps -aux | greap nginx`查看进程号

小技巧：有时候经常需要查看 nginx 主进程号， 可以查看pid的存放文件，这样用的时候直接如： 
```bash
kill -INT  'cat  /usr/local/var/niginx/log/nginx.pid'
```
### nginx第二种启动方法
nginx  -s reload 重启 
nginx -s stop 停止  
nginx -s reopen 重新读取配置文件 
nginx -t  查看配置文件配置是否正确

### 虚拟主机配置
Nginx 的虚拟主机就是配置 http 中的 server 块。
可以基于 域名、端口、和ip 主要 三项： 
`listen、server_name、location{ root:}`

Location 定位配置块 三种:

 1. 精准匹配（精准匹配最好不要匹配跟目录，会默认追加索引文件可能导致多重定位，如循环重定向死循环等）
 2. 正则匹配
 3. 普通匹配：普通匹配优先级最高，言外之意是正则会覆盖普通匹配的结果 ，普通匹配如果命中多个则最长的匹配结果先记下来，然后走正则匹配如果正则没有匹配到这返回这个最长的匹配结果，静态文件的动静分离 可以用 location 将静态文件 放到 应用以外的目录 然后用 loaction 正则定位。
	备注：当发现 404 时 要记住看下log 日志 是去哪个目录没找到 非常有用 。

### 日志管理：
首先日志可以辅助排错，`nginx`可以针对不同的`server`配置不同的`log`位置和`log`日志格式 ：默认是main 格式，大概是 来访者ip ，访问方式 …..

一个日志的切割管理bash:
```bash
#!/bin/bash
logpath = /usr/local/nginx/log/access.log
topath  = /data/log/$(date -d yesterday + "%Y%m")
mkdir -p $topath
bathpath = $topaht/$(date -d yesterday + "%Y%m%d").log
mv $logpath $bathpaht
touch $logpath
kill USR1 /usr/run/nginx.pid

sh ./run.sh
```