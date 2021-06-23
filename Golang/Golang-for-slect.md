# Golang for select

通常在 for 循环中，使用 break 可以跳出循环，但是注意在 Go 语言中，for select 配全时，break 并不能跳出循环。

```
func testSelectFor2(chExit chan bool){
 EXIT:
    for  {
        select {
        case v, ok := <-chExit:
            if !ok {
                fmt.Println("close channel 2", v)
                break EXIT//goto EXIT2
            }
 
            fmt.Println("ch2 val =", v)
        }
    }
 
    //EXIT2:
    fmt.Println("exit testSelectFor2")
}

// output 
// fatal error: all goroutines are asleep - deadlock!
```