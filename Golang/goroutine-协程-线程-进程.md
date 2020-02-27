# Goroutine、协程、线程、进程
## 区别
* 协程 coroutine 是 python、java 中都有的概念。在内核态不切换，在用户态切换（除非有 IO 操作去内核态运行）。
*  Goroutine是一个与其他goroutines 并发运行在同一地址空间的Go函数或方法。一个运行的程序由一个或更多个goroutine组成。它与线程、协程、进程等不同。简单的说就是golang自己实现了协程并叫做goruntine。
*  线程是操作系统能够进行运算调度的最小单位。它被包含在进程之中，是进程中的实际运作单位。一条线程指的是进程中一个单一顺序的控制流，一个进程中可以并发多个线程，每条线程并行执行不同的任务。
*  进程是分配资源的基本单位，也是我们说的隔离。线程作为独立运行和独立调度的基本单位。
## 关系
一个进程可以有多个线程。一个线程有多个 Goroutine/协程

## threading 线程


## goruntine
golang 自己实现的协程和叫 goruntine，又和其它语言的协程不一样。

```Golang
package main

import (
	"fmt"
	"sync"
	"time"
)

func worker(id int, wg *sync.WaitGroup) {
	defer wg.Done()
	fmt.Printf("Worker %d starting\n", id)
	time.Sleep(time.Second)
	fmt.Printf("Woker %d done\n", id)
}
func main() {
	var wg sync.WaitGroup

	for i := 1; i <= 5; i++ {
		wg.Add(1)
		go worker(i, &wg)
	}
	wg.Wait()
}
```

#### 参考链接 
* [得到Go程序的汇编代码的方法](https://colobu.com/2018/12/29/get-assembly-output-for-go-programs/)
* [进程、线程、协程与goruntine](https://zhuanlan.zhihu.com/p/27245377)
* [Golang：线程 和 协程 的区别](https://juejin.im/post/5d9a9c12e51d45781420fb7e)
* [多进程编程](https://github.com/dominicing/golang/blob/master/03-Go%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B/00-%E7%AE%80%E7%95%A52-%E5%A4%9A%E8%BF%9B%E7%A8%8B%E7%BC%96%E7%A8%8B.md)