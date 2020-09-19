# Golang sync.map
>我们在使用的原生的map在进行迸发操作的时候，会panic。
>Go语言原生 map 只是读线安全，写操作不是线程安全的，一般写程序的时候，并发操作都需要加上锁。
>Go语言官方实现了一个并发安全的 map即 sync.map。1.9版本提供。

## 特性
* 无需初始化，直接声明即可。
* sync.Map 不能使用 map的方式进行取值和设置操作，而是使用sync.Map 的方案进行调用，Store 存储，Load 获取，Delete 删除。
* 使用 Range 配合一个回调函数进行遍历操作，通过回调函数遍历出来值， Range 参数中的回调函数的返回值在需要继续迭代遍历时，返回true终止遍历，则返回 false。

## 示例

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var securityMap sync.Map

	securityMap.Store("read", 100)
	securityMap.Store("write", 101)
	securityMap.Store("readonly", 102)

	fmt.Println(securityMap.Load("read"))
	securityMap.Delete("read")
	fmt.Println(securityMap)

	securityMap.Range(func(key, value interface{}) bool {
		fmt.Println(key,value)
		return true
	})
}
```