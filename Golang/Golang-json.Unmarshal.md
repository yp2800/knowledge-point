# Golang json.Unmarshal
## Map
json.Unmarshal 的时候，Map 必须要加上`&`

```
	m := make(map[string]json.RawMessage)
	err = json.Unmarshal([]byte(deployJson.Data.Marathon), &m)
```

```
package main

import (
    "fmt"
)

func mapFunc(m map[string]interface{}) {
    m = make(map[string]interface{})
    m["abc"] = "123"
}

func mapPtrFunc(mp *map[string]interface{}) {
    m := make(map[string]interface{})
    m["abc"] = "123"

    *mp = m
}

func main() {
    var m1, m2 map[string]interface{}
    mapFunc(m1)
    mapPtrFunc(&m2)

    fmt.Printf("%+v, %+v\n", m1, m2)
}
// output 
// map[], map[abc:123]
```
* 首先，映射m1和m2都指向nil。
* 调用mapFunc会将m1指向的值复制到m，结果m也将指向nil映射。
* 如果在(1)中已经分配了 map ，则在(2)中，由m1指向的基础 map 数据结构的地址(不是m1的地址)将被复制到m。在这种情况下，m1和m都指向相同的 map 数据结构，因此m1也可以看到通过m修改 map 项。
* 在mapFunc函数中，将分配新 map 并将其分配给m。无法将其分配给m1。

如果是指针:
* 调用mapPtrFunc时，m2的地址将被复制到mp中。
* 在mapPtrFunc中，将分配新 map 并将其分配给*mp(不是mp)。由于mp是m2的指针，因此将新映射分配给*mp将更改m2指向的值。请注意mp的值不变，即m2的地址。

# slice 同理也需要

```
var slice []string
err := json.Unmarshal([]byte("[\"a\",\"b\",\"c\",\"d\"]"), &slice) //&slice
```