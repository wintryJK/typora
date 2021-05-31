# day06

## 内容回顾

### 接口

接口是一种类型，抽象的类型

接口就是要实现的方法的清单

接口的定义

```go
type mover interface{
    方法的签名(参数)(返回值)
}
```

接口的实现

- 实现了接口的所有方法就实现了接口
- 实现了接口就可以当成这个接口类型的变量

接口变量

- 实现了一个万能的变量，可以保存所有实现了接口的类型的值
- 通常是作为函数的参数出现

空接口

- 接口中没有定义任何方法，也就是说任意类型都实现了空接口 
- 任何类型都可以存到空接口变量中
- `interface{}`：连到一起
- `map[string]interface{}`

接口底层

- 动态类型
- 动态值

类型断言

`x.(string\int)`

`x.(type)`



### 包

包的定义：`package`关键字，包名通常是和目录名一致，不能包含`-`中横线

- 一个文件夹就是一个包
- 文件夹里放的都是.go文件

包的导入：`import`

- 包导入路径从`$GOPATH/src`
- 单行导入
- 多行导入
- 起别名
- 匿名导入 --> `sql`包导入会用到
- Go不支持循环导入

包中`标识符(变量名、函数名、结构体名、接口名等)`可见性 --> 标识符首字母大写表示对外可见

`init()`

- 包导入时会自动执行

- 一个包只有一个init()

- 没有参数，返回值也不能调用

- 多个包的init执行顺序

- 一般用于做初始化

  

### 文件操作

打开文件

```go
// 应该使用的是f2，defer应该在error检查以后，如果err不是空，fileObj是对应类型的空值，直接关闭会引发空指针错误
func f1() {
	var fileObj *os.File
	var err error
	fileObj, err = os.Open("./main.go")
	defer fileObj.Close()
	if err != nil {
		fmt.Printf("open failed, err: %v\n", err)
		return
	}
}

func f2(){
	var fileObj *os.File
	var err error
	fileObj, err = os.Open("./main.go")
	
	if err != nil {
		fmt.Printf("open failed, err: %v\n", err)
		return
	}
	defer fileObj.Close()
}
```

读文件

```go
fileObj.read()
bufio
ioutil
```

写文件

`os.OpenfFile()`

`fileObj.write/fileObj.WriteString`

`bufio`
`ioutil`



## 今日内容

### time标准库

1. 时间类型

2. 时间戳

3. 时间间隔

4. 时间操作

   - Add
   - Sub
   - Equal
   - Before
   - After

5. 定时器

6. 时间格式化

   - `口诀2006 1 2 3 4 5`

   - 解析字符串格式的时间`注意加载时区`

### 日志库

#### 需求分析

1. 支持往不同的地方输出日志
2. 日志分级别
   - Debug
   - Info
   - Trace
   - Warning
   - Error
   - Fatal
3. 日志要支持开关控制
4. 日志要有时间信息，行号，文件名，日志级别，日志信息
5. 日志文件要切割
6. 

### 反射

