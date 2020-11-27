# Golang sync.Cond
> Cond 实现了一种条件变量，可以使用在多个reader待共享资源 ready 的场景。

十人比赛跑步的例子

```Golang
package main

import (
	"fmt"
	"sync"
	"time"
)

// 10 个人跑步比赛
func main() {
	cond := sync.NewCond(&sync.Mutex{})
	var wg sync.WaitGroup
	wg.Add(11)
	for i := 0; i < 10; i++ {
		go func(num int) {
			defer wg.Done()
			cond.L.Lock()
			fmt.Println(num, "号已经就位")
			cond.Wait() // 先解锁，挂起，等唤醒信号，再锁上，然后向下执行
			fmt.Println(num, "号开始跑...")
			cond.L.Unlock() // 然后再解琐
		}(i)
	}
	time.Sleep(time.Second * 2)
	go func() {
		defer wg.Done()
		fmt.Println("裁判已经就位，准备发令枪")
		fmt.Println("比赛开始，大家开始跑")
		cond.Broadcast()
	}()
	wg.Wait()
}
```

##### 参数
* [https://ieevee.com/tech/2019/06/15/cond.html](https://ieevee.com/tech/2019/06/15/cond.html)