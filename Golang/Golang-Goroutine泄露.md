# Golang Goroutine 泄露
## Goroutine 泄露是指什么
Go 并发模式会创建 goroutine，如果 goroutine 一直得不到释放并且还在不断创建协程 就是有问题的。

#### 参考
* [如何防止 goroutine 泄露](https://juejin.im/post/5d2f66a251882553086ab819)