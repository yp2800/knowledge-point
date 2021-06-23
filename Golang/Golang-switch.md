# Golang switch

switch 语句中的 case 代码块会默认带上 break，但可以使用 fallthrough 来强制执行下一个 case 代码块。

```
isSpace := func(char byte) bool {
		switch char {
		case ' ': // 空格符会直接 break，返回 false // 和其他语言不一样
			fallthrough // 返回 true
		case '\t':
			return true
		}
		return false
	}
fmt.Println(isSpace('\t')) // true
fmt.Println(isSpace(' '))  // false
```