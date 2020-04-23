# Python vs Golang 协程的区别
## 进程间通信的方式
七种通信方式

* 管道/匿名管道 （pipe）

	> 管道的实质是一个内核缓冲区，进程以先进先出的方式从缓冲区存取数据，管道一端的进程顺序的将数据写入缓冲区，另一端的进程则顺序的读出数据。 该缓冲区可以看做是一个循环队列。
* 有名管道 （FIFO）	
	> 匿名管道，由于没有名字，只能用于亲缘关系的进程间通信。为了克服这个缺点提出了有 名管理（FIFO）。 
* 信号 （Signal）
	> Linux 常用的 64 个信号 
* 消息队列 （Message）
* 共享内存 （share memory）
	> IPC	 
* 信号量 （Semaphore）
	> 信号量是一个计数器，用于多进程对共享数据的访问，信号量的意图在于进程间同步。
* 套接字 （Socket）

## 线程、进程、协程
* 进程

	> 进程是计算机中已运行程序的实体。程序本身只是指令，数据及其组织形式的描述，进程才是程序的真正运行实例。
* 线程
	> 操作系统能够进行运算调度的最小单位。它被包含进进程之中，是进程中的实际运作单位。
* 进程与线程的关系。
	> 一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。CPU 的最小调度单元是线程不是进程，所以单进程多线程也可以利用多核 CPU。
* 协程
	> 协和是通过在线程中实现调度，避免陷入内核级别的上下文切换造成的性能损失，进而突破了线程在 IO 上的性能瓶颈。
* 协程和线程的关系
	> 协和是语言层面实现对纯种的调度，避免了内核级别的上下文消耗。
	
## Python
Python 3.5 正式引入 Async/Await 表达式，使得协和在语言层面得到支持和优化，大大简化之前 yield 的写法。
Python 协和是 eventloop模型实现， 严格的 1:N 的关系，也就是一个线程对应多个协程，虽然可以实现异步 I/O,但不能有效的利用多核（GIL)。

```Python
import asyncio
import requests
import time

async def run(url):
    print("开始执行爬虫...",url)
    print("开始执行时间：", time.time())
    loop = asyncio.get_event_loop()
    response = await loop.run_in_executor(None, requests.get, url)
    print(response.url)
    print("结束执行时间：", time.time())
    
url_list = ["https://www.baidu.com","https://mengsec.com"]

tasks = [asyncio.ensure_future(run(url)) for url in url_list]
loop = asyncio.get_event_loop()
loop.run_until_complete(asyncio.wait(tasks))
```

作为服务端， event loop 最核心的就是 IO 多路复用技术，所有来自客户端的请求都由 IO 多路复用函数来处理;
作为客户端， event loop 的核心在于利用 Future 对象延迟执行，并使用 send 函数激发协程，挂起，等待服务端处理完成后再调用 CallBack 函数继续下面的流程。

## Golang
Golang csp 模型实现, M:N 的关系也就 是 N 个协程会映射分配到 M 个线程上，这样带了两点好外。

1. 多个线程能分配到不同的核心上，CPU 的密集的应用使用 goroutine 也会获得加速。 
2. 即使有少量的阻塞的操作，也只会阻塞某个 work 线程，而不会把整个程序阻塞。

(不要以共享内存的方式来通信，相反，要通过通信来共享内存) -- CSP并发模型

go 还实现了另外一种并发形式，多线程共享内存。如 map、数组或者某个结构体通过锁来访问。

## 区别
两种协和对比

* async 是非抢占式的，一旦开始采用 async 函数，那么你整个程序都必须是 async 的，不然就会有阻塞的地方，也就是说 async 具有传染性
* Python 整个异步编程生态的问题，之前标准库和各种第三方库的阻塞性函数都不能用了
* Goroutine 是 go 与生俱来的特性，所有库都是可以用的，避免了 Python 中需要把所有库重写一遍的问题。
* Goroutine 中不需要显式使用 await 交出控制权，但是 Go 也不会严格按照时间片去调试 goutine，而是会在可能阻塞的地方插入调试。Goroutine 可以看做是半抢占式的。

协程四种状态

* Pending
* Running
* Done
* Cacelled

## 参考
* [Python 协程与go协程的区别!](https://zhuanlan.zhihu.com/p/65308850)
* [进程间通信IPC (InterProcess Communication)](https://www.jianshu.com/p/c1015f5ffa74)
* [Python3 高级特性学习——协程](https://mengsec.com/2019/03/04/Python3-advanced-feature-learning/)
* [python协程与golang协程精讲](https://blog.csdn.net/runner668/article/details/90344267)