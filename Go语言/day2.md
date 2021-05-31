# day2

## 今日内容

流程控制

运算符

复杂数据类型

函数

匿名函数和闭包

## 内容回顾

### Go安装

`GOPATH`:写代码的工作区，保存Go代码

`GOPATH/bin`添加到环境变量：`go install`命名会把生成的二进制文件拷贝到`GOPATH/bin`

### Go命令

`go build`:编译Go程序

`go buid -o "xx.exe"`:编译成xx.exe

`go run main.go`:像脚本一样执行main.go文件

`go install`:先编译后拷贝把生成的二进制文件拷贝到`GOPATH/bin`

### Go语言文件基础语法

文件后缀名 `.go`

文件第一行：`package关键字声明包名`

如果要编译可执行文件，必须要有main包和main函数（入口函数）

Go语言函数外的语句必须以关键字开头

函数内部定义的变量必须使用

### 变量和常量

#### 变量

三种声明方式

1. `var name string`
2. `var name2 = "sss"`
3. 函数内部专属：`name3 := "ssss"`

匿名变量（哑元变量）：

​	当有些变量必须接收但是又不想使用时，可以用`_`接收

#### 常量

`const PI = 3.1415926`

`const UserError = 10000`

iota:实现枚举

两个要点：

1. 在const关键字出现时将被重置为0
2. const中每新增一行常量声明将使`iota`计数一次

### 流程控制

#### if

```go
if 条件1{
    语句
}else if{
    
}else{
    
}
```

#### for

```go
for 初始语句;条件判断;结束语句{
    循环体
}
// 初始语句;条件判断;结束语句（都可省略）
// 死循环
for {
    
}

// range
for k,v := range "abcd"{
    
}
```

## 基本数据类型

### 整型

1. 无符号：`uint8,uint16,uint32,uint64`
2. 有符号：`int8,int16,int32,int64`
3. `int`具体多少位看系统
4. `uintptr`表示指针

### 其他进制数

Go语言没法直接定义二进制数

八进制： `var n1 = 0777`  `%o`

十六进制：`var n2 = 0xff`  `%x`



### 浮点型

1. 直接定义`var f1 = 1.33` f1的类型时float64
2. float64和float32不能比较

### 复数

`complex128和complex64`

### 布尔值

`true和false`

不能和其他类型做转换

### 字符串

不能修改

常用方法

### byte和rune类型

都属于类型别名



# 今日内容

## 复合数据类型

### 数组Array

```go
// 数组
// 存放元素的容器，必须指定类型和容量
// 数组长度时数组类型的一部分
func main() {
	var a1 [3]bool // [true false true]
	var a2 [4]bool

	fmt.Printf("a1:%T  a2:%T\n", a1, a2)

	// 初始化
	fmt.Println(a1, a2)
	// 1.方式1
	a1 = [3]bool{true, true, true}
	fmt.Println(a1)
	// 2.方式2
	// 根据数组值自动推断数组的长度时多少
	// a10 := [...]int{1, 2, 3, 4, 5, 6}
	// 3.方式3: 根据索引来初始化
	a3 := [5]int{0: 1, 4: 2}
	fmt.Println(a3)

	// 数组的遍历
	var a = [...]string{"北京", "上海", "深圳"}
	// 方法1：for循环遍历
	for i := 0; i < len(a); i++ {
		fmt.Println(a[i])
	}

	// 方法2：for range遍历
	for index, value := range a {
		fmt.Println(index, value)
	}

	// 多维数组
	var a11 [3][2]int
	a11 = [3][2]int{
		{1, 2},
		{3, 4},
		{5, 6},
	}
	fmt.Println(a11)

	// 数组是值类型,修改原来数组的值，新值不会变
	b1 := [3]int{1,2,3}
	b2 := b1
	b2[0] = 100
	fmt.Println(b1, b2)
}
```

### 切片（Slice）

切片就是一个框，框住一块连续的内存。

真正的数据都是保存在底层的数组里的

#### 切片不能直接比较

切片之间是不能比较的，我们不能使用`==`操作符来判断两个切片是否含有全部相等元素。 切片唯一合法的比较操作是和`nil`比较。 一个`nil`值的切片并没有底层数组，一个`nil`值的切片的长度和容量都是0。但是我们不能说一个长度和容量都是0的切片一定是`nil`，例如下面的示例：

```go
var s1 []int         //len(s1)=0;cap(s1)=0;s1==nil
s2 := []int{}        //len(s2)=0;cap(s2)=0;s2!=nil
s3 := make([]int, 0) //len(s3)=0;cap(s3)=0;s3!=nil 
```

所以要判断一个切片是否是空的，要是用`len(s) == 0`来判断，不应该使用`s == nil`来判断。

## VSCODE配置快捷键

```json
// ctil + shift + p
// 搜snippet，配置Go
"pln": {
		"prefix": "pln",
		"body": "fmt.Println($0)",
		"description": "fmt.Println()"
	},
	"plf": {
		"prefix": "plf",
		"body": "fmt.Printf(\"$0\\n\", )",
		"description": "fmt.Printf()"
	}

```

## 指针

Go语言中不存在指针操作，只需记住两个符号：

1. `&`取地址
2. `*`根据地址取值

### make和new的区别

1. make和new都是用来申请内存的
2. new很少用，一般用来给基本数据类型申请内存，string\int\struct，返回的是对应类型的指针
3. make是用来给`slice`、`map`、`chan`申请内存的，make函数返回的是对应的这三个类型本身

## map

map是一种无序的基于`key-value`的数据结构，Go语言中的map是引用类型，必须初始化才能使用。

