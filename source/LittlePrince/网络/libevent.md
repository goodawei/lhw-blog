针对反应式编程的封装，依赖linux 2.6以上内核提供的多路复用 epool 接口。

nginx.

redis.

workman.

swoole: 通过携程实现了 异步mysql、异步redis 等，如果其他的网络连接如kafka，或者发起一个http请求，依然是同步的。

fpm： 多进程模型，但是每个进程都是同步的， 可以使用 libevent，实现fpm 的协程，比如封装http的请求。应用程序在调用http发起网络请求时候，对方没有数据到来时，可以让出cpu。上下文切换。