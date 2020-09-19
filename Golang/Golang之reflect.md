# Golang reflect
> 在阅读 Gin 源码发一个有意思的package `github.com/gin-gonic/gin/internal/bytesconv` ，该包用反射和unsafe包实现了类型指针转换。没有内存的申请。以后可以借鉴，片断如下，非常妙，眼前一亮的感觉。

```
package bytesconv

import (
   "reflect"
   "unsafe"
)

// StringToBytes converts string to byte slice without a memory allocation.
func StringToBytes(s string) (b []byte) {
   sh := *(*reflect.StringHeader)(unsafe.Pointer(&s))
   bh := (*reflect.SliceHeader)(unsafe.Pointer(&b))
   bh.Data, bh.Len, bh.Cap = sh.Data, sh.Len, sh.Len
   return b
}

// BytesToString converts byte slice to string without a memory allocation.
func BytesToString(b []byte) string {
   return *(*string)(unsafe.Pointer(&b))
}
```

测试效果

```
func main() {
	s := "ABCD"
	bytes := StringToBytes(s)
	fmt.Println(bytes)

	toString := BytesToString(bytes)
	fmt.Println(toString)
}
```
输出

```
[65 66 67 68]
ABCD
```
