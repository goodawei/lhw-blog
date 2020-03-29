主要了解业界这些优秀产品，在分布式一致性算法以及在整个微服务架构领取所起到的作用。


创建一台agent,修改默认 http port。

consul agent -bootstrap-expect 1 -server -data-dir /data/consul_data -node=swoft01 -bind=0.0.0.0  -enable-script-checks=true -datacenter=sunny -client=0.0.0.0 -ui -http-port=2222


测试一下服务注册，服务删除，kv配置中心。


如何搭建一个集群。集群之间如何工作。

