# Golang 之 channel
## channel 三种状态和三种操作
* 三种状态
	* nil, 未初始化的状态，只进行了声明，或者手动赋值。
	* active, 正常的 channel，可读或可写。
	* closed,已关闭，千万不要误认为关闭 channel后，channelr 值是 nil。
* 三种操作
	* 读
	* 写 
	* 关闭 close 的时候会通知所有 goroutine
	
三种状态，三种操作组成的 9 种组合

| 操作 | nil 的 channel | 正常的 channel | 已关闭的 channel |
| ---  | --- | --- | --- |
| <- ch | 阻塞 | 成功或阻塞 | 读到零值 |
| ch <- | 阻塞 | 成功或阻塞 | panic |
| close(ch) | panic | 成功 | panic |

## channel 分类
* 只读 channel 		`var ch <-chan int`
* 只写 channel 		`var ch chan<- int`
* 读写 channel 		`var ch chan`
* 有无缓冲 channel 		`ch := make(chan int,3)`

#### 参考
* [Go 只读/只写channel](https://www.cnblogs.com/baiyuxiong/p/4545028.html)
* [总结了才知道，原来channel有这么多用法！](https://segmentfault.com/a/1190000017958702)