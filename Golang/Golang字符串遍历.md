# Golang 字符串遍历
## 什么是字符串
在 Go 语言中，字符串实际上是类型为 byte 的只读切片。

一个字符包含了任意个 byte。它并不限定 Unicode，UTF-8或者任何其它预定义的编码。在内容上，它完全等价于一个类型为 byte 的切片。

字符串是 Golang 语言中最常用的基础数据类型之一，虽然字符串被看作是一个整体，但是实际上字符串是一片连续的内存空间，我们也可以理解成一个由字符组成的数组。

C 语言中的字符串就使用字符串就使用字符数组 char[] 表示，作为数组会占用一片连续的内存连续空间，这片内存空间存储了的字节共同组成了字符串，Go语言中的字符串其实是一个只读的数组。

## UTF-8 和字符串
索引字符字符串返回的是 byte，而不是字符：一个字符就是一堆字节。这意味着当我们将字符存储在字符串中时，实际存储的是这个字符的字节。

* Go源码总是UTF-8
* 一个字符串包含任意个byte
* 字符串常量如果缺少字节级转义，将始终保持UTF-8序列。
* Unicode的代码点在Go语言中被称为rune
* Go不保证字符串中的字符是规范化的。

## go有两种方式对字符串进行遍历
go 语言中的字条串实际上是类型为 byte 的只读切片。或者说一个字符串是一堆字节。
* utf-8  uint8   byte
* Unicode int32 rune

```
package main

import (
    "fmt"
    "reflect"
)

func main() {
    str := "Hello,世界"
    //utf-8遍历
    for i := 0; i < len(str); i++ {
        ch := str[i]
        ctype:=reflect.TypeOf(ch)
        fmt.Printf("%s ",ctype)  				 //uint8
    }
    fmt.Println("=============>Unicode遍历")
    //Unicode遍历
    for _, ch1 := range str {
        ctype:=reflect.TypeOf(ch1)
        fmt.Printf("%s ",ctype) 		  		 //int32
    }
}
```

```
修改字符串中的字节（用 []byte）：

func main() {
	
	 s := "Hello 世界！"
	
	 b := []byte(s)        // 转换为 []byte，自动复制数据
	
	 b[5] = ','            // 修改 []byte
	
	 fmt.Printf("%s\n", s) // s 不能被修改，内容保持不变
	
	 fmt.Printf("%s\n", b) // 修改后的数据

}

修改字符串中的字符（用 []rune）：

func main() {

	 s := "Hello 世界！"
	
	 r := []rune(s)         // 转换为 []rune，自动复制数据
	
	 r[6] = '中'            // 修改 []rune
	
	 r[7] = '国'            // 修改 []rune
	
	 fmt.Println(s)         // s 不能被修改，内容保持不变
	
	 fmt.Println(string(r)) // 转换为字符串，又一次复制数据

}
```
#### 参考
* [go 中的字符串遍历](https://studygolang.com/articles/439)
* [go字符串的遍历输出](https://blog.csdn.net/benben_2015/article/details/78904860)
* [3.4 字符串](https://draveness.me/golang/docs/part2-foundation/ch03-datastructure/golang-string/)