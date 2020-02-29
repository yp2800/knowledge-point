# go并发 MPG 模式
## MPG模型
* S (Sched): 结构就是调度器，它维护有存储的 M 和 G 的队列以及调度器的一些状态信息等。
* M (Machine)：一个 M 直接关联了一个内核线程
* P (Processor)：代表 M 所需的上下文环境，也是处理用户级代码逻辑的处理器。
* G (Goroutine): 其实本质上也是一种轻量级的线程。

#### 参考
* [Go并发(二)：goroutine的实现原理](https://zhuanlan.zhihu.com/p/82740001)
* [Golang 的 goroutine 是如何实现的](https://www.zhihu.com/question/20862617)