# zip
> 在看zip包的源码时，发现了这空结构体的方法写有有些怪

如下写结构方法，没有传递(dw dirWriter)，像是一个匿名的方式

```go
type dirWriter struct{}

func (dirWriter) Write(b []byte) (int, error) {
	if len(b) == 0 {
		return 0, nil
	}
	return 0, errors.New("zip: write to directory")
}
```