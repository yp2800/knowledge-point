# Golang 并发退出
> 在工作中可能要起多个 Goroutine 跑任务，有时候遇到问题需要中止所以的 Goroutine 暂停或者退出。

控制并发通常有三种方法

* 全局共享变量
* channel 通信
* Context 包

## 全局共享变量
这种模式最为简单，所有的 Goroutine 轮询变量，来决定要不要继续跑任务。

```go
running := true
f:= func() {
	for running {
		fmt.Println("sub proc running")
		time.Sleep(time.Second)
	}
}
go f()
go f()
go f()
time.Sleep(time.Second)
running = false
fmt.Println("set running=false")
time.Sleep(time.Second)
fmt.Println("main proc exit")
```

## channel 通信
一种更通用灵活的实现方式。下面是一种从外部中止所有goroutine的示例

```
import (
	"fmt"
	"os"
	"os/signal"
	"sync"
	"syscall"
	"time"
)

func main() {
	stop := make(chan bool)
	var wg sync.WaitGroup
	for i := 0; i < 3; i++ {
		wg.Add(1)
		go func(stop <-chan bool) {
			defer wg.Done()
			consumer(stop)
		}(stop)
	}
	waitForSignal()
	close(stop)
	fmt.Println("stopping all jobs")
	wg.Wait()
}
func consumer(stop <-chan bool) {
	for {
		select {
		case <-stop:
			fmt.Println("exit sub goroutine")
			return
		default:
			fmt.Println("running ...")
			time.Sleep(20*time.Microsecond)
		}
	}
}

func waitForSignal() {
	sigs := make(chan os.Signal)
	signal.Notify(sigs, os.Interrupt)
	signal.Notify(sigs, syscall.SIGTERM)
	<-sigs
}
```

## Context
Context的创建和调用关系是层层递进的，也就是我们通常所说的链式调用，类似数据结构里的树，从根节点开始，每一次调用就衍生一个叶子节点。

```go
type favContextKey string

func main() {
	wg := &sync.WaitGroup{}
	values := []string{"https://www.baidu.com", "https://www.zhihu.com"}
	ctx, cancelFunc := context.WithCancel(context.Background())
	for _, value := range values {
		wg.Add(1)
		subCtx := context.WithValue(ctx, favContextKey("url"), value)
		go reqUrl(subCtx, wg)
	}
	go func() {
		time.Sleep(time.Second * 3)
		cancelFunc()
	}()
	wg.Wait()
	fmt.Println("exit main goroutine")
}
func reqUrl(ctx context.Context, wg *sync.WaitGroup) {
	defer wg.Done()
	url, _ := ctx.Value(favContextKey("url")).(string)
	for {
		select {
		case <-ctx.Done():
			fmt.Printf("stopping getting url %s\n", url)
			return
		default:
			get, err := http.Get(url)
			if get.StatusCode == http.StatusOK && err == nil {
				all, _ := ioutil.ReadAll(get.Body)
				subCtx := context.WithValue(ctx, favContextKey("resp"),fmt.Sprintf("%s%x",url, md5.Sum(all)))
				wg.Add(1)
				go showResp(subCtx,wg)
			}
			get.Body.Close()
			time.Sleep(time.Second)

		}
	}
}
func showResp(ctx context.Context,wg *sync.WaitGroup)  {
	defer wg.Done()
	for  {
		select {
		case <-ctx.Done():
			fmt.Println("stop showing resp")
			return
		default:
			fmt.Println("printing: ", ctx.Value(favContextKey("resp")))
			time.Sleep(time.Second)
		}
	}
}
```