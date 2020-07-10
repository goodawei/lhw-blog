主要了解业界这些优秀产品，在分布式一致性算法以及在整个微服务架构领取所起到的作用。


创建一台agent,修改默认 http port。

consul agent -bootstrap-expect 1 -server -data-dir /data/consul_data -node=swoft01 -bind=0.0.0.0  -enable-script-checks=true -datacenter=sunny -client=0.0.0.0 -ui -http-port=2222


测试一下服务注册，服务删除，kv配置中心。


如何搭建一个集群。集群之间如何工作。


-----


基础容器centos 装了 consul 主要要多留出几个端口用：

	docker run -it -v /Users/lihongwei/docker/data:/data --name myphp -p 8081:80 -p 33006:3006 -p 881:881 9aec5c5fe4ba	

	2222/tcp, 0.0.0.0:881->881/tcp, 55555/tcp, 0.0.0.0:8081->80/tcp, 0.0.0.0:33006->3006/tcp [2222 的失败了，不知道为啥]



