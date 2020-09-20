# Golang fallthrough
> Golang 的 switch 语句中不需要 break来退出一个 case，case执行完成后，是不是继续向下匹配的。
> fallthrough 不会判断下一下 case 的条件。不论下一个 case 是否被匹配，都会被执行。
> 如果fallthrouth 之后没有 case，则会产生 `cannot fallthrough final case in swith`

示例如下

```go
package main

import "fmt"

func main() {
	switch 8 {
	case 8:
		fmt.Println(8)
		fallthrough
	case 7:
		fmt.Println(7)
		fallthrough
	case 6:
		fmt.Println(6)
	default:
		fmt.Println(0)
	}
}
```

