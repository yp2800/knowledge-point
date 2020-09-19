# Golang type 用法
> type 在 Golang 中非常常见的关键字，一般的用法就是定义结构，接口等。
> 但是 type 还有很多其它的用法。

## 定义结构体

```go
type Person struct {
	name string
	age string
}

type Mutex struct {}

type otherMutex Mutex
```

## 定义接口

```go
type Person interface {
	ShowName(s string)
}
```

## 定义类型

```go
type Myint int
type (m Myint) showValue() {
	fmt.Println("show int", m)
}
```

## 别名定义
> 定义和原来一样的类型，就是一个alias

```go
type nameMap = map[string]interface{}

func main(){
	m := make(nameMap)
	m["name"] = "golang"
	fmt.Printf("%v", m)
}

type A int32
type B = int32

func main() {
	var a A = 333
	var b B = 333
	b=B(a) // a,b 属于不同的类型，所以这里强制转换
}
```

## 定义函数类型

```go
type cb func(s string)  // 定义一个函数类型

//对函数类型再定义方法
func (f cb) ServerCb() error{
	f("cbb")
	fmt.Println("server cb")
	return nil
}

func main() {
	s :=cb(func( s string){
		fmt.Println("sss", s)
	})
	s.ServerCb()
}
```

有一个关于 type 示例

```go
package main

import (
	"fmt"
)

func main() {
	first(1, callback)
}

func callback(i int) {
	fmt.Println("I am callback")
	fmt.Println(i)
}

func first(i int, f func(int)) {
	Second(i, fun(f))
}

func Second(i int, call Call) {
	call.call(i)
}

type fun func(int)

func (f fun) call(i int)  {
	f(i)
}
type Call interface {
	call(int)
}
```

##### 参考
*[type 关键字使用](https://www.cnblogs.com/sunlong88/p/13482960.html)