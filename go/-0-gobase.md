# go base

## go环境配置

1. 下载 安装包
   - 安装完 运行`go`验证

2. go 代理配置
    - 需要1.13及以上才支持
    - `go env` 查看配置go的环境配置
    - [GOPROXY参考](https://github.com/goproxy/goproxy.cn/blob/master/README.zh-CN.md)
    - 使用命令`go env -w GOPROXY = "https://goproxy.cn"`进行配置

3. 启用goGO111MODULE
    - 依赖管理相关配置
    - `go env -w GO111MODULE=on`

4. goimports
    - 自动格式化代码,增删依赖的包引用
    - `go get -v golang.org/x/tools/cmd/goimports`

5. idea
    - 安装插件 go和 fileWatchers
    - 在fileWatchers 配置 goimports 自动格式化等导入包

## 定义变量

`var 参数名[,参数名1,....] [类型][=值,值,....]`

- 几种通用的定义方式

```go
var (
  u int
  i string
)
var a int
var s string
var b, c int = 1, 2
var d = "1"
var e, f = 1, 2
```

> 类型可以省略 根据赋值推断,设置类型拥有默认值

函数内可以使用
`参数名[,参数名1]:=值[,值,....]`

- 函数内定义的方式

```go
jj := 1
j, k := 1, "xx"
```

## 内建类型

1. `bool`
2. `string`
3. `int`
   - `int8,int16,int34,int64`
      - 规定长度,不规定长度根据系统
   - 加u为无符号整数`uint`
4. `uintptr`
   - 指针
   - 长度根据系统
5. `byte`
   - 长度8
6. `rune`
   - 其他语言的char
   - 长度为32
7. `float32`
   - `float64`
8. `complex64`
   - `complex128`
   - 复数 实部[+-]虚部i

> `byte` `rune` 都为`int`的别名
>> go 只能强制类型转换,没有隐式转换

## 定义常量

`const`

- 定义

```go
const c="1"
const a,b=1,2
const d int=3
const(
   e="1"
   g,h=1,2
)

```

> `const`数值可作为各种类型使用

### 枚举

- 定义

```go
const(
   a=1
   b=2
   c=3
)

// 自增值枚举
const(
   d=iota
   e
   f
)

const(
   d=iota
   _
   f
)
const(
   u=iota*10
   p
   i
)
```

> `iota` 自增值种子 默认`1` 可以用公式 `10*iota`等
>>`_`表示跳过

## 程序控制

- `if`

```go
i:=1
if i>10{
   fmt.println("a")
}else if i>5 {
   fmt.println("b")
}else{
   fmt.println("c")
}

//接收变量的写法

if  _,err:=ioutil.ReadFile("a.txt"); err==nil{
   fmt.Println("s")
}else {
   fmt.Println("e")
}
```

> 下面的写法变量只有在表达式的作用域里有效

- `switch`

```go
a,b,op:=1,2,"+"
var result int
switch op {
case "+":
   result = a + b
case "-":
   result = a - b
default:
   fmt.Println("error")
}
 fmt.Println(result)

// 省略表达式的写法
var result string
s:=1;
switch {
case s > 10:
   result = "a"
case s > 5:
   result = "b"
default:
   result = "c"
}
fmt.Println(result)
```

> 自动`break` 使用`fallthrough`可以避免

for 条件不需要括号

## 函数 定义

func eval (a,b int ) int {}
func eval (a,b int ) (int int){}

## 指针

var aint=2
pa *int =&a
*pa=3
值传递 引用传递
go 只有值传递
使用指针可以变为引用传递

## 数组

- 值类型
var arr [6]int
arr2:=[3]int {1,2,3}
arr2:=[...]int {1,2,3}

遍历
for i,v:=reage arr{
}

slice 切片
arr:=[...]int {0,1,2,3,4,5,6,7}
s=:arr[2:6] 左开右闭
var s[]int 声明slice
make([]int,10,32)
作为array 的一个视图
reSlice 可以后扩展下标,无法向前扩展

01234567
   2345---
         56
slice内部实现
ptr 当前索引
len 当前长度
cap ptr 末尾end

append  超过cap 内部发生拷贝,重新分配一个更大的数组

map -> hash
m :=map[string]string{k:v,k1:v2}
m2:=make(map[string] int) emptyMap
var m3 map[string] int  nil
nil emptyMap 在运算中相同
错误key 会获得zero value

内建类型处理 slice Map function都可以作为key

Struct 类型包含可内建类型key都可以作为Struct

rune
len("")根据字节码来计算
utf8.RuneContInString 字符数量
int32 的别名
strings 包 提供常用api

结构体和方法
不支持继承和多态

type typeName struct {
参数 类型
}
定义
var 参数名 typeName
赋值
typeName{参数名:值}
typeName{值,值}
new(typeName)
func ( name type) print()  ()前置表示接收者

局部变量的地址可以被返回  c++不能

nil 指针也能调用方法

封装 camelCase

首字母 大写表示 public
首字母 小写 表示 private
针对于包 每个是一个包
main包含一个执行入口

结构定义的方法必须放在同一个包内 可以是不同的文件 一个目录一个包

扩展  已有类型 或系统类型 go没有继承

定义别名
type x x

使用组合
包装结构体

使用 embedding 内嵌
 包装结构体 去掉名称  
 相当于包装的语法糖 包装类型当作已有类型使用

依赖管理
 第三方库的管理
 GOPATH,GOVENDOR,go mod

GOPATH
 用户目录\go 包所在地址
 go env
 需要关闭 GO111MODUL  只会去googel拉

 go get -u go.uber.org/zap   不受镜像影响
 vendor 优先拉取项目目录下vendor
拉取库的优先顺序
 vendor ->go root ->GOPATH

 管理工具 glide dep

 go mod

go get -u go.uber.org/zap@版本号

go mod tidy 清楚 go.sum

GOPATH,GOVENDOR 迁移
go build ./...

go mon init  初始化

go 命令统一管理

目录整理

接口

type typeName interface{}

duck typing  描述事物的外部行为而非内部结构
结构化类型系统
接口使用者定义

接口变量自带指针
接口变量使用值传递
指针接收者只能使用指针

>待整理
