---
title: 大型网站技术篇之负载均衡
date: 2017-04-17 22:03:03
tags: 大型网站
---

### 架构图


![架构图][1]

[1]: http://o8979n2hu.bkt.clouddn.com/%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.jpg


### 负载均衡工作方式

![lvs][2]

[2]: http://o8979n2hu.bkt.clouddn.com/lvs.png

#### http 重定向＝》转移请求

#### DNS 负载均衡

多机房部署，就近访问。可以查看google的dns配置 ： `dig google.cn` 可以产看 `google.cn` 的域名后面配置的`IP`

#### 反响代理负载均衡

工作在 http层（七层）常用 nginx做代理软件

#### IP负载均衡

工作在 传输层 (四层转发负载) ，通过转发ip数据包方式，缺点是所有数据均要通过负载机器，负载机器的网络带宽会逐渐成为瓶颈

#### LVS

工作在 数据链路层（二层）通过虚拟IP转发webserver,2⃣webserver处理完成后直接响应给客户端

### 负载均衡常见策略

- 轮询
- 加权轮询
- 最小链接数
- ip hash



