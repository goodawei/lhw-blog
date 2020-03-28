cpu 负载

压测工具 ，利用cpu盒数。


ps -ef | grep -v grep | grep marketApi | awk '{print $2}' | xargs kill -9

