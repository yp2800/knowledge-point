# Golang slice 删除元素
## slice 
slice 的底层存储是数组，所以函数传参的时候传slice可能会对原 slice 更改，除非由于 slice 由于扩容重新创建了。
## 删除 slice 中指定元素的方法

```
for i := 0; i < len(nums); i++ {
	if nums[i] == 0 {
		nums = append(nums[:i], nums[i+1:]...)
		i--
	}
}
```

```
func DeleteSliece2(nums []int, num int) []int {
	j := 0
	for _, v := range nums {
		if v != num {
			nums[j] = v
			j++
		}
	}
	return nums[:j]
}
```

```
func DeleteSliceItem(nums []int, num int) []int {
	result := make([]int, 0, len(nums))
	for _, v := range nums {
		if v != num {
			result = append(result, v)
		}
	}
	return result
}
```

#### 参考
[golang删除slice中特定条件的元素，优化版](https://blog.csdn.net/liyunlong41/article/details/85132603)