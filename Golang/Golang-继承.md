# Golang 夜读

* 匿名结构体继承

```golang
package main

import (
	"fmt"
)

type Person struct {
	Name string
	Age  int
}

func (*Person) Start() {
	fmt.Println("persoin start ...")
}

type Man struct {
	Person
}

func (*Man) Start() {
	fmt.Println("man Start ...")
}

func main() {
	man := Man{Person{Name: "xiaoming"}}
	fmt.Printf("My Name is %s", man.Name)
	man.Start()
}
```