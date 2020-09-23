# Golang Label
> 在看 golang标准库的时候发现有些奇怪的 label用法。
> 整理下关于 Label的用法

## goto

```go
func main() {
		fmt.Println(11)
		goto END
		fmt.Println(22)
END:
		fmt.Println(33)
}
```

## break Label
> break 可以带 Label，用在 for, switch, select上

```go
ForLoop:
	for i := 0; i < 5; i++ {
		fmt.Println(i)
		break ForLoop
	}
	
SitchLoop:
	switch 1 {
	case 1:
		fmt.Println(1)
		break SitchLoop
	case 2:
		fmt.Println(2)
	}
```

## continue Label

```go
ForLoop:
	for i := 0; i < 5; i++ {
		fmt.Println(i)
		continue ForLoop
	}
```

##### 参考
* [Golang Label使用方法](https://www.jianshu.com/p/73fb21401d41)