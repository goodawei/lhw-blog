Linux系统在访问设备的时候，存在以下几种IO模型：

Blocking IO Model，阻塞IO模型；
Nonblocking I/O Model，非阻塞IO模型；
I/O Multiplexing Model，IO多路复用模型;
Signal Driven I/O Model，信号驱动IO模型；
Asynchronous I/O Model，异步IO模型；


在Linux中是通过select/poll/epoll机制来实现的。

阻塞IO模型：在IO访问的时候，如果条件没有满足，会将当前任务切换出去，等到条件满足时再切换回来。
缺点：阻塞IO操作，会让处于同一个线程的执行逻辑都在阻塞期间无法执行，这往往意味着需要创建单独的线程来交互。
非阻塞IO模型：在IO访问的时候，如果条件没有满足，直接返回，不会block该任务的后续操作。
缺点：非阻塞IO需要用户一直轮询操作，轮询可能会来带CPU的占用问题。


select/poll 都要轮寻文件描述符集合，select是在用户空间轮询，文件描述符限制在1024，而poll是在系统内核轮询没有文件描述符限制。