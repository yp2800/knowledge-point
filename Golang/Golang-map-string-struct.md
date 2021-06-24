# Golang map[string]struct{} map[string]*struct{} 区别

## 现象
下面代码会有报错 `cannot assign to p["HM"].age`

```
type Person struct {
     name string
     age int
 }

type People map[string]Person

func main() {
  p := make(People)
  p["HM"] = Person{"Hank McNamara", 39}
  p["HM"].age = p["HM"].age + 1
  fmt.Printf("age: %d\n", p["HM"].age)
}

// cannot assign to p["HM"].age
```

## 原因
p["HM"]不是一个常规的可寻址值:hashmap可以在运行时增长，然后它们的值在内存中移动，旧的位置就会过时。如果映射中的值被视为常规可寻址值，那么映射实现的那些内部内容将被公开。  因此，p[“HM”]是一个稍微不同的东西，在规范中称为“映射索引表达式”;如果您在规范中搜索短语“索引表达式”，您将看到可以对它们进行某些操作，如读取它们、为它们赋值，并在递增/递减表达式(用于数字类型)中使用它们
## 解决方案

```
package main

import "fmt"

type Person struct {
    name string
    age  int
}

type People map[string]*Person

func main() {
    p := make(People)
    p["HM"] = &Person{"Hank McNamara", 39}
    p["HM"].age += 1
    fmt.Printf("age: %d\n", p["HM"].age)
}
```
##### 参考地址
* [Cannot assign to struct field in a map](https://stackoverflow.com/questions/42605337/cannot-assign-to-struct-field-in-a-map)
* [Why do I get a “cannot assign” error when setting value to a struct as a value in a map? [duplicate]](https://stackoverflow.com/questions/32751537/why-do-i-get-a-cannot-assign-error-when-setting-value-to-a-struct-as-a-value-i)
* [Index_expressions](https://golang.org/ref/spec#Index_expressions)