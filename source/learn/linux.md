cpu 平均负载

压测工具 ，利用cpu盒数。


ps -ef | grep -v grep | grep marketApi | awk '{print $2}' | xargs kill -9



nc,telnet,lsof,ping


1. ping

ping: icmp 协议 非ip,发的是 ICMP包。


ping 的输出，可以分为两部分。

第一部分，icmp_seq 是序号，ttl 是生存时间


第二部分，则是三次 ICMP 请求的汇总。

终点： rtt也就是从发送 ICMP 开始，到接收到 回复的确认，总共经历时间。


我们通常使用带宽、吞吐量、延时等指标，来衡量网络的性能；相应的，你可以用 ifconfig、netstat、ss、sar、ping 等工具，来查看这些网络的性能指标。


