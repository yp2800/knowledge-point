# go并发 MPG 模式
## MPG模型
* S (Sched): 结构就是调度器，它维护有存储的 M 和 G 的队列以及调度器的一些状态信息等。
* M (Machine)：一个 M 直接关联了一个内核线程，其中 Go程序启动时，runtime会去启动一个名为sysmon的m(一般称为监控线程)，该m无需绑定p即可运行，该m在整个Go程序的运行过程中至关重要，sysmon将“向长时间运行的G任务发出抢占调度”。一旦G的抢占标志位被设为true，那么待这个G下一次调用函数或方法时，runtime便可以将G抢占，并移出运行状态，放入P的local runq中，等待下一次被调度。
* P (Processor)：代表 M 所需的上下文环境，也是处理用户级代码逻辑的处理器。它的主要用途就是用来执行goroutine的，所以它也维护了一个goroutine队列，里面存储了所有需要它来执行的goroutine Sched：代表调度器，它维护有存储M和G的队列以及调度器的一些状态信息等。  
* G (Goroutine): 其实本质上也是一种轻量级的线程。

观察调试的逻辑 `GODEBUG=schedtrace=1000,scheddetail=1 ./go-paragram`
#### 参考
* [Go并发(二)：goroutine的实现原理](https://zhuanlan.zhihu.com/p/82740001)
* [Golang 的 goroutine 是如何实现的](https://www.zhihu.com/question/20862617)
* [2-4 goroutine 协程的调度模型MPG](http://www.learnku.net/course/4/articles/124)
* [一张图说明Golang的并发模型MPG](https://juejin.im/post/5d6f42865188255eef1a78df)
* [Golang/Go goroutine调度器原理/实现【原】](https://www.cnblogs.com/sunsky303/p/11058728.html) 这篇讲的明白些