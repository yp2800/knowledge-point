# Golang 并发控制
## 常用的并发控制模式
* 通过无缓冲通道来实现多 goroutine 并发控制
* 通过 sync 包中的 WaitGroup 实现并发控制
* Context 包
* 全局共享变量
* select

#### 参考
* [Golang 并发控制的两种模式](https://golangnote.com/topic/184.html)
* [深入golang之---goroutine并发控制与通信](https://zhuanlan.zhihu.com/p/36907022)