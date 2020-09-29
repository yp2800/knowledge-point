# Golang 插件系统
> 我们知道 Linux 系统有静态库和动态库
> 
> 静态库或者静态链接库是编译期间决定的程序、外部函数和变量构成， 在程序的编译阶段，编译器或者链接器将其内容拷备到目标的应用，并生成一个独立的可执行的对象文件。
> 
> 动态库或者动态链接库是在程序的运行期动态调用的。是共享对象，可以在多个可执行程序之间共享，程序所使用的模块也会在运行期间从共享对象中加载。
> 
> Go 语言的插件系统就是基于 `程序的运行期间动态调用` 实现的，也就是动态库或动态链接库。插件系统是基于 C 语言的动态库实现的。

## 示例
插件实现的两个函数 Add 和 Sub 在文件 func-plugin.go 里

```go
package main
// go build -buildmode=plugin -o main.so func-plugin.go
import "fmt"

// Add two integers
func Add(a int, b int) int {
	fmt.Printf("\nAdding a=%d and b=%d\n", a, b)
	return a + b
}

// Sub two integers
func Sub(a int, b int) int {
	fmt.Printf("\nSubtracting b= %d from a=%d\n", b, a)
	return a - b
}
```

编译成动态库 `go build -buildmode=plugin -o main.so func-plugin.go`

写一个程序来调用 main.so

```go
package main

import (
	"fmt"
	"path/filepath"
	"plugin"
)

func main() {
	plugins, err := filepath.Glob("main.so")
	if err != nil {
		panic(err)
	}
	fmt.Printf("Loading plugin %s\n", plugins[0])
	// Open – Loads the plugin
	p, err := plugin.Open(plugins[0])
	if err != nil {
		panic(err)
	}
	// Lookup – Searches for a symbol name in the plugin
	add, err := p.Lookup("Add")
	if err != nil {
		panic(err)
	}
	// Checks the function signature
	addFunc, ok := add.(func(i, j int) int)
	if !ok {
		panic("Plugin has no 'Add(int, int)int' function")
	}
	// Uses the function to return results
	fmt.Printf("\n10 + 12 = %d\n", addFunc(10,12))

	sub, err := p.Lookup("Sub")
	if err != nil {
		panic(err)
	}
	subFunc, ok := sub.(func(i, j int) int)
	if !ok {
		panic("Plugin has no 'Sub(int, int)int' function")
	}
	fmt.Printf("\n22 - 8 = %d\n", subFunc(22,8))

}
```

##### 参考链接
* [Writing Plugins in Go](https://code.tutsplus.com/tutorials/writing-plugins-in-go--cms-29101)
* [Plugins in Go](https://technobeans.com/2019/03/05/plugins-in-go/)