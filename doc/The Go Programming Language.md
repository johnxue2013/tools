# The Go Programming Language 笔记

##1.1
Go是一门编译型语言(静态编译)

```Go
//hello.go
package main

import "fmt"

func main() {
	fmt.Printf("hello, world!")
}
```

```Bash
$ go run hello.go
```
```
# 运行结果
hello, world!
```
go提供的工具都通过一个单独的命令go调用，go命令有一系列的子命令。最简单的一个子命令就是run。该命令编译一个或多个以.go结尾的源文件，链接库文件，并运行最终生成的可执行文件。

`go build`命令可以保存编译结果，该命令生成一个名为hello的可执行的二进制文件(windows下生成的是hello.exe，增加了.exe后缀)，之后你可以随时运行它

```Bash
$ go build hello.go
$ ./hello
```
```
#运行结果
hello, world!
```

**GOPATH**
自己设置的保存go代码的根目录路径，可通过命令行`go env`查看本机go环境配置，可以多有个目录，多个目录使用:分隔，window使用;分隔。当GOPATH有多个值时，默认将`go get`获取的包存放在第一个目录下

$GOPATH目录约定有三个子目录
- src存放源代码(比如：.go .c .h .s等)   按照golang默认约定，go run，go install等命令的当前工作路径（即在此路径下执行上述命令）。
- pkg编译时生成的中间文件（比如：.a）　　golang编译包时
- bin编译后生成的可执行文件（为了方便，可以把此目录加入到 $PATH 变量中，如果有多个gopath，那么使用${GOPATH//://bin:}/bin添加所有的bin目录

**go install**
`go install`会生成可执行文件直接放到bin目录下，当然这是有前提的
你编译的是可执行文件，如果是一个普通的包，会被编译生成到pkg目录下该文件是.a结尾

**go get命令**
go get会做两件事：
- 从远程下载需要用到的包(将放在GOPATH目录中的第一个目录)
- 执行go install

`import`声明必须跟在文件的`package`声明之后。随后，则是组成程序的函数、变量、常量类型的声明语句(分别由关键字`func`,`var`,`const`,`type`定义).

`go get`可以根据要求和

Go语言不需要在语句或者声明的末尾添加分号，除非一行上有多条语句。实际上，编译器会主动把特定的符号后的换行符转换为分号。

## 1.2命令行参数
Go语言只有for循环这一种循环语句。但有多重形式。如下:

```Go
//Go for循环语法
for initialization; condition; post {

}
```
for的三个部分都可以省略,如果省略initialization和post则分号也可以省略
// a traditional “while” loop
for condition {
  // do something
}

```Bash
package main

import (
	"os"
	"fmt"
)

func main()  {
	//定义两个string类型的变量
	var s, sep string
	for i:=1; i < len(os.Args); i++ {
		s += sep + os.Args[i]
		sep = " "
	}
	fmt.Println(s)
}
```

符号**:=**是短变量声明（short variable declaration）的一部分,这是定义一个或多个变量并根据它们的初始值为这些变量赋予适当类型的语句。

for的另一种形式，在某种数据类型的区间(range)上遍历，如字符串切片
```Go
// range 产生一对值。索引以及在该所引处的值
	for _, arg := range os.Args[1:] {
		s += sep + arg
		sep = " "
	}
```
每次循环range产生一对值，这个例子不需要索引，然range语法要求，要处理元素必须处理索引。**Go不允许使用无用的局部变量(local variables)，这样会导致编译错误。**

声明一个变量有好几种方式，下面这些都等价
```Go
//声明一个短变量，只能用在函数内部，而不能用于包变量
s := ""
//此种方式依赖于字符串的默认初始化零值机制，被初始化为""
var s string
//用的比较少，除非同时声明多个变量
var s = ""
//显示的标明变量的类型，当变量与初值类型相同时，类型冗余，但如果两个不同，变量类型就必须了
var s string = ""
```

当import了一个包路径包含有多个单词的package时，比如image/color(image和color两个单词)，通常我们只需要用最后的那个单词表示这个包就可以。

当一个**goroutine**尝试在一个channel上做send或者receive操作时，这个goroutine会阻塞在调用处，直到另一个goroutine往这个channel里写入或接收值。

go中的`switch`默认case之间不需要显示的写break，Go每执行完一个case后自动break，当然要连续的case执行相同的操作则需要显示的写上`fallthrough`

Go中的switch还可以不带任何操作对象（不带任何操作对象默认使用true代替，然后将每个case的表达式和true值进行比较）；
```Go
func Signnum(x int) int {
  switch  {
  case x > 0:
    return +1
  default:
    return 0
  case x < 0:
    return -1
  }
}
```

就像for和if控制语句一样，switch也可以紧跟一个简短的变量声明，一个自增表达式、赋值语句，或者一个函数调用

**命名类型: 类型声明**
类型声明使得我们可以很方便的给一个特殊类型一个名字。因为struct类型声明通常非常长，所以我们总要给这种struct取一个名字。如
```Go
type Point struct {
	x, y int
}

var p Point
```

**指针**
Go语言提供了指针，指针是一种直接存储了变量的内存地址的数据类型(也可理解为指针是一种可见的内存地址)。&操作符可以返回一个变量的内存地址，并且*操作符可以获取指针指向的变量内容

## 2.1 命名
保留字见书P<sub>21</sub>

如果一个名字是在函数内部定义，那么它就只能在函数内部有效，如果在函数外部定义，那么将在当前包的所有文件中都可以访问。名字的开头字母大小写决定了变量在包外的可见性。**如果一个名字是大写字母开头的(补充：必须是在函数外部定义的包级名字)，那么它将是导出的，也就是说可以被外部的包访问，例如fmt包的Printf函数，可以在fmt包外部访问。**包本身的名字一般总是用小写字母

## 2.2 声明
声明语句定义了程序的各种实体对象以及部分或全部的属性。Go语言主要有四种类型的声明语句：var、const、type、func，分别对应变量、常量、类型和函数实体对象的声明。

## 2.3 变量
var声明语句可以创建一个特定类型的变量，然后给变量加一个名字，并且设置变量的初始值。语法如下:
```Go
var 变量名 类型 = 表达式
```
其中"类型""或“=表达式"两个部分可以省略其中的一个。如果省略的类型信息，那么将根据初始化表达式来推导变量的类型信息。如果初始化表达式被省略，那么将用零值初始化改变量。

数值类型对应的零值是0，布尔类型零值是false，字符串类型零值是空字符串，接口欧或引用类型(包括slice、指针、map、chan和函数)变量对应的零值是nil。

因此在Go语言中不存在未初始化的变量。