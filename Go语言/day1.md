# day1

## 课上笔记

markdown格式

# Go语言开发环境搭建

## vscode 安装Go插件失败

设置代理

```shell
go env -w GOPROXY=https://goproxy.io,direct
```

## 编译

```shell
# 在当前文件夹编译
go build xxx.go

# 在其他位置，编译之后生成的文件在当前位置
go buid src以后的文件位置

# 打包
go buid -o xxx.exe
```

## go run

像执行脚本文件一样执行代码

## go install

分成两步：

1. 先编译得到一个可执行文件
2. 将可执行文件拷贝到`GOPATH/bin`目录下，在任何地方都能执行

## 交叉编译

Go支持跨平台编译

可以在windows平台编译其他平台的可执行文件

```shell
SET CGO_ENABLED=0  // 禁用CGO
SET GOOS=linux  // 目标平台是linux
SET GOARCH=amd64  // 目标处理器架构是amd64
```

执行go build

# 变量和常量

## 变量声明

```go
// Go语言的变量声明格式为：
var 变量名 变量类型

var name string
var age int
var isOk bool

// 批量
var (
    a string // ""
    b int    // 0
    c bool   // false
    d float32
)
```

## 字符串

```go
// 字符串
s := "Hello 沙河"
// 单独的字符
c1 := 's'
c2 := '1'
c3 := '沙'
// 字节： 1Byte=8bit
// 1个字符‘A' = 1个字节
// 1个utf8编码的汉字'沙'=一般3个字节
```

