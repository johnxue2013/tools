# The Go Programming Language 笔记

## 1.1
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
- src目录 存放源代码(比如：.go .c .h .s等)   按照golang默认约定，go run，go install等命令的当前工作路径（即在此路径下执行上述命令）。
- pkg目录 存放编译时生成的中间文件（比如：.a）　　golang编译包时
- bin目录存放 编译后生成的可执行文件（为了方便，可以把此目录加入到 $PATH 变量中，如果有多个gopath，那么使用${GOPATH//://bin:}/bin添加所有的bin目录

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
```Go
// a traditional “while” loop
for condition {
  // do something
}
```

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
                                      类型声明使得我们可以很方便的给一个特殊类型一个名字。因为struct类型声明通常非常长，所以我们总要给这种struct取一个名字。如
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

```Go
//也可以在一个声明中同时声明一组变量
var i, j,k, int  //int int int
var b, f, s = true, 2.3, "four"
```
## 2.3.1 简短变量声明
语法:
名字 := 表达式
在函数内部使用。

不要和赋值操作=弄混淆，
```Go
// 交换和j的值
i, j = j, i
```

```Go
//命令行参数的使用
package main

import (
	"flag"
	"fmt"
	"strings"
)

//命令行参数的名字 n，n实际类型是一个指针
var n = flag.Bool("n", false, "omit trailing newline")
var sep = flag.String("s", " ", "seplarator")

func main() {

	//使用命令行参数之前先更新每个标志参数对应变量的值
	flag.Parse()
	//对于非标志参数的普通命令行参数可以通过调用flag.Args()
	fmt.Print(strings.Join(flag.Args(), *sep))

	if !*n {
		fmt.Println()
	}
}

```

## 2.3.3 new函数
另一个创建变量的方法是调用内建的new函数，表达式new(T)将创建一个T类型的匿名变量，初始化为T类型的零值，然后返回变量地址，返回的指针类型为*T。

```Go
p := new(int) // p, *int类型，指向匿名的int变量
fmt.Println(*p) // 0
*p = 2 //设置int匿名变量的值为2
fmt.Print(*p) // 2
```

用new创建变量和普通变量声明语句方式创建变量没有什么区别，除了不需要声明一个临时变量的名字外，我们还可以在表达式中使用new(T)

> new并不是一个关键字，可以在定义一个变量名为new

```go
//由于new被定义为int类型的变量名，因此在delta函数内部是无法使用内置的new函数
func delta(old, new int) int {
  return new - old
}
```

## 命名类型

```Go
type 类型名字 底层类型
```

```Go
package main

type Celsius float64    //摄氏温度
type Fahrenheit float64 //华氏温度

const (
	AbsoluteZeroC Celsius = -273.15 //绝对零度
	FreezingC     Celsius = 0       //结冰点温度
	BoilingC     Celsius = 100     // 沸水温度
)

func CToF(c Celsius) Fahrenheit {
	return Fahrenheit(c*9/5 + 32)
}

func FTOC(f Fahrenheit) Celsius {
	return Celsius(f-32) * 5 / 9
}

```

### 包的初始化

对于包级别的变量，如果有初始化表达式则用表达式初始化，还有一些没有初始化表达式的，可以使用一个特殊的init初始化函数来简化初始化工作。每个文件可以包含多个init初始化函数(多个时将按照声明顺序被自动调用,每个包只会被初始化一次)
```Go
func init() {
  /* do something*/
}
```

# 第三章 基础数据类型
Go语言将数据分为四类：
- 基础类型: 数字、字符串和布尔型
- 复合类型: 数组和结构体
- 引用类型: 指针、切片、字典、函数、通道
- 接口类型

### 字符串和Byte切片
四个包对字符串处理尤为重要：bytes、strings、strconv、和unicode包

### 常量
一个常量的声明语句定义了常量的名字，常量值不可以修改。
```Go
const pi = 2014159 //使用math.Pi更合适

//也可以这样批量定义多个常量
const {
	e = 207011245544555444251532112
	pi = 3014159265135313153545450456
}
```

所有常量的运算都可以在编译器完成

因为常量值是在编译期就确定的，因此常量可以使构成类型的一部分。如
```Go
const IPV4Len = 4

//parseIPV4 parses an IPV4 address (d.d.d.d)
func parseIPV4(s string) net.IP {
	var p [IPV4Len]byte
}
```

#### iota 常量生成器
常量声明可以使用iota常量生成器初始化

```Go
//定义一个Weekday命名类型
type Weekday int

const (
	//第一行iota将被置成0，下面每行将依次+1，这种形式在其他语言中叫做枚举
	Sunday Weekday = iota
	Monday
	Tuesday
	Wednesday
	Thursday
	Friday
	Saturday
)
```

> Go中的强制类型转换语法: 需要转到的类型(变量),如  

```Go
const Pi64 float64 math.Pi

var x float32 = float32(pi64)
```  

# 第四章 符合数据类型
主要讨论四中类型：数组、slice、map和结构体

## 4.1 数组

```Go
package main

import "fmt"

type Currency int

const (
	USD Currency = iota
	EUR
	GBP
	RMB
)

func main() {
	var s [3]int //array of 3 integers
	fmt.Println(s[0])

	q := [3]int{1, 2, 3}
	for _, v := range q {
		fmt.Println(v)
	}

	//省略号表示数组长度根据元素个数计算
	p := [...]int{1, 2, 3, 4, 5}
	for _, v := range p {
		fmt.Println(v)
	}

	//指定索引和对应值列表
	symbol := [...]string{USD: "$", EUR: "#", GBP: "@", RMB: "￥"}
	fmt.Println(RMB, symbol[RMB])

	//没有用到的索引可以省略

	//定义一个长度为100的数组，最后一个初始化为-1，其他为0
	r := [...]int{99: -1}
	fmt.Println(r)
}
```

如果一个数组的元素类型是可以相互比较的，那么数组类型也是可以相互比较的，这时候可以直接通过==比较运算符来比较两个数组。只有当两个数组的所有元素都是相等的时候数组才是相等的。不相等比较运算符!=遵循同样的规则。

> 当调用一个函数的时候，函数的每个调用参数将会被赋值给函数内部的参数变量，所以**函数参数变量接收的是一个复制的副本，并不是原始调用的变量**。因为参数传递的机制导致传递大的数组类型将是低效的，并且对数组参数的任何的修改都是发生在复制的数组上，并不能直接修改调用时的数组变量。

## 4.2 Slice 切片
代表变长的序列，一个slice一般写作[]T,其中T代表slice中元素的类型。slice的语法和数组很像，只是没有固定长度而已。

slice由三个部分构成：指针、长度和容量。
slice底层引用一个数组对象
多个slice之间可以共享底层的数据，并且引用的数组部分区间可能重叠。

```Go
package main

import "fmt"

//将数组元素反转
func reverse(s []int) {
	for i, j := 0, len(s)-1; i < j; i, j = i+1, j-1 {
		s[i], s[j] = s[j], s[i]
	}
}

func main() {
	a := []int{0, 1, 2, 3, 4, 5, 6}
	reverse(a)
	fmt.Println(a)
}
```

Slice之间不同于数组因此不能直接使用==比较是否相等

slice唯一合法的比较操作时和nil比较，例如:
```Go
if summer == nill { /*...*/}
```

### append函数

## Map
可以写为map[K]V

内置的make可以创建一个map
```Go
ages := make(map[string]int) //mapping form strings to ints
//也可以使用map字面值的语法创建map
ages := map[string]int {
	"alies": 31,
	"chalie", 34
}
//这相当于
ages := make(map[string]int)
ages["alies"] = 31
ages["chalie"] = 34

//因此创建一个空的map的表达式是map[string]int{}

//使用内置的delete函数可以删除元素
delete(ages, "alies") //如果alies不存在也没关系

//如
ages["bob"] = ages["bob"] + 1 //返回0值后+1
//如果需要真正知道map是否存在某值，可以这样
age, ok := ages["bob"]
if !ok {
	//bob is not a key in this map; age == 0
}

//两者经常结合起来使用
if age, ok := ages["bob"]; !ok {

}

```

```Go
package main

import (
	"fmt"
	"sort"
)

func main() {
	var names []string
	ages := map[string]int{"alice": 21, "bob": 32, "tom": 43}

	for name := range ages {
		names = append(names, name)
	}

	//对map中的key先排序
	sort.Strings(names)

	//顺序遍历
	for _, name := range names {
		fmt.Printf("%s\t%d\n", name, ages[name])
	}
}
```

### 结构体
一种聚合的数据类型
