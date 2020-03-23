# Golang 类型判断
## 三种类型判断的方式
* `fmt.Sprintf("%T", v)`
* 反射 `reflect.TypeOf(v).String()`
* 断开断言

```	
switch v.(type) {
	case int:
		return "int"
	case uint:
		return "uint"
	default:
		_ = v
		return "unknown"
}
```

#### 参考
* [golang如何获取变量的类型：反射，类型断言](https://ieevee.com/tech/2017/07/29/go-type.html)