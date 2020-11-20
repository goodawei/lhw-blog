1. go 的传值是值传递，也就是所谓的值拷贝，不会影响源值，值得注意的是，对于引用类型，传值拷贝的源值的地址，会影响源值。

对于slice的拷贝还要分情况，当对slice进行进行append操作的时候会触发扩容，扩容后会申请新的地址，就不会影响源值了。


```go
var lgs = []string{
	"php","java",
}

func main()  {
	handle(lgs)
}

func handle(lgs []string)  {

	lgs[1] = "dddd" //影响源值

	k := append(lgs,"python") //不影响源值

	fmt.Print(lgs)

	fmt.Println(k)
}
```


2. chan 


注意死锁，或者就阻塞。

可以把chan就理解为一个队列，所有的go程可以在这个队列交换数据，是线程安全的。


3. go协程

go协程 + chan 构成了gsp，与传统方式不同，传统方式是：在并发访问共享数据的时候都会封装成为线程安全的方法，也就是通过共享内存去通信。

gsp,实现的是通过通信去共享内存。


4. gsp


5. MPG







