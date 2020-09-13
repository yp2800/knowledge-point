# Golang变量避坑
遇到的坑又重复踩了一些，我一直谨记着使用规则，记录一下。

Golang里 := 的作用是局部变量，全局变量需要声明，可有时候声明了全局变量，在调用相关方法调用变量的时候也会返回err，为了接收err省事就用了:= ,可以调用其它函数的时候确用了全局变量，这样会产生 ”panic: runtime error: invalid memory address or nil pointer dereference“ 空指针错误。

**改造前**

```
var DB *gorm.DB

func SetupDb() {
	mysqlUrl := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?allowNativePasswords=true&charset=utf8mb4&parseTime=true&loc=Local",
		config.Conf.Mysql.User,
		config.Conf.Mysql.Password,
		config.Conf.Mysql.Host,
		config.Conf.Mysql.Port,
		config.Conf.Mysql.Name)
	DB, err := gorm.Open("mysql", mysqlUrl)  // 此处的 DB是局部变量，全局变量不受影响
	if err != nil {
		log.Fatalln(err)
	}
	DB.LogMode(true)
	createTable(&APP{})
	createTable(&Env{})
	DB.AutoMigrate(&APP{}, &Env{})
}

func createTable(t interface{}) {
	if !DB.HasTable(t) {    // 此处调用会产生空指针错误
		DB.CreateTable(t)
	}
}
```

**改造后**

```
var DB *gorm.DB

func SetupDb() {
	var err error  // 定义err
	mysqlUrl := fmt.Sprintf("%s:%s@tcp(%s:%d)/%s?allowNativePasswords=true&charset=utf8mb4&parseTime=true&loc=Local",
		config.Conf.Mysql.User,
		config.Conf.Mysql.Password,
		config.Conf.Mysql.Host,
		config.Conf.Mysql.Port,
		config.Conf.Mysql.Name)
	DB, err = gorm.Open("mysql", mysqlUrl) // 给全局变量赋值
	if err != nil {
		log.Fatalln(err)
	}
	DB.LogMode(true)
	createTable(&APP{})
}

func createTable(t interface{}) {
	if !DB.HasTable(t) {
		DB.CreateTable(t)
	}
}
```