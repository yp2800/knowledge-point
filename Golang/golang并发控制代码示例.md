# Golang 并发控制代码示例
## WaitGroup

```
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {
	var wg sync.WaitGroup

	wg.Add(2)
	go func() {
		time.Sleep(2 * time.Second)
		fmt.Println("1号完成")
		wg.Done()
	}()
	go func() {
		time.Sleep(2 * time.Second)
		fmt.Println("2号完成")
		wg.Done()
	}()
	wg.Wait()
	fmt.Println("好了，大家都干完了，放工")
}
```

## Chan 通知

```
package main

import (
	"fmt"
	"time"
)

func main() {
	stop := make(chan bool)

	go func() {
		for {
			select {
			case <-stop:
				fmt.Println("监控退出，停止了...")
				return
			default:
				fmt.Println("goroutine监控中...")
				time.Sleep(2 * time.Second)
			}
		}
	}()

	time.Sleep(10 * time.Second)
	fmt.Println("可以了，通知监控停止")
	stop<- true
	//为了检测监控过是否停止，如果没有监控输出，就表示停止了
	time.Sleep(5 * time.Second)

}
```
## Context
`Context 接口`

```
type Context interface {
	Deadline() (deadline time.Time, ok bool)

	Done() <-chan struct{}

	Err() error

	Value(key interface{}) interface{}
}
```

`Context的继承衍生`

```
func WithCancel(parent Context) (ctx Context, cancel CancelFunc)
func WithDeadline(parent Context, deadline time.Time) (Context, CancelFunc)
func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)
func WithValue(parent Context, key, val interface{}) Context
```

* 控制一个 goroutine

```
package main

import (
	"context"
	"fmt"
	"time"
)

func main() {
	ctx, cancel := context.WithCancel(context.Background())
	go func(ctx context.Context) {
		for {
			select {
			case <-ctx.Done():
				fmt.Println("监控退出了,停止了 ...")
				return
			default:
				fmt.Println("goroutine监控中 ...")
				time.Sleep(2 * time.Second)
			}
		}
	}(ctx)
	time.Sleep(10 * time.Second)
	fmt.Println("可以了通知监控停止")
	cancel()
	time.Sleep(5 * time.Second)
}
```

* 控制多个 goroutine

```
import (
	"context"
	"fmt"
	"time"
)

func main() {

	ctx, cancelFunc := context.WithCancel(context.Background())
	go watch(ctx, "监控1： ")
	go watch(ctx, "监控2： ")
	go watch(ctx, "监控3： ")
	time.Sleep(10 * time.Second)
	fmt.Println("可以了，通知监控停止 ...")
	cancelFunc()
	time.Sleep(5 * time.Second)

}

func watch(ctx context.Context, name string) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println(name, "监控退出，停止了 ...")
			return
		default:
			fmt.Println(name, "goroutine 监控中 ...")
			time.Sleep(2 * time.Second)
		}
	}
}
```

* WithValue 传递元数据

```
package main

import (
	"context"
	"fmt"
	"time"
)
var key string = "name"

func main() {
	ctx, cancelFunc := context.WithCancel(context.Background())
	valueCtx1 := context.WithValue(ctx, key, "监控1: ")
	go watch(valueCtx1)
	valueCtx2 := context.WithValue(ctx, key, "监控2: ")
	go watch(valueCtx2)
	valueCtx3 := context.WithValue(ctx, key, "监控3: ")
	go watch(valueCtx3)
	time.Sleep(10 * time.Second)
	fmt.Println("可以了，通知监控停止 ...")
	cancelFunc()
	time.Sleep(5 * time.Second)

}

func watch(ctx context.Context) {
	for {
		select {
		case <-ctx.Done():
			fmt.Println(ctx.Value(key), "监控退出，停止了 ...")
			return
		default:
			fmt.Println(ctx.Value(key), "goroutine 监控中 ...")
			time.Sleep(2 * time.Second)
		}
	}
}
```

##### 参考链接
* [Go语言实战笔记（二十）| Go Context](https://www.flysnow.org/2017/05/12/go-in-action-go-context.html)