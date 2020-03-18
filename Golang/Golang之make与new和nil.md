# Golang 之 make new nil
## nil 是什么
任何类型在初始化的时候都对应一个零值：

* bool 是 false
* 整型和 float 类型等 是 0
* 复数类型如complex64、complex128，默认值为0+0i。
* string 是 ""
* nil 可以用作 interface、function、pointer、map、slice 和 channel 的“空值”
* struct 的零值  其每一个元素都被go自动初始化其类型对应零值
* array 的零值 数组的默认值要根据其数据类型来确定。例如：var a [4]int，其默认值为[0 0 0 0]。

## make 和 new
* new 是内建函数 `func new(Type) *Type` 内置函数 new 分配空间。传递给 new 函数的是一个类型，不是一个值。返回值是指向这个新分配的零值的指针。
* make 也是内建函数 `func make(Type, size IntegerType) Type ` 它的函数原型比 new 多一个长度size参数，返回值也不同。内建函数 make 分配并且初始化一个 slice 或者 map 或者 chan 对象。并且只能是这三种对象。和 new 一样第一个参数是类型，不是一个值。但是 make 的返回值就是这个类型（即一个引用类型），而不是指针，依赖具体传入的类型。

new 的作用是 初始化 一个指向类型的指针 (*T)， make 的作用是为 slice, map 或者 channel 初始化，并且返回引用 T

make(T, args)函数的目的与new(T)不同。它仅仅用于创建 Slice, Map 和 Channel，并且返回类型是 T（不是T*）的一个初始化的（不是零值）的实例。 这中差别的出现是由于这三种类型实质上是对在使用前必须进行初始化的数据结构的引用。 例如, Slice是一个 具有三项内容的描述符，包括 指向数据（在一个数组内部）的指针，长度以及容量。在这三项内容被初始化之前，Slice的值为nil。对于Slice，Map和Channel， make（）函数初始化了其内部的数据结构，并且准备了将要使用的值。

#### 参考
* [不得不知道的golang知识点之nil](https://studygolang.com/articles/11535)
* [理解 Go nil](https://sanyuesha.com/2017/06/11/go-nil/)
* [Go 中的 nil](https://lingchao.xin/post/go-nils.html)
* [理解Go语言的nil](https://cloud.tencent.com/developer/article/1422480)
* [Go 语言中的 new() 和 make()的区别](https://studygolang.com/articles/3496)