# Golang 并发数控制
> 用 sync.WaitGroup 和 channel 在一起实现

```go
	ch := make(chan int, 10)
	var wg sync.WaitGroup
	for i:=0;i<10;i++{
		go func() {
			for i2 := range ch {
				fmt.Println("work start : ", i2)
				wg.Done()
			}
		}()
	}

	jobCount := 20
	for i:=0;i<jobCount;i++{
		ch<- 1
		wg.Add(1)
		fmt.Printf("job No.: %d gouroutine No.: %d\n", i, runtime.NumGoroutine() )
	}
	wg.Wait()
	
```
	
> 控制十个并发数，如果其中一个goutine有错误，就退出，但要执行完正在执行的任务。

```go
func main() {
	consumer([]int{1, 2, 3, 4, 5, 6, 9, -11, 11, 12, 13, 14, 15, 16, 17, 18, 19, 10, -1})
}

func consumer(task []int) error {
	ch := make(chan int, 10)
	exitch := make(chan int)
	var wg sync.WaitGroup
	for i := 0; i < cap(10); i++ {
		go func() {
			for i := range ch {
				wg.Done()
				fmt.Println("Start task :", i)
				if err := handerFunc(i); err != nil {
					exitch <- 1
				}

			}
		}()
	}
	for _, v := range task {
		flagexit := false
		select {
		case <-exitch:
			flagexit = true
		default:
			ch <- v
			wg.Add(1)
			fmt.Println("task queue :", v)
		}
		if flagexit {
			break
		}
	}

	wg.Wait()
	return nil
}
func handerFunc(v int) error {
	if v > 0 {
		return nil
	}
	return errors.New("error v <= 0")
}
```