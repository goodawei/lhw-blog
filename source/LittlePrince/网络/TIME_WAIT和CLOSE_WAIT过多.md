1. netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'

我们用netstat可以查看到目前的tcp连接中，各个状态的数量，最常见的问题是，TIME_WAIT和CLOSE_WAIT状态的数量过多了，严重占用端口资源。

众所周知，TCP连接是三次握手，四次挥手的状态。当客户端或服务器其中一方想主动关闭连接时，主动关闭方就会进入FIN_WAIT1状态，对方收到FIN包后进入CLOSE_WAIT状态，返回返回ACK包。被动关闭方，需要调用系统的close方法回收socket资源，这个时候系统发送FIN包给主动关闭方，并且被动关闭方进入到LAST_ACK状态。主动关闭方收到FIN包后进入到TIME_WAIT状态，并回复ACK包，然后超时2MSL个时间(大概为2分钟)后，主动关闭方进入到CLOSED状态，彻底回收socket资源。被动关闭方收到ACK包后，从LAST_ACK状态进入到最终的CLOSED状态。

经过上面冗长的分析，我们得到两个要点：
	
	1. TIME_WAIT状态只在主动关闭方出现，这个主动关闭方可能是客户端，也有可能是服务器。TIME_WAIT状态的消失只能通过2MSL时间转换为CLOSED状态后消失。无法人工删除，因为这是一个非常谨慎的TCP设计方案，最好不要通过修改系统参数来避免这个2MSL的等待时间。

	2. CLOSE_WAIT状态就简单得多，就是被动关闭方收到主动关闭方的FIN包时就会进入，只要被动关闭方调用close关闭socket就能马上进入LAST_ACK状态。

好了，经过以上的分析，我们知道，TIME_WAIT会过多的原因只有一个，就是本地主动关闭的连接太多了，常见的具体原因是：

	1. http.Client中没有设置MaxIdleConnsPerHost，如果你的httpclient后端只有有限的几个host的服务器，由于默认的MaxIdleConnsPerHost只设置为2，这代表，大部分的持久连接都会在完成请求后，会被http.Client主动关闭，导致大量的TIME_WAIT事件发生。如果MaxIdleConnsPerHost设置得比较大，这些连接请求完成后则会被http.Client放进连接池中留作下次使用，不会去主动关闭，大大减少短连接的使用，避免了TIME_WAIT事件的发生。	

	2. http.Server中直接连前端的浏览器，没有经过中转网关。这样会导致大量设计不良的爬虫直接使用短连接连接server。由于这些请求都是带上Connection: close参数，导致http.Server主动关闭这些短连接，使得服务器大量留下了TIME_WAIT状态。解决方法很简单，让http.Server前面建立一个nginx网关，将短连接转换为长连接来连接后端的golang服务，这样这些TIME_WAIT状态会耗费在网关层，而不是在服务层。

	3. http.Server中连接第三方服务时没有使用连接池，第三方服务例如redis，mysql,rabbitmq这种，然后每个操作都是使用短连接来操作，用完就主动关闭，这样会大大增加server端的TIME_WAIT状态，耗费了大量的端口资源。解决办法，就是用连接池了，没什么好说的。

最后一个，CLOSE_WAIT状态过多，这个最容易解决了，就是服务器或客户端接收到对方主动关闭的消息后，马上调用close释放socket资源就可以了。CLOSE_WAIT状态过多都是由于，程序考虑不够充分，没有调用close释放socket资源导致的。常见的情况是，服务器使用websocket或者long-poll做推送服务，当前端主动关闭浏览器后，服务器忘了立即close对应的socket资源，导致CLOSE_WAIT太多了。
