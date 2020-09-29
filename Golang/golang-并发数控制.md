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