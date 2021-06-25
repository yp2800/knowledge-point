# Golang map convert to struct

## map 转 json 再转 struct

```
type MyStruct struct {
    Id           string `json:"id"`
    Name         string `json:"name"`
    UserId       string `json:"user_id"`
    CreatedAt    int64  `json:"created_at"`
}

func main() {
    m := make(map[string]interface{})
    m["id"] = "2"
    m["name"] = "jack"
    m["user_id"] = "123"
    m["created_at"] = 5
    fmt.Println(m)

    // convert map to json
    jsonString, _ := json.Marshal(m)
    fmt.Println(string(jsonString))

    // convert json to struct
    s := MyStruct{}
    json.Unmarshal(jsonString, &s)
    fmt.Println(s)
```