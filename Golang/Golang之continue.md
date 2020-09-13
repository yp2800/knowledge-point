# golang continue 
> Go语言中的 continue 就是跳过当次的循环，执行下一次的循环语句。
> 
> 可是在多重的循环中，也可用 label 标出想 continue 的循环。这点容易被忽略。

如下一个嵌套循环，continue 后面跟了 walk label。

```
package main

import (
	"fmt"
	"time"
)

func main() {
walk:
	for i := 0; i < 10; i++ {
		time.Sleep(time.Second)
		fmt.Println("go => %d", i)
		if i == 5 {
			continue walk //这里有walk label
		}
		//fmt.Println("gogogo => %d", i)

		for i := 0; i < 3; i++ {
			fmt.Println("gogogo => %d", i)
			continue walk
		}
	}

}
```
输出的结果为

```
go => %d 0
gogogo => %d 0
go => %d 1
gogogo => %d 0
go => %d 2
gogogo => %d 0
go => %d 3
gogogo => %d 0
go => %d 4
gogogo => %d 0
go => %d 5
go => %d 6
gogogo => %d 0
go => %d 7
gogogo => %d 0
go => %d 8
gogogo => %d 0
go => %d 9
gogogo => %d 0
```

把 continue 后面的 walk lable去掉的话，输出如下

```
go => %d 0
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
go => %d 1
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
go => %d 2
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
go => %d 3
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
go => %d 4
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
go => %d 5
go => %d 6
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
go => %d 7
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
go => %d 8
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
go => %d 9
gogogo => %d 0
gogogo => %d 1
gogogo => %d 2
```

#### 总节
label的标签的用法容易被忽略，但有时候给人眼前一亮的感觉。
