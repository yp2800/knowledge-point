# Golang 性能优化
## Golang 项目中性能优化主要有以下几个方面：
* CPU profile 			CPU 的使用情况
* Memory Profile			Heap Profile，内存的使用情况
* Block Profilling			Goroutines 不在运行状态的情况，可以用来分析和查找死锁等性能瓶颈
* Gogoutine Profilling	Goroutines 的使用情况，有哪些 goroutine 及它们的调用关系

## 采集性能数据的包
Golang 有两个以下两个标准库可以猎取程序的运行数据：

* runtime/pprof  采集工具型应用运行数据进行分析
* net/http/pprof 采集服务型应用运行数据进行分析

##### 参考
* [Go性能调优](https://studygolang.com/articles/23047)
* [使用pprof及Go 程序的性能优化](https://www.kancloud.cn/liupengjie/go/778656)